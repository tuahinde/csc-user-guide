Metagenomics analysis pipeline in Taito
CSC hosted a hands-on  Metagenomics data analysis course in April 2017. The analysis tools used during the course were installed in virtual machines in the CSC cPouta cloud. As the course installation is not available any more, we have installed the same analysis tools for CSC users in the Taito cluster.

This document is based on the exercises developed by Espen Robertsen and Erik Hjerde for the course. We recommend using the taito-shell.csc.fi through the NoMachine remote desktop for the exercises.

The tools are part of the META-pipe analysis pipeline developed by Willassen et al at ELIXIR-Norway. META-pipe analysis steps can be divided in the following modules as shown below: preprocessing and assembly (blue), classification (yellow), and functional annotation (green). The exercises in this document cover the first two modules, and we are working on making the annotation module available for CSC users as well. The following tools are installed (those in bold are actively maintained): FastQC, Seqtk, Trimmomatic SeqPrep, Megahit, MetaQUAST, predict_16s.py,   LCAClassifier.py,  Krona Tools, MetaPhlAn2, Blast  



1.  Quality control, filtering, assembly & validation
1.1. Setting up the environment and obtaining sample data
1.2. Quality control - FastQC
1.3. Merge paired reads - SeqPrep
1.4. Pre-processing - Trimmomatic
1.5. Assembly - Megahit
1.6. Quality assessment - MetaQUAST

2. Taxonomic profiling
2.1. Taxonomic classification using 16S rRNA sequences
2.2. Taxonomic classification using clade-specific marker genes

3. Comparison & Visualization
3.1. Compare taxonomic profiles of the same sample obtained with 16S rRNA or Clade-specific markers
3.2. Compare taxonomic profiles of two different samples using Krona Compare
3.3. Run MetaQUAST on the assemblies of two different samples

4. Functional annotation
4.1 Accessing META-pipe with the support of CSC
4.2 Performing the analysis
4.3 Related materials
