---
title: "Drug repositioning in epilepsy reveals novel antiseizure candidates"
date: 2019-01-08 08:00:00 -0400
categories: DrugDiscovery Bioinformatics RNASeq
---

## Abstract
- We tested a low‐cost, **drug‐repositioning strategy to identify candidate epilepsy drugs** that are already FDA‐approved and might be immediately tested in epilepsy patients
- Biopsies of **spiking and nonspiking hippocampal brain tissue from six patients** with unilateral mesial temporal lobe epilepsy were analyzed by **RNA‐Seq**.
- The transcriptome profiles were correlated with L1000 data, a cell lines data treated with FDA-approved drugs.
- Identified **689 DEGs** from RNA-seq (spiking vs. nonspiking biopsies) --> **148 DEGs of which were previously cited** in articles mentioning seizures or epolepsy.
- Matched with Protein-Protein interactions and formed **3 clusters** with associated GO-terms including myelination, protein ubiquitinatio, and neuronal migration.

## Summary

![image](https://wol-prod-cdn.literatumonline.com/cms/attachment/4f20bd1e-cfdd-4dd0-b198-e2c85aab92b2/acn3703-fig-0001-m.jpg)
> Overview of study design

- Spiking and nonspiking tissue from *N*=6 patients.
- DEGs of spiking vs. nonspiking were compared to **CMAP** database.
- identify compounds that **reverse the differential expression signature** (potentially therapeutic activity).
- **4 compounds were tested further** for their antiseizure properties in a zebrafish model of seizure.
- Found 3 compounds that had significant antiseizure properies in vivo.
- The drugs are FDA approved for human use, but **none are specifically approved for seizures or epilepsy**.


## Methods
### Differential expressio analysis
- GLMM as fit to each measure of transcript abundance (Kallisto counts).
- glht() function in the multcomp R Package to compare spiking and nonspiking tissue.
- DEG condition: p-value < 0.05

### Cluster analysis
- DEGs (p < 0.05) were loaded into a **STRING network using the STRINGapp** within Cytoscape.
- minimum edge interaction strength: 0.40
- **Glay clustering algorithm** within the clusterMaker application of Cytoscape --> 3 clusters of DE genes are defined
- Functional enrichment analysis of each clusters of DE genes by **clusterProfiler library** in R
- Highlighted genes from each cluster by citation count

### Drug repositioning
- The coefficient median was calculated
- and, the mean of these values was aggregated across all DE transcripts mapping to a single gene

## Table2의 "Coefficient"?
아래는 DE coefficient를 계산한 방법인데, 매우 헷갈림. 쉽게? 요약하자면, DE coefficient는 각각의 contrast (총 6개)로 부터 구해진 coefficient값의 합산이며, 이것의 의미는 direction of effect가 되는 것임. effect의 방향이 일관될 수록 |coefficient| 값이 커짐
- Gene-level summaries of direction of effect were computed for the DE transcripts described above. First, for
each contrast, the coefficient median was calculated, and
the mean of these values was aggregated across all DE
transcripts mapping to a single gene. This yielded a vector
across all DE genes that indicated the direction of effect
in healthy, nonspiking tissue.

## URL
- https://onlinelibrary.wiley.com/doi/full/10.1002/acn3.703
