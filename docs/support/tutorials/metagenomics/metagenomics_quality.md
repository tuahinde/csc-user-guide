# 1.  Quality control, filtering, assembly & validation
In this assignment we will be working with a metagenomic whole genome shotgun dataset from human baby faeces. The sample has been sequenced with an Illumina MiSeq machine, using paired end technology. The main objectives are to pre-process and assemble the reads and to validate the assembly. You will first practise working on command line, produce summary and quality statistics of the sequence data, perform filtering, and assemble the pre-processed sequence data. Finally you will validate the assembled metagenome and produce a summary statistics of the assembled metagenome. Introductory slides for this assignment are available.

## 1.1. Setting up the environment and obtaining sample data
Copy sample data to a new directory called "metagenomics" in the $WRKDIR directory in Taito:

```text
cd $WRKDIR
mkdir metagenomics
cd metagenomics/
cp /proj/csc/biosci/metagenomics/sample*.gz ./
cp /proj/csc/biosci/metagenomics/md5_data.txt ./
``` 

Make sure all your data is there and get to know the FASTQ file format:

a] Open the md5_data.txt file and look at the content in a text editor. It should have one checksum per file

b] Check that the sum is correct for your files by typing:
 md5sum -c md5_data.txt
If the files are complete, the test should return 'OK' for all files
c] Optional: You can try to generate MD5 checksum on a file and output it to a file:

```text
md5sum filename > md5_test.txt
``` 

d] 'file' let you test the type of file. In the directory where the compressed FASTQ files are located, type:

```text
file sample1_R1.fastq.gz
```

What type of file is this?

e] The FASTQ files containing the sequence reads are normally compressed with gzip. To look at the content you must either uncompress the files, or you can use zcat to preview the content. 'head' is a preview tool. '|' pipes the output from one command into the next command. Type:
```text
 zcat sample1_R1.fastq.gz | head
``` 
 
Each sequence read has four lines. What information do these lines contain?

f]It may be useful to know how many sequences a FASTQ file contains. Count sequence numbers compressed file:
```text
zcat sample1_R1.fastq.gz | echo $((`wc -l`/4))
```

Are there equal number of sequence reads in both the paired FASTQ files?

Also it is useful to convert FASTQ files to FASTA format. Seqtk is a nice and extremely fast tool for processing sequences in the FASTA or FASTQ format. You can use the Seqtk toolkit to convert the FASTQ file to a FASTA file in addition to other manipulations such as extracting subsequences from FASTA/Q file with reads ID (a name list file). For details please see the Seqtk manual.

In Puhti seqtk can be activated with command:
```text
module load biokit
```
g] Convert fastq.gz to fasta:
```text
seqtk seq -a sample1_R1.fastq.gz > sample1_R1.fasta
```
HINT: here is a unix one-liner to do the same. You can try it optionally
```text
gunzip -c sample1_R1.fastq.gz  | awk '{if(NR%4==1) {printf(" >%s\n",substr($0,2));}="" else="" if(nr%4="=2)" print;}'="" >="" sample1_r1.fasta
```

i] Have a look at the FASTA file:
```text
head sample1_R1.fasta
```
What is the main difference between a FASTQ and a FASTA file?

j] Subsample 100 read pairs from two large paired FASTQ files using seqtk:
```text
seqtk sample -s100 sample1_R1.fastq.gz 100 > sample1_R1_red.fastq
seqtk sample -s100 sample1_R2.fastq.gz 100 > sample1_R2_red.fastq
```
k] Count the number of sequences in the reduced file:
```text
cat sample1_R1_red.fastq | echo $((`wc -l`/4))
```
l] Do a quick fastq QC - base/quality summary:
```text
seqtk fqchk sample1_R1.fastq.gz
```
What is the average length of the sequence reads?

 

## 1.2 Quality control - FastQC
We will perform the quality control of the raw data with FastQC using its graphical user interface. For more instructions and examples please see the FastQC manual  (html or pdf).

a] Start FastQC by typing:

fastqc
Note that as FastQC uses graphical user interface, you must use NoMachine virtual desktop or Xterm enabled terminal connection to launch the program. Once FastQC is running, click on ‘File' and select the two compressed FASTQ files in the $WRKDIR/metagenomics directory ($WRKIR is /wrk/your_user_name)

FastQC operates a queuing system where only one file is opened at a time, and new files will wait until existing files have been processed.

b] The left hand side of the main interactive display shows a summary of the modules which were run and an evaluation of the results. What do the traffic lights indicate?

c] How many reads does each of your files contain? What is the sequence length range? What is the GC content of the genome? What does a quality score of 30 indicate about the accuracy? Which of the sequence files would you say has the best base calling quality?

The "Per Base Sequence Quality" plot shows an overview of the range of quality values across all base positions in the FASTQ file. The y-axis on the graph shows the quality scores. The higher the score the better the base call. The quality of calls on most platforms will degrade as the run progresses, so it is common to see base calls falling into the orange area towards the end of a read.

d] What are the major differences between the quality of the reads in R1 and in R2?

e] View the "Per Base Sequence Content plots". In a random library you would expect that the lines in this plot should run parallel with each other. Do your reads have any biases in any position?

f] Illumina believes this effect is caused by the "not so random" nature of the random priming process used in the protocol. This may explain why there is a slight overall G/C bias in the starting positions of each read. The first 12 bases probably represent the sites that were primed by the hexamers used in the random priming process. Look at the Kmer content plot and see which Kmers are over-represented and in which position they are.

g] Have a look at the other modules, especially the ones marked with a red cross. Can you say something about the duplication level, etc?

## 1.3 Merge reads - SeqPrep
a] In order to reduce the number of reads for the downstream tools/analysis, we normally merge sequence read-pairs that overlap with a tool called SeqPrep. (SeqPrep was taken in use with command "module load biokit"):
```text
SeqPrep -f sample1_R1.fastq.gz -r sample1_R2.fastq.gz -1 sample1_seqprep_R1.fastq.gz -2 sample1_seqprep_R2.fastq.gz -s sample1_seqprep_merged.fastq.gz
```
b] How many merged reads were there?
```text
zcat sample1_seqprep_merged.fastq.gz | echo $((`wc -l`/4))
```
c] How many merged reads were there in the non-merged files?
```text
zcat sample1_seqprep_R1.fastq.gz | echo $((`wc -l`/4))
``` 

## 1.4 Pre-processing next-generation sequence data - Trimmomatic
Trimmomatic is a fast, multithreaded filtering/trimming tool written in java. It can be used to trim and crop Illumina (FASTQ) data as well as to remove adapters. There are two major modes of the program: Paired end mode and Single end mode, therefore you will need to run the tool twice: once for the paired data, and once for the file with the merged reads (single end). For the full explanation of the parameters see manual.

a] In the terminal window type:
```text
trimmomatic -h
```
This will display a list of options and parameter settings you can apply on your sequence data.

The FastQC quality check showed that there was an enrichment of certain Kmers in the ~12 first residues of the reads. The quality of the bases also dropped toward the end of the reads. In addition there were some duplication in the dataset. Use Trimmomatic to trim the reads:

b] You must specify both fastq files as input (-fastq and –fastq2). In addition, filter out reads which have average quality below phred score 20 (AVGQUAL). Trim the reads in both ends: the first 12 nt in the 5' (HEADCROP), and all reads below threshold quality 3 in both ends the 3' (LEADING and TRAILING). Finally, filter out reads which areshorter than 75 nt (MINLEN):
```text
trimmomatic PE -phred33 sample1_seqprep_R1.fastq.gz -threads 4 sample1_seqprep_R2.fastq.gz sample1_seqprep_trimmomatic_R1.fastq.gz Discraded_trimmomatic_R1.fastq.gz sample1_seqprep_trimmomatic_R2.fastq.gz Discraded_trimmomatic_R2.fastq.gz AVGQUAL:20 HEADCROP:12 LEADING:3 TRAILING:3 MINLEN:75
```
c] You also need to trim the file containing the merged read pairs:
```text
trimmomatic SE -phred33 sample1_seqprep_merged.fastq.gz -threads 4 sample1_seqprep_merged_trimmomatic.fastq.gz AVGQUAL:20 HEADCROP:12 LEADING:3 TRAILING:3 MINLEN:75
```
d] Perform a FastQC quality control of the trimmed sequence data, and compare the preprocessed data with the raw data from the first FastQC run. What are the main differences?

## 1.5 Assembly - Megahit
We will use the Megahit to assemble the trimmed data. Megahit is an ultra fast assembly tool for metagenomics data. You can read more about Megahit in the manual.

a] Megahit is not included in biokit module. To use it, unload biokit module with command:

module purge
Then load the Megahit environment with commands:

module load intel/16.0.0
module load megahit
b] Assembling metagenomic data can be very resource demanding. Note that you should not run Megahit in the login nodes of Taito.If you are running the command interactively in taito-shell.csc.fi, you can use 128 GB RAM and 4 cores, which is quite good when the metagenome is not very complex, hence assembling over 2 million reads will take about 20 minutes. For larger analysis task we recommend running Megahit as a batch job with 12 cores.
```text
megahit -1 sample1_seqprep_trimmomatic_R1.fastq.gz -2 sample1_seqprep_trimmomatic_R2.fastq.gz -r sample1_seqprep_merged_trimmomatic.fastq.gz -t 4 -o sample1_trim
```

c] When the assembly is done, go the the directory where the assembled contigs are:
```text
cd sample1_trim
```
d] Since you will be working with several assemblies, rename the FASTA file with the contigs to sample1_trim.fasta:
```text
mv final.contigs.fa sample1_trim.fasta
```
e] Count the number of contigs in the fasta file:
```text
grep -c "^>" sample1_trim.fasta
``` 

## 1.6 Quality assessment - MetaQUAST
MetaQUAST evaluates and compares metagenome assemblies based on alignments to close references. It is based on the QUAST genome quality assessment tool, but addresses features specific for metagenome datasets. The tool will divide all contigs into groups aligned to each reference genome they align to. For comparing purposes we have pre-run the assembly of the same data using

only on the raw (untrimmed) data
the trimmed data keeping only the large contigs (< 500 bp)
using a different assembler called MetaSPAdes.
The contig files are located in directory /proj/csc/biosci/metagenomics/assemblies/.

a] Create a directory called assemblies in the metagenomics directory, and copy the assembly you made with the trimmed data to this directory. Execute the following commands when you are in the directory containing the assembly sample1_trim.fasta:
```text
mkdir ../assemblies
cp sample1_trim.fasta ../assemblies
```
b] Then move to this directory and copy the pre-calculated assemblies there:
```text
cd ../assemblies
cp /proj/csc/biosci/metagenomics/assemblies/* ./
ls -l
```
In this directory, there is also a file named reference_list.txt. This text file contains a pre-made list of reference genomes we know are in the sample. To save compute time, you can provide this list of reference genomes together with the input files. Alternatively, MetaQUAST can produce a reference list by identifying which species are present based on searching for 16S rRNA sequences in the contigs. It will then download the reference genomes from NCBI, and align the contigs against these genomes. This will make the run time longer.

c] Run MetaQUAST on these assemblies and evaluate which performs best/worst:

```text
module load biokit
metaquast.py sample1_trim.fasta sample1_raw.fasta  sample1_500.fasta sample1_metaspades.fasta --references-list reference_list.txt -t 4
```
d] Open the MetaQUAST report in a web browser. The report locates in subfolder quast_results/results_date_and_index/combined_reference. (Note that you must use NoMachine connection to run Firefox remotely from Taito)
```text
cd quast_results/results_date_and_index/combined_reference
firefox --no-remote "file:///$(pwd)/report.html"
``` 

Which of the assemblies is the largest and the smallest (in bp)?
Which of the assemblies produces the longest contig?
Which of the assemblies produces most unaligned contigs?
Which of the assemblies produces mismatches relative to the reference genomes?
Which method will you choose for this data and why?
 

e] Icarus generates contig size viewer and one or more contig alignment viewers. Contig size viewer contigs ordered from longest to shortest. Open the Icarus.html in a web browser and go to the contig size viewer.
```text
firefox --no-remote "file:///$(pwd)/icarus.html"
```
Try to fade contigs shorter than 10000 bp
Why do you think so many of the largest contigs are tagged as misassembled?
How many bp is the longest correctly aligned contig?
f] The contig alignment viewer places contigs according to their mapping to the reference genome. Choose the Klebsiella oxytoca OK-1 genome.

Use the search function to identify the conting named "NODE_37_length_44183_cov_13.918". Why is this flag as "suspicious"?
MetaSPAdes produces longer contigs than Megahit on these data. Navigate to "NODE_22_length_73354_cov_16.7912". Is it always better with obtaining the longest contigs?
