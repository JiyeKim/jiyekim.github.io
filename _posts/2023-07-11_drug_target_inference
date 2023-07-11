---
title: "Drug Target Inference (약물 타겟 예측) "
date: 2018-01-20 08:00:00 -0400
categories: Bioinformatics drugdevelopment
---

나와 있는 omics data 기반 약물 타겟 예측 모델에 대해 알아보았다. 

TREAP: A New Topological Approach to Drug Target Inference (2020, Muying Wang)
- 약물의 절반이상은 임상 3상에서 실패한다고 함
- 실패 이유는 약물 MoA에 대해 잘 모르는 상태에서 진행 했기 때문
- 약물 MoA에 대한 이해를 위해 개발된 ProTINA, DeMAND는 너무 복잡하다고...
- 게다가 약물처리 후 유전자 발현값이나 네트워크 토폴로지가 약물 타겟과 관련하여 큰 영향을 주는 인자인지도 알 수 없음
- 이 논문에서는 ProTINA의 정확도가 network와 gene expression data에 따라 어떻게 달라지는지 확인했고 network topology가 ProTINA의 예측 정확도에 큰 영향을 준다는 것을 알아냄
- 그리고 가장 최적의 network조건을 알아냇고, 새로운 알고리즘 "TREAP" 을 개발함
- TREAP은 betweenness value와 adjusted p-values를 combine함
- source: https://github.com/ImmuSystems-Lab/TREAP
    - PPI: protein-protein interaction data, package 자체 데이터 or user 데이터 
    - PGI: protein-gene interaction data, 
    - adjusted p-value: DEG 분석 후 나온 pvalue
- testset: DP14, HepG2, MP 데이터로 테스트 각각 0.850, 0.801, 0,806 의 AUROC 값을 가짐
- 평가: L1000 CDS 나 LINCS search tool과 다른 방법: 기존 gene expression에 p-p, p-g도 이용, network topology 기반, 기존 방법은 유사한 drug 찾는 방법이지 target 예측 방법은 아님

참고
- https://brunch.co.kr/@dimension-value/12
