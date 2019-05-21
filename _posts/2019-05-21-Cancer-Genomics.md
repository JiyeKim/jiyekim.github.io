---
title: "Cancer Genomics (암 유전체 분석)"
date: 2019-05-21 09:00:00 -0400
categories: Cancer Bioinformatics 
---


# 암 유전체 분석
## Waterfall plot
환자의 Muation을 유전자별로 한 눈에 보이도록 만든 plot임. 폭포처럼 생겼다해서 waterfall plot.  
R의 "GenVisR" 팩키지로 waterfall plot을 그릴 수 있음
  
참고 코드 ([출처](https://2wordspm.com/2018/07/17/%EC%8A%A4%ED%81%AC%EB%9E%A9-%EC%95%94%EC%9C%A0%EC%A0%84%EC%B2%B4-%EB%B6%84%EC%84%9D-waterfall-plot/))
  
```
library(GenVisR)
setwd(“c:/BTC_R/sample”)

# Load data file
mutationData <- read.csv(“BKM120_mutationdata.csv”)
clinicalData <- read.csv(“BKM120_clinical.csv”)
mutationBurden <- read.csv(“BKM120_mutationburden.csv”)

# Reformat the mutation data for waterfall()
mutationData <- mutationData[,c(“patient”, “gene.name”, “trv.type”, “amino.acid.change”)]
colnames(mutationData) <- c(“sample”, “gene”, “variant_class”, “amino.acid.change”)

# Create a vector to save mutation priority order for plotting
mutation_priority <- as.character(unique(mutationData$variant_class))

# Create an initial plot
waterfall(mutationData, fileType = “Custom”, variant_class_order=mutation_priority)

# Define a mutation hierarchy
mutationHierarchy <- c(“nonsense”, “frame_shift_del”, “frame_shift_ins”, “in_frame_del”, “splice_site_del”, “splice_site”, “missense”, “splice_region”, “rna”)

# define colours for all mutations
mutationColours <- c(“nonsense”=’#4f00A8′, “frame_shift_del”=’#A80100′, “frame_shift_ins”=’#CF5A59′, “in_frame_del”=’#ff9b34′, “splice_site_del”=’#750054′, “splice_site”=’#A80079′, “missense”=’#009933′, “splice_region”=’#ca66ae’, “rna”=’#888811′)

# Find which samples are not in the mutationBurden data frame
# First, let’s look at the sample names in the mutationData and mutationBurden
mutationData$sample
mutationBurden$sample

# Now, determine the non-overlap between these values
sampleVec <- unique(mutationData$sample)
sampleVec[!sampleVec %in% mutationBurden$sample]

# Fix mutationBurden to match mutationData
mutationBurden$sample <- gsub(“^WU(0)+”, “”, mutationBurden$sample)

# Check for non-overlap again
sampleVec[!sampleVec %in% mutationBurden$sample]
# reformat clinical data to long format
library(reshape2)
clinicalData_2 <- clinicalData[,c(1,2,3,5)]
colnames(clinicalData_2) <- c(“sample”, “Months on Study”, “Best Response”, “Treatment Setting”)
clinicalData_2 <- melt(data=clinicalData_2, id.vars=c(“sample”))

# find which samples are not in the mutationBurden data frame
sampleVec <- unique(mutationData$sample)
sampleVec[!sampleVec %in% clinicalData$sample]

# fix mutationBurden to match mutationData
clinicalData_2$sample <- gsub(“^WU(0)+”, “”, clinicalData_2$sample)

# create the waterfall plot
waterfall(mutationData, fileType = “Custom”, variant_class_order=mutationHierarchy, mainPalette=mutationColours, mutBurden=mutationBurden, clinData=clinicalData_2, clinLegCol=3, clinVarCol=c(‘0-6’=’#ccbadc’, ‘6.1-12’=’#9975b9’, ‘12.1+’=’#663096’, ‘Partial Response’=’#c2ed67’, ‘Progressive Disease’=’#E63A27’, ‘Stable Disease’=’#e69127’, ‘1’=’#90ddee’, ‘2’=’#649aa6′, ‘3+’=’#486e77’), clinVarOrder=c(‘1’, ‘2’, ‘3+’, ‘Partial Response’, ‘Stable Disease’, ‘Progressive Disease’, ‘0-6’, ‘6.1-12’, ‘12.1+’), section_heights=c(1, 5, 1), mainLabelCol=”amino.acid.change”, mainLabelSize = 3)
# Create a sample ordering
sample_ordering <- c(“19”, “5”, “31”, “22”, “12”, “2”, “32”, “8”, “28”, “18”, “4”, “24”, “23”, “17”, “11”, “14”)

# Create a gene ordering
gene_ordering <- c(“CDH1”, “MALAT1”, “RUNX1”, “NCOR1”, “GATA3”, “FOXA1”, “ESR1”, “CBFB”, “TBX3”, “TAB1”, “MED12”, “XBP1”, “TP53”, “RB1CC1”, “BRCA2”, “ATM”, “SMARCD1”, “MLL3”, “MLL2”, “ARID1A”, “FBXW7”, “CAV1”, “MAP3K1”, “MAP2K4”, “NOTCH4”, “PDGFRA”, “ERBB3”, “ERBB2”, “RELN”, “MAGI3”, “MTOR”, “AKT2”, “AKT1”, “PTEN”, “PIK3CA”)

# Create a gene ordering
waterfall(mutationData, fileType = “Custom”, variant_class_order=mutationHierarchy, mainPalette=mutationColours, mutBurden=mutationBurden, clinData=clinicalData_2, clinLegCol=3, clinVarCol=c(‘0-6’=’#ccbadc’, ‘6.1-12’=’#9975b9’, ‘12.1+’=’#663096’, ‘Partial Response’=’#c2ed67’, ‘Progressive Disease’=’#E63A27’, ‘Stable Disease’=’#e69127’, ‘1’=’#90ddee’, ‘2’=’#649aa6′, ‘3+’=’#486e77’), clinVarOrder=c(‘1’, ‘2’, ‘3+’, ‘Partial Response’, ‘Stable Disease’, ‘Progressive Disease’, ‘0-6’, ‘6.1-12’, ‘12.1+’), section_heights=c(1, 5, 1), mainLabelCol=”amino.acid.change”, mainLabelSize=3, sampOrder=sample_ordering, geneOrder=gene_ordering)
```
