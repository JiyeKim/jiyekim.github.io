# ML on Transcriptomic Data for Biomarker Discovery and Tissue-Specific Drug Target Identification

---

## title: "ML on Transcriptomic Data for Biomarker Discovery and Tissue-Specific Drug Target Identification"
date: 2019-02-27 09:00:00 -0400
categories: Bioinformatics GEO

---

[Machine Learning on Human Muscle Transcriptomic Data for Biomarker Discovery and Tissue-Specific Drug Target Identification](https://www.frontiersin.org/articles/10.3389/fgene.2018.00242/full)

다양한 age의 muscle tissue로부터 얻은 transcriptome data를 ML방법으로 분석해서 muscle aging과 가장 연관 있는 genes의 순위를 매김. 한가지 유형의 데이터를 다양한 방법으로 분석해서 각각의 방법으로 부터 나온 결과를 하나로 합칠때의 노하우가 중요해 보임. 여기서는 Borda algorithm이용해서 gene final ranking매김.

ML기법을 통해 prediction model을 만드는 것이 목적이 아니라, prediction에 가장 많이 기여하는 feature를 selection하는 방법 (논문의 feature importance analysis 참고)이 핵심 임

논문을 요약하면, GEO에서 19-89살까지의 transcriptome dataset을 수집해서 young/old 그룹으로 나눈 후 DE 분석과 pathway분석을 수행함. 그리고 ML 기반 age 예측모델을 만듬 (SVM, ElasticNet, RandomForest, DeepFeatureSelection). ML 기반 예측 모델 구성시 사용된 유전자(feature)들의 중요도를 판단할 수 있는 알고리즘을 만들고 그 결과 유전자의 ranking을 매길 수 있게됨. DE분석으로 부터나온 gene ranking (fold chage기반)과 ML모델로 부터 나온 gene ranking을 종합하여 final ranking을 계산함 (Borda Algorithm).

상위 20 개의 final ranked gene을 살펴 본 결과, 5개가 이미 알려진 drug target 이었고 일부는 알려진 therapeutic target 임을 확인 했고, 그러므로 분석 결과로 나온 유전자 중에 novel target이 충분히 있을 수 있음. 

참고) Drug target정보는 DrugBank, R, Reactome pathway database, NCI pathway database로 부터 얻음.  

# Abstract

- present a method for tracking age-related changes of human skeletal muscle.
- DE and pathway analysis were performed to compare signatures of young and old muscle tissue
- developed ML models for predict age values from tissue samples — 0.91 pearson correlation to the actual age values.

# Introduction

- We first applied a state of the art signaling pathway analysis algorithm, iPANDA, to compare transcriptomic signatures of "old" and "young" muscles.
- ElasticNet, SVM, RandomForest, Neural Networks are applied to predict the age of samples based on their transcriptomic signatures.

# Materials and Methods

## Data

- Gene expression profiles source: **GEO, ArrayExpress**
- Number of datasets: 12 series
- Number of samples: 545 samples
- Data for validation: gene expression profiles of skeletal muscles from GTEx

## Supervised Machine Learning Models (from expression to age)

Train and Test Set Design

- Dataset: expression values of 7,682 common genes
- Training/Testing sets: The dataset was split into training and testing sets at an 80/20 ratio. and normalized with "normalized.quantiles" (`preprocessCore` package in R)

Regression Model Implementation

- Adapted 5 ML methods for the age prediction task
    - ElasticNet, Support Vector Machines, k-NN, Random Forest, feed-forward neural networks
    - shallow models using `scikit-learn`, deep models using `Keras`
- We tranined the models with five-fold cross validation to compenstate for overfitting

Model Evaluation

- Pearson correlation coefficient: shows the strength of a linear association b/w predicted and actual age.
- Coefficient of determination: shows the percentage of variance explained by the regression b/w predicted and actual age.
- Mean absolute arre (MAE): demonstrates average  disagreement b/w chronological age and the predicted age.
- epsilon - accuaracy
- → used `multiclass.roc` function from the pROC R package to calculate multiclass area

Feature Importance Analysis

- 

Target Indentification

External Validation

## Signaling Pathway Analysis

# Results

- 545 gene expression profiles of 19-89 age individuals from GEO, ArrayExpress.
- split samples into "old" and "young" groups
- analyze them using differential gene expression analysis and pathway analysis
- train a set of supervised models to predict the age of samples
- finally, rank genes according to their importance for age prediction using **Borda count** over rank values obtained by ElasticNet, Random Forest, Deep Feature Seletion and wrapper alorithms.