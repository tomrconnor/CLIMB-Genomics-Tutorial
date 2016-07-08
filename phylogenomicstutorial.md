#CLIMB Phylogenomics Tutorial - Shigella
This tutorial will provide you with an introductory example of the steps required to analyse a set of bacterial genomes using a CLIMB VM.

The approaches used here will enable you to;
####Perform sequence mapping and generate a core genome mapped alignment
####Remove recombination events from the core genome alignment
####Use TempEst to identify if there is temporal signal in the dataset using a root to tip analysis
####Identify the presence of Antimicrobial Resistance genes in a set of bacterial samples
####Identify the (_E. coli_) serotype of the samples being analysed
####Identify the MLST sequence type of the samples being analysed

This tutorial will make use of a set of settings and pipelines that we find useful in our work. This tutorial is not an instruction manual on how to make a high impact publication, nor is it the last word in how to analyse sequence data. The tutorial makes use of a number of scripts/pipeliens that all have default settings. It is up to you to decicde if these meet your requirements based on the sort of questions you want to ask. For instance, when we are examining bacteria that are distantly related (i.e. are from across a species), we can expect that there will be large numbers of SNPs on the internal branches of the tree. We also know expect that the isolates being examined are likely to be distantly related. Therefore, when we perform our analysis we will implicitly not be controlling for false-positive SNP calls (i.e. SNP calls that aren't real, but are an artefact of our SNP filtering). Clearly, if we were interested in an outbreak, for example, then not selecting settings that control for false poisitive SNPs would have the potential to seriously affect our inference from our analyses.

**Bottom line: Most of these tools are easy to run, and produce pretty pictures. Just because the software is easy to use, doesn't mean that you can get away with not understanding what it is doing underneath.** If you want to use these sort of analyses in anger, then we suggest that you put some time into understanding how these packages perform in your hands, to answer your questions.

##Basic sequence mapping
###Reference selection

###Sequence mapping using Snippy

###Generation of core genome alignment using Snippy

###Generation of first look tree using FastTree

##Removal of Recombinations using Gubbins

##Using TempEst to identify the presence of temporal signal

##Screening for AMR genes

###Abricate

###SRST2

##Serotype Identification with SRST2

##MLST Sequence Type identification with SRST2
