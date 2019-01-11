---
title: "GDC Data Portal"
date: 2019-01-08 09:00:00 -0400
categories: Cancer Bioinformatics Database
---

Genomic Data Commons (GDC)는 NCI [^1] 에서 운영하는 연구 프로그램으로, TCGA, TARGET 등 여러 암 연구 프로젝트로 부터 생성된 genomic data를 저장하고 공유할 수 있는 community 이다. GDC에서 관리하는 데이터는 [GDC Data Portal](https://portal.gdc.cancer.gov)을 통해 다운 받을 수 있다.

GDC Data Portal에서 제공하는 데이터는 자유롭게 이용할 수 있는 'open'유형과, 사용 허가가 필요한 'controlled' 유형으로 나뉜다. Raw data나 개인정보에 해당하는 데이터는 controlled 유형이고, 가공된 데이터 (gene expression, masked somatic mutation 등)는 open 유형인듯 보인다 ([이곳](https://gdc.cancer.gov/about-data/data-types-and-file-formats/generated-data-types-and-file-formats)을 통해서 자세한 정보 확인 가능).

아래는 GDC에서 관리하는 데이터 Level과 Type들이다.

## Data Levels
- Level 1: Raw data
- Level 2: Normalized data
- Level 3: Aggregated data
- Level 4: Regions of interest data
- Level 0: No Level

## Data Types

| type | level 1 | level 2 | level 3 |
| --- | --- | --- | --- |
| Exom-seq | Genome alignment | Mutations | Oncogene vs. Tumor suppressor |
| Whole genome-seq | Genome alignment | Mutations + SV | Translocations |
| RNA-seq | Genome alignment | Digital gene expression | Relative RNA levels, Alternative splicing |
| Copy number | Data segmentation | Copy number calls | Gene amplification/deletion |

이렇게 다양한 데이터를 관리 해야하다보니까 Data 구조를 정밀하게 설계할 필요가 있겠다는 생각이 들었다. 아래는 GDC Data 모델이다. 엄청나다.


![GDC data model](https://gdc.cancer.gov/files/public/image/all_nodes_040318.png)
> GDC Data Model (from GDC hompage)

## References
- GDC Documentation: https://docs.gdc.cancer.gov/

## Note
[^1]: National Cancer Institute의 줄임말, National Institutes of Health (NIH)의 한 파트
