---
title: "Drug Target Inference (약물 타겟 예측)"
date: 2023-07-11 08:00:00 -0400
categories: Bioinformatics
---
   
# Drug Target Inference (약물 타겟 예측)

omics data 기반 약물 타겟 예측 관련하여 나와있는 논문을 살펴보았다.

## TREAP: A New Topological Approach to Drug Target Inference (2020, Muying Wang)

- 약물의 절반이상은 임상 3상에서 실패한다고 함, 실패 이유는 약물 MoA에 대해 잘 모르는 상태에서 진행 했기 때문인데 기존에 개발된 툴인 ProTINA, DeMAND는 너무 복잡하다고...
- 게다가 약물처리 후 유전자 발현값이나 네트워크 토폴로지가 약물 타겟과 관련하여 큰 영향을 주는 인자인지도 알 수 없음
- 이 논문에서 개발한 TREAP은 topology 기반으로 target을 예측하는 알고리즘인데, 기존에 나와있는 ProTINA에서 사용하는 network data최적화 한것
- TREAP은 betweenness value와 adjusted p-values를 combine하는 방법을 사용함
- source: https://github.com/ImmuSystems-Lab/TREAP
- input 으로 사용하는 데이터
    - PPI: protein-protein interaction data, package 자체 데이터 or user 데이터 
    - PGI: protein-gene interaction data, 
    - adjusted p-value: DEG 분석 후 나온 pvalue
- output: 각 유전자 별 score
- validation test: DP14, HepG2, MP 데이터로 테스트 각각 0.850, 0.801, 0,806 의 AUROC 값을 가짐
- 평가
  - target을 예측하는 방법은 크게 1) deg 기반, 2) deg + topology기반으로 나눌 수 있고 논문의 Figure5와 같이 2번 방법이 1번보다 대체로 높은 AUROC를 보임
  - deg 값만 가지고 target 예측하는 경우는 cell 종류나 화합물 특성에 따라 정확도 편차가 큰데, 여기에 고정되어있는 protein-protein, protein-gene interacrtion정보를 combine해 줌으로서 정확도가 높아지고 편차가 줄어드는 것을 확인
  - topology기반 방법은 proTINA와 Demend가 유명한 듯

## 참고
- TREAP: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7732766/
- 
