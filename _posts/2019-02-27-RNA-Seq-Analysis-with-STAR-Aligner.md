# RNA-Seq Analysis with STAR Aligner

RNA-Seq mapping에 사용되는 STAR aligner는 SJ(splicing junction)을 다른 tool보다 정밀하게 인식한다는 장점이 있음. 두 번의 mapping을 거치는 2-pass mapping방법을 이용하면 좀더 정확하게 SJ를 인식하여 mapping시킬 수 있음.

## quantMode option

STAR에서 제공하는 여러 옵션들이 있는데, 그 중 `—quantMode` 는 read를 mapping시킬때 genomic 
 level로 볼것인지 transcript level로 볼 것 인지 결정하는 옵션임.

default 값으로 분석한 결과가 다음과 같다면,

    D00307:562:CB0YYANXX:2:2202:4795:28502  99      1       172410867       255     100M    =       172410916       145     ATTTAACTGAGGAACCTGGACAA
    GTCTTCCTTGATTTCAGCTTCATCCCAAGGCCCATGAATGTTTTCTTTAAAAAGCTGCAAGCGAATGAGGTAGAATG   /<<<<BF<FBFFFFFFF<FFFFF/<BF<F/FFF<F//<BFFFFBFFF<BB/FFF/F/<FFFFF
    FB<FBFBB/BB/<//<B///<FBFF/F/<B/B/<<FF   NH:i:1  HI:i:1  AS:i:194        nM:i:0
    D00307:562:CB0YYANXX:2:2202:4795:28502  147     1       172410916       255     96M2S   =       172410867       -145    AAGGCCCATGAATGTTTTCTTTA
    AAAAGCTGCAAGCGAATGAGGTAGAATGGACACAGACATGAGATAGACATCAGCAGAAGGGCAAAGAGTACGGAT     FBFBFBFFBFF/</FFFF<BBFFFFF<FFFFFFFFBFFFBFFFFBFFFFFFFFFF<FFFFFFF
    FFFFFFFFFBFFFFFFBFFFFFFFFFFFBBBBBBF     NH:i:1  HI:i:1  AS:i:194        nM:i:0

`—quantMode TranscriptomeSAM` 으로 분석한 결과는 다음과 같음. 3번째 컬럼이 transcript id로 바뀐 것을 볼 수 있음. 이 옵션을 사용하면 `*.toTranscriptome.out.bam` 이라는 파일이 생성됨.

    D00307:553:CBDDHANXX:3:1108:15699:35561 163     ENST00000598182 121     3       100M    =       281     260     GGGAAAAAGCTGCCACGCACCTTCTGCCAGC
    TGATCCTGGACCCCATCTTCAAGGTGTTTGATGCGATCATGAATTTCAAGAAAGAGGAGACAGCAANAC   /BBBB/BFFFFFFFFFBF<FFFFBFFFFFFFFFFFFFFFFFF<FFFFFFFFF</BBBFFFBFFFF<F<FFF
    FFFF<BFF<FFFFF<FFFFFFFFFFF#7<   NH:i:2  HI:i:1
    D00307:553:CBDDHANXX:3:1108:15699:35561 83      ENST00000598182 281     3       100M    =       121     -260    TGCTGAAGGCTGTGATGCGCCGCTGGNTGCC
    TGCCGGAGACGCCTTGTTGCAGATGATCACCATCCACCTGCCCTCCCCTGTGACGGCCCAGAANTACCG   FFFFFFFFFFFFFFFFFFFFFFFBF<#FFFFFFBFFFFBFFFFFFFFFFFFFFFFFFFBFFFFFFFFFFFF
    FFFFFFBFFFFFFFFFFFFFFBB#BBBBB   NH:i:2  HI:i

RSEM과 같이 transcript quantification을 분석하는 tool은 `— quantMode TranscriptomeSAM` 으로 분석한 bam파일만 input으로 받으니 꼭 요 옵션으로 mapping해 주어야함.

    rsem-calculate-expression \
    	...
    	--bam Aligned.toTranscriptome.out.bam 

반면, RNA-Seq 데이터를 가지고 Variant/Mutation calling 을 하고자 하는 경우에는 default mode로 돌려야함.

## 2-pass mapping

STAR로 2-pass mapping을 수행할 때 흔히 가지게 되는 궁금증 중 하나는 "**sample이 여러개 일때, 생성되는 여러개의 SJ.out.tab파일을 하나로 합쳐서 index를 만드냐, 아니면 각각 index를 만드냐?**" 이다.

하나로 합쳐진 "global" sj.out.tab이나 sample별 "local" sj.out.tab파일이나 둘 다 맞다는 것이 여러 논의로 부터 나오는 결론이다.

- global approach: 여러 sample로 부터 나오는 sj파일을 하나로 합쳐서 global한 index를 만드는 방법. 이론적으로는 이 방법이 더 정확하다고 할 수 있지만, 실제로는 꼭 그렇지만은 않다고... 와일드카드를 이용할 수 있음. `—sjdbFileChrStartEnd *.SJ.out.tab`
- local approach: 여러 sample로 부터 나오는 sj파일을 각각 따로 사용하는 방법. 이 경우에는 `—twopassMode` 옵션을 이용하면 genome을 re-generate하지 않고 자동으로 분석할 수 있다.