---
title: "Alternative Splicing Analysis (MATS이용)"
date: 2021-11-16 09:00:00 -0400
categories: Bioinformatics RNAseq Transcriptome
---

# Alternative Splicing 분석

## 개요
두 샘플 그룹 (약믈 처리 전,후)간 Alternative Splicing Event 변화 확인분석. 최종 목표는 약물에 sensitive한 model1,2와 resistant한 model3간 AS 패턴의 차이를 확인 하는 것이다.

- model1: drug sensitive
- model2: drug sensitive
- model3: drug resistant

## 분석 실행 (MATS 이용)
MATs tool을 이용하여 두 그룹의 PSI값과 dPSI값을 계산한다. AS 유형 5가지에 대한 결과가 각각 생성된다.
분석 시 input으로 필요한 파일은 다음과 같다.

- 두 그룹의 bamfile위치가 적혀있는 텍스트 파일 (나는 control.txt, treatment.txt로 작성함)
- reference genome의 GTF파일 (나는 hg19.gtf 사용)

```sh
pyenv activate ana4

# bamfile sorting and indexing (모든 bamfile 에 대해 수행)
samtools sort trt1.bam -o trt1.sorted.bam
samtools index trt1.sorted.bam

# treatment.txt, control.txt 생성
cat treatment.txt
trt1.sorted.bam,trt2.sorted.bam,trt3.sorted.bam

cat control.txt
ctr1.sorted.bam,ctr2.sorted.bam,ctr3.sorted.bam

# run rmats
rmats.py \
    --b1 treatment.txt \
    --b2 control.txt \
    --gtf ~/data/Reference/Ebiogen/UCSC_hg19/hg19.gtf \
    -t paired --readLength 100 --nthread 4 \
    --od ./output \
    -t paired
python ../../summary.py ./output \
    --use-raw-p --p-cutoff 0.05 \
    --summary-path summary.txt

```

## 분석 결과 해석
결과는 AS 5가지 유형별로 나오게 된다.
* AS 유형: Exon skipping (SE), Exon MX (MXE), Retained intron (RI), Alternative 5' or 3' Splice Site (S5SS, S3SS)

5가지 유형별로 각각 아래와 같은 output이 생성된다.

1. `[AStype].MATS.JCEC.txt`
1. `[AStype].MATS.JC.txt`   ### 요 파일로 report생성
1. `fromGTF.[AStype].txt`
1. `fromGTF.novelEvents.[AStype].txt`
1. `JCEC.row.input.[AStype].txt`
1. `JS.row.input.[AStype].txt`

요 파일 중에 `[AStype].MATS.JC.txt` 파일가지고 결과 확인하면 되는데, 결과 컬럼 구성을 AS type마다 조금씩 다르지만 아래와 같이 정리 할 수 있음. exon 위치 별로 맵핑된 read count를 통해, inclusion/skipping된 read count 수를 group간 비교해서 전보다 splicing이 일어난 exon이 많아 졌는지 적어졌는지 파악할 수 있다.

| **column** | **의미** | **비고** |
|---|---|---|
|**ID**|rMATS event id||
|**GeneID**|ASE 부위의 유전자||
|**geneSymbol**|ASE 부위의 유전자||
|**chr**|ASE 부위의 chromosome||
|**strand**|ASE 부위의 strand||
|(Event specific columns)*|Exon 위치|AS type별로 column name이 다름|
|**ID**|rMATS event id|첫번째 컬럼과 동일|
|**IJC_SAMPLE_1**|Inclusion counts for Group1|여러 sample일 경우 ','로 구분|
|**SJC_SAMPLE_1**|Skipping counts for Group1|여러 sample일 경우 ','로 구분|
|**IJC_SAMPLE_2**|Inclusion counts for Group2|여러 sample일 경우 ','로 구분|
|**SJC_SAMPLE_2**|Skipping counts for Group2|여러 sample일 경우 ','로 구분|
|**IncFormLen**|Length of inclusion form, used for normalization||
|**SkipFormLen**|Length of skipping form, used for normalization|||
|**PValue**|dPSI값에 대한 p-value||
|**FDR**|PValue에 대한 False Discovery Rate||
|**IncLevel1 (PSI)**|Group1 sample들 PSI값|normalized count를 %로 바꾼 것|
|**IncLevel2 (PSI)**|Group2 sample들 PSI값|normalized count를 %로 바꾼 것|
|**IncLevelDifference (dPSI)**|그룹간 PSI 차이(dPSI)|IncLevel1 평균 - IncLevel2 평균|


## 분석 결과 정리
이제 분석결과를 자세히 살펴볼 차레인데 그전에 분석결과를 보기 쉽도록 살짝 가공하려고 한다.
  
참고로 나는 분석을 3가지 모델 (model1,2,3)에 대해서 했기 때문에 한 경로 아래에 모델 별로 디렉토리를 만들어 rMATS분석결과가 위치하도록 구성해 놓았다.
```sh
├── model1
│   ├── A3SS.MATS.JCEC.txt
│   ├── A3SS.MATS.JC.txt
│   ├── A5SS.MATS.JCEC.txt
           ...
├── model2
│   ├── A3SS.MATS.JCEC.txt
│   ├── A3SS.MATS.JC.txt
│   ├── A5SS.MATS.JCEC.txt
│   ├── A5SS.MATS.JC.txt
          ... 
├── model3
│   ├── A3SS.MATS.JCEC.txt
│   ├── A3SS.MATS.JC.txt
│   ├── A5SS.MATS.JCEC.txt
│   ├── A5SS.MATS.JC.txt
         ...
```
### maseR 이용하여 결과 확인

R의 maseR 패키지를 이용하면 MATS분석결과를 쉽게 정리 할 수 있다. 아래와 갔이 결과가 저장된 경로를 넣어주면 maser에서 자동으로 각 AS유형별 결과 파일을 인식하여 가져온다.

```r
library(maser)
library(rtracklayer)

path = "/path/to/model1"
res = maser(path, c("Treatment", "Control"), ftype="JC")
res

# output (요렇게 나온다)
A Maser object with 30155 splicing events.

Samples description: 
Label=Treatment     n=3 replicates
Label=Control     n=3 replicates

Splicing events: 
A3SS.......... 2201 events
A5SS.......... 1780 events
SE.......... 22626 events
RI.......... 194 events
MXE.......... 3354 events

# 실제 목록을 보고싶을 땐 summary 를 쓰면 된다.
summary(res)

# `type=` 파라메터를 사용하면 원하는 유형만 볼 수 있다.
summary(res, type="RI))
```


maser로 결과를 불러온 후에는 유의한 ASE만 필터링 하면 된다. 보통 아래와 같은 조건으로 필터링한다.

- coverage > 5: 각 AS부위에 매핑된 read count 수 평균 5이상인 것
- fdr < 0.05 : FDR이 0.05 미만인 것
- dPSI > 0.1: |dPSI| 값이 1 이상인 것

```r
# coverage > 5
res.cov5 <- filterByCoverage(res1, avg_reads=5)
# coverage > 5 & fdr < 0.05
res.cov5.fdr <- topEvents(res1.cov5, fdr=0.05)
# coverage > 5 & fdr < 0.05 & dpsi > 0.1
res.cov5.fdr.psi <- topEvents(res1.cov5, fdr=0.05, deltaPSI=0.1)
```

PCA를 통해 필터링 전, 후의 샘플 분포 확인해보면 필터링 해서 유의한 AS만 골라낸 샘플들이 두 그룹간 구분되어 보이는 것을 확인 할 수 있다.

```r
pca(res)
pca(res.cov5)
pca(res.cov5.fdr)
pca(res.cov5.fdr.psi)
```

샘플별 PSI값 분포을 boxplot으로도 확인 할 수 있다. maser의 `boxplot()`기능을 사용하면 된다.
Treatment, Control 두 그룹의 PSI분포나, 샘플 별 PSI분포를 확인 할 수 있다. 나는 model1,2,3을 maseR을 통해 `res1`, `res2`, `res3`으로 불러왔다.

```r
# PSI 분포 (sample별)
boxplot(PSI(res1.cov, "RI"), las=2, main="boxplot of PSI")
boxplot(PSI(res2.cov, "RI"), las=2, main="boxplot of PSI")
boxplot(PSI(res3.cov, "RI"), las=2, main="boxplot of PSI")
## 또는 
boxplot_PSI_levels(res1.cov,"RI")
boxplot_PSI_levels(res2.cov,"RI")
boxplot_PSI_levels(res3.cov,"RI")

# dPSI 분포 (모델별)
name1 <- "model1"
name2 <- "model2"
name3 <- "model3"
boxplot(summary(res1.cov.fdr, "RI")$IncLevelDifference,
        summary(res2.cov.fdr, "RI")$IncLevelDifference,
        summary(res3.cov.fdr, "RI")$IncLevelDifference,
        
        summary(res1.cov.fdr, "A3SS")$IncLevelDifference,
        summary(res2.cov.fdr, "A3SS")$IncLevelDifference,
        summary(res3.cov.fdr, "A3SS")$IncLevelDifference,
        
        summary(res1.cov.fdr, "A5SS")$IncLevelDifference,
        summary(res2.cov.fdr, "A5SS")$IncLevelDifference,
        summary(res3.cov.fdr, "A5SS")$IncLevelDifference,
        
        summary(res1.cov.fdr, "SE")$IncLevelDifference,
        summary(res2.cov.fdr, "SE")$IncLevelDifference,
        summary(res3.cov.fdr, "SE")$IncLevelDifference,
        
        summary(res1.cov.fdr, "MXE")$IncLevelDifference,
        summary(res2.cov.fdr, "MXE")$IncLevelDifference,
        summary(res3.cov.fdr, "MXE")$IncLevelDifference,
        main="boxplot of dPSI", ylim=c(-0.5,0.5), las=2,
        names=c(
                name1, name2, name3,
                name1, name2, name3,
                name1, name2, name3,
                name1, name2, name3,
                name1, name2, name3
               )
       )
```

### 결과 정리 파일 생성
maseR를 이용하여 샘플의 PSI, count, dPSI, pvalue, FDR값을 계산하고 tsv파일로 내보내기 한다.  

```r
write_summary <- function(type) {
    write.table(
        cbind(summary(res1, type),
              counts(res1, type),
              PSI(res1, type)),
        file = sprintf("./model1_%s_maseR.tsv", type),
        sep = "\t")
    
    write.table(
      cbind(summary(res2, type),
            counts(res2, type),
            PSI(res2, type)),
      file = sprintf("./model2_%s_maseR.tsv", type),
      sep = "\t")
    
    write.table(
      cbind(summary(res3, type),
            counts(res3, type),
            PSI(res3, type)),
      file = sprintf("./model3_%s_maseR.tsv", type),
      sep = "\t")

}

write_summary("RI")
write_summary("SE")
write_summary("A5SS")
write_summary("A3SS")
write_summary("MXE")

## model1,2,3_{AStype}_maseR.tsv 생성
```

## Reference
- MATS official: http://rnaseq-mats.sourceforge.net/
- MATS github: https://github.com/Xinglab/rmats-turbo/tree/v4.1.1

