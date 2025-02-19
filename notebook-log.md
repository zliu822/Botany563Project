# Botany 563 Project Spring 2025
My final project will be using and analyzing the sequencing data 
from three unlinked nuclear genes and one mitochondrial region of selected spieces of Murinae family described in the paper.

PaperLink: https://www.sciencedirect.com/science/article/pii/S1055790305001417#app2
GenBank Link: https://blast.ncbi.nlm.nih.gov/Blast.cgi#alnHdr_67944442



# QC of sequenced genome data with Bootstrap and Bayesian Analysis for Robustness
1. Nonparametric bootstrapping (Felsenstein, 1985) was performed on all data partitions: 200 replicates for ML and 500 replicates for MP.
2. Bootstrap analyses for MP and ML used 10 random-sequence addition replicates per bootstrap replicate.
3. Likelihood bootstrap analyses were limited to 2–4000 rearrangements for individual nuclear genes (10,000 for AP5) and combined-data set.
4. 20 random-sequence addition replicates and 40,000 rearrangements (per each sequence-addition replicate) for the mitochondrial data set.



# Multiple Sequence Alignment Method Chosen
Clustal W

ClustalW is a widely used multiple sequence alignment algorithm that creates alignments progressively. It starts by computing pairwise alignments to estimate evolutionary distances, constructs a guide tree from those distances, and then aligns sequences step-by-step according to that tree. The method uses biologically informed gap penalties and sequence weights to improve alignment quality. I choose this method because my sequences are all from the same family of Murinae species, which makes the ClustalW an viable option.

Assumptions:
1. Sequences being compared share a common evolutionary history.
2. Pairwise sequence alignments provide a good approximation of actual evolutionary distances.
3. More closely related sequences should be aligned first, with progressively more distantly related sequences added in later steps.

Limitations:
1. Errors made early in the progressive alignment process cannot be corrected later.
2. It struggles with very divergent sequences, as the guide tree might not accurately reflect true evolutionary relationships.
3. It may not handle large data sets or highly diverse sequence sets as well as more recent algorithms.
