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

# Steps of ClustalW
1. Downloaded ClustalW using:
   conda install -c bioconda clustalw
2. Find the accession number of the sequences from appendix of my paper:     
   https://www.sciencedirect.com/science/article/pii/S1055790305001417
3. Search the accession number and download the FASTA file from:
   https://blast.ncbi.nlm.nih.gov/Blast.cgi#alnHdr_67944442
4. Move the FASTA file to the ClustalW file using command:
   mv /Users/alanliu/Downloads/DOWNLOADED_FILE /Users/alanliu/miniconda3/pkgs/clustalw-2.1-h4675bf2_12/bin
5. Find the commands on:
   http://www.clustal.org/download/clustalw_help.txt
6. Run the alignment using command:
   clustalw2 -ALIGN -INFILE=DOWNLOADED_FILE -OUTFILE=OUTPUT_FILE_NAME -OUTPUT=FASTA
7. Store the multiple sequence alignment output file in .aln format later for inference.



# Inference Method 1 Chosen
IQ-Tree
IQ-Tree is a Maximum Likelihood based phylogenetic inference tool. It’s fast, accurate, and designed for both small and large datasets like DNA, RNA, or protein. It includes model selection, bootstrap analysis, and approximate support values such as SH-aLRT, aBayes, and ultrafast bootstrap. I choose this method because Murinae species include diverse genera like Mus, Rattus, Apodemus etc., which often show rapid radiations and closely related lineages. IQ-Tree uses Maximum Likelihood, which is ideal for resolving these fine-scale relationships with statistical rigor.

Assumptions:
1. Assumes that the multiple sequence alignment is correct. Each column must represent homologous sites, that is, descended from a common ancestor
2. Assumes that each site evolves independently from others.
3. Assumes there is a single true tree topology that explains all sites in the alignment.

Limitations:
1. ML methods estimate one best tree, but do not provide a full distribution over trees like Bayesian methods do.
2. It can be overconfident if model fit is poor or alignment is low quality.
3. It is less informative on posterior probability of clades.
4. There is no time estimates.

# Steps of Running IQ-tree
1. Download and install IQ tree on the website:
   https://iqtree.github.io/
2. Run IQ-tree using following command to get initial output tree:     
   /Users/alanliu/Desktop/Phylo\ 563\ iqtree/iqtree3 -s /Users/alanliu/Desktop/Phylo\ 563\ iqtree/clustalw.aln -m MFP
3. Observe the output, look at which is the best-fit model based on Bayesian Information Criterion.
4. Run IQ-tree again using following command to run in the best-fit model and add bootstrap values to reflect how often a clade appears in resampled dataset.
   /Users/alanliu/Desktop/Phylo\ 563\ iqtree/iqtree3 -s /Users/alanliu/Desktop/Phylo\ 563\ iqtree/Phylo563fasta.fasta -m [bestfit_model] -bb [bootstrap values chosen]
5. Find the output tree file in .tree ending and save for later



# Inference Method 2 Chosen:
BEAST
BEAST (Bayesian Evolutionary Analysis Sampling Trees) Co-estimation method is a powerful tool used by evolutionary biologists to build phylogenetic trees and estimate when different species or lineages split from a common ancestor. What sets BEAST apart is that it doesn't just give one best tree. Instead, it uses a Bayesian approach to sample from a range of possible trees, giving both an estimate and a measure of confidence in that estimate. It’s especially useful when incorporating time into the tree is needed, such as figuring out when two species diverged, using molecular clocks and fossil data. I choose BEAST for my second inference method because it allows me to build a time-calibrated tree and contains posterior distribution of trees, which means it captures the uncertainty in the phylogenetic inference. That’s crucial in a group like Murinae, where closely related species may have short internal branches and conflicting signal between nuclear and mitochondrial DNA. Rather than forcing a single best tree, BEAST lets me account for those uncertainties directly and provides posterior probabilities for each clade, helping me judge the confidence in the results.

Assumptions:
1. Input sequences are correctly aligned and homologous
2. Each site evolves independently.
3. Assumes no recombination within the alignment

Limitations:
1. BEAST is much slower than ML methods like IQ-TREE, especially for large datasets or complex models
2. Poorly chosen priors can bias results; Bayesian inference is sensitive to prior assumptions
3. Must specify substitution, clock, and tree models manually (no built-in model testing like IQ-TREE).

# Steps of Running BEAST
1. Download and install BEAST on the website:
   https://beast.community/install_on_mac
2. Convert the multiple sequence alignment output file in .aln format to .nexus format in this website:
   https://sequenceconversion.bugaco.com/converter/biology/sequences/fasta_to_nexus.php
3. Open BEAUti app in the package, import the converted .nexus file, this will parse the aligned sequences and make it ready to use in BEAST. The output file will be in .xml format.
4. Run BEAST by importing the output .xml file from BEAUti. The output file will be in .tree format.
5. Find the total sample ran by using the Tracer by downloading and installing on this website:
   https://github.com/beast-dev/tracer/releases/tag/v1.7.2
   Tracer helps you determine whether your BEAST analysis ran long enough and sampled the parameter space effectively.
7. Run TreeAnotator using by importing the output .tree file and specify the burn-in values (10% of the sample ran). The burn-in values removes the early, unreliable part of the Markov Chain.
8. Find the output tree file in that you named and save for later.



# Visualize the output phylogenetic tree
FigTree
I choose this program becasue the ape R package cannot be installed on my laptop no matter what I do. The FigTree natively supports posterior probabilities and bootstrap values, which are important in comparing the quality of phylogenetic tree because they measure statistical support for the clades in a phylogenetic tree. They tell me how confident I can be that a particular grouping of taxa is correct, based on my data and inference method.

# Steps of Running FigTree
1. Download and install FigTree on website:
   https://github.com/rambaut/figtree/releases
2. Import the output file ending in .tre from IQ-Tree and the output file from TreeAnotator
3. Label each taxa
4. Check the Node Label box, choose to display "label" from IQ-tree and choose to display "posterior" from BEAST
5. Compare the result and analyze.
