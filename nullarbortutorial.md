This tutorial will provide you with an introductory example of the steps required to analyse a set of bacterial genomes the GVL.

The approach outlined here will enable you to;
* Perform sequence mapping and generate a core genome mapped alignment
* Identify the MLST sequence types in your dataset
* Perform a limited QC of dataset
* Identify the presence of Antimicrobial Resistance genes in a set of bacterial samples
* Identify the MLST sequence type of the samples being analysed

This tutorial also covers using a subset of the data analysed to examine individual lineages.

This tutorial will make use of a pipeline that we find useful in our work. This tutorial is not a playbook on how to make a high impact publication, nor is it the last word in how to analyse sequence data. The tutorial makes use of a number of software that are generally used with their default settings. It is up to you to decide if these meet your requirements based on the sort of questions you want to ask. For instance, when we are examining bacteria that are distantly related (i.e. are from across a species), we can expect that there will be large numbers of SNPs called that will fall on the internal branches of the tree. Therefore, when we perform our analysis we will implicitly not be controlling for false-positive SNP calls (i.e. SNP calls that aren't real, but are an artefact of our SNP filtering). Clearly, if we were interested in an outbreak, for example, then not selecting settings that control for false positive SNPs would have the potential to seriously affect our inference from our analyses.

Bottom line: Most of these tools are easy to run, and produce pretty pictures. Just because the software is easy to use, doesn't mean that you can get away with not understanding what it is doing underneath.

If you want to use these sort of analyses in anger, then we suggest that you put some time into understanding how these packages perform in your hands, to answer your questions.

Nullarbor
This tutorial makes use of a standard GVL image, and uses a pipeline that combines sequence mapping using Snippy and a set of useful other tools that are of value in Public Health and for initial analysis of samples in any lab. The pipeline is called Nullarbor, and is developed by Torsten Seeman. The software and documentation are available at;

https://github.com/tseemann/nullarbor67

Nullarbor is quite complex and can sometimes break itself. If this happens, check the page above, as the bug may have been seen before, and there may be a solution.

Before you start, create a directory to work in;

`mkdir shigs`

and then move into this directory

`cd shigs`

# Reference selection

We are going to compare a set of genomes using a mapping based approach. Initially, therefore, you need to pick a reference genome to map your sequence reads against. There are a few ways to do this, but one easy way is to start with the EBI bacterial genomes page;

http://www.ebi.ac.uk/genomes/bacteria.html38
This page has published genomes for many organisms, most of which are of decent quality and are contiguated. Search in the page for "flexneri" and you will see that there are 4 reference strains. We want to use 2457T as our reference strain here. This strain is selected for simplicity, as we will use the results generated in two ways. We will look across Shigella, and we will also look within one lineage of Shigella - the lineage to which 2457T belongs.

To load this reference onto your VM do the following. First, on the ebi webpage the row with the reference strain we will use looks something like this;

`2714	Shigella flexneri 2a str. 2457T	4,599,354	AE014073	AE014073	PRJNA408	3,786 fasta UniProt`

Directly next to the column listing the genome size (4,599,354) there is an accession number (AE014073) that has a hyperlink. This link points towards an EMBL format file with the sequence and sequence features.

This link is:

http://www.ebi.ac.uk/ena/data/view/AE014073&display=txt&expanded=true18

If you copy this link, then you can download the EMBL file directly from the web onto your VM using the following command;

`wget -O reference_flexneri.embl 'http://www.ebi.ac.uk/ena/data/view/AE014073&display=txt&expanded=true'`

This will download the file in the quote marks to the file named reference_flexneri.embl.

Once you have this file you will need to convert it to a .fasta file for the pipeline. To do this use seqret, part of the emboss toolkit;

`seqret reference_flexneri.embl`

Seqret will then ask you to enter a name for your output fasta file. Hit enter to keep the name suggested (ae014073.fasta)

`Read and write (return) sequences
output sequence(s) [ae014073.fasta]:`

You should then have a .fasta file for strain 2457T available for use in sequence mapping.

# Preparing the Nullarbor input files

The sequence files you will be using to run Nullarbor are stored on the CLIMB S3 object store. To download them first download a simple script to pull down the files required using the command;

`wget s3.climb.ac.uk/shigellademo/getseqs.sh`
set this script to be executable

`chmod 755 getseqs.sh`

and then run the script

`./getseqs.sh`

This will then download all the files you need.

# Running Nullarbor

To run Nullarbor you have to provide a file containing details for the isolates you want to process. This file should be tab delimited and should look something like this;

`Sample1	8403_7_6_1.fastq.gz	8403_7_6_2.fastq.gz`
`Sample2	8403_7_7_1.fastq.gz	8403_7_7_2.fastq.gz`
`Sample3	8403_7_19_1.fastq.gz	8403_7_19_2.fastq.gz`
`Sample4	8403_7_22_1.fastq.gz	8403_7_22_2.fastq.gz`


Depending on the sort of VM you are using, there are two options. Nullarbor will test for the number of CPU cores available on your machine, and so smaller machines will run much more slowly. As such we have included two nullarbor files, one for analysing 7 samples, and one for analysing 16 samples.

*For smaller VMs use:*

`nullarbor_samples_basic.tab`

*For larger VMs, the Nullarbor tab file can be found in the file:*

`nullarbor_samples.tab`

Taking your nullarbor samples file, you can process this file and prepare nullarbor to be run using the command:

`nullarbor.pl --name run1 --mlst ecoli --ref ae014073.fasta --input nullarbor_samples.tab --outdir shigellarun1`

These options provide;
* a name for the run
* the MLST schema to use
* the reference genome to use for the run
* the input file containing the list of samples and their fastq files 
* the output directory where the data will be saved to. This directory should not already exist

Running this command will start Nullarbor off checking for the required prerequisits. It builds a makefile (effectively a set of steps to be carried out) and the output directory for the data. Finally nullarbor provides a command to run to start this off.

However, if you run the command as specified, this will run in the foreground, and if you log off will stop. To rectify this we can use a system such as nohup or screen to dispatch the process in its own shell. By starting this off in the background (by appending an & to the command) it is possible to spawn a job that will run in the background and won't stop if you log off. With that in mind, now you can run Nullarbor on your data using a command that should look something like this:

`nohup nice make -j 1 -C /home/ubuntu/shigs/shigellarun1 &`

Nullarbour will then perform a raft of analyses for you, but will take a little while. It is reccomended at this point that you have a look at some of the other tutorials, letting Nullarbour do its thing.

Once it completes you will get a message that looks something like:

`[2]+ Done nice make -j 1 -C /home/ubuntu/shigs/shigellarun1`

# Checking results

At this point you have two options for viewing your results. In your directory you will find a directory for each sample, containing the individual sample-level results. You will also find a directory called "report" where there is a collated version of the results, complete with html page. To use this, you need to copy the report directory to a suitably named directory located within the "public_html" directory of the researcher account of the GVL.

To do this first copy the contents of the report directory to a new directory in the public_html directory of the "researcher" account

`cd shigellarun1/report/
sudo mkdir /home/researcher/public_html/shigella
sudo cp * /home/researcher/public_html/shigella/
sudo chmod 755 /home/researcher/public_html/shigella/*`

You can now view the report by navigating to your GVL instance and clicking on the public_html option and then selecting your directory that you just created and moved the report data to.

Have a look through the report. You will see the full range of outputs generated by Nullarbor. If you have run the full sample dataset, you will see the result of poor quality input sequence, as well as the results that are generated by using data that are of good quality. Have a play with the data and with the results generated and see what Nullarbor can do.

Finally, if you have analysed the larger selection of isolates, you should see that you have a group of samples that are more closely related. When Nullarbor runs its variant calling, the selection of sites for inclusion in the SNP alignment is dependent upon a site being called at that position in all isolates in the collection. Clearly this means that as you add more samples, and as you add more distantly related samples, you will loose resolution within clusters. It might be that you want to look at the relationships of isolates within clusters, and so as a result you may want to recall the core SNPs for a subset of isolates and generate a new tree for those samples.

To do this using the Nullarbor-generated mapping data, identify the samples you want and navigate to the nullarbor directory. In this case we want to look at Samples1-12. To just recall the SNPs on these samples, you can issue the following command;

`nohup snippy-core --prefix shigellaflexgroup Sample1/Sample1 Sample10/Sample10 Sample11/Sample11 Sample12/Sample12 Sample2/Sample2 Sample3/Sample3 Sample4/Sample4 Sample5/Sample5 Sample6/Sample6 Sample7/Sample7 Sample8/Sample8 Sample9/Sample9 &`

This command gets Snippy to rerun the generation of the SNP alignment, just with this group. once complete, you can generate a new tree (to visualise independently) using;

`FastTree -nt < shigellaflexgroup.aln > shigellaflextre.tre`

Hopefully this tutorial has given you an idea of how powerful Nullarbor is for examining a set of isolates. Please let us know if you have any questions.
