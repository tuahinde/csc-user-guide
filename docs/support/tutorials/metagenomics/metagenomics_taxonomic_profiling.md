# 2. Taxonomic profiling
Taxonomic classification allows you to find out what organisms the sample contains. The aim of this assignment is to to create a taxonomic classification starting from the trimmed and merged reads that we prepared in the previous exercise. We will use two alternative approaches: classification based on 16S rRNA sequences and classification using clade-specific marker genes. Please see the slides and video of the introductory lecture to this assignment.

## 2.1 Taxonomic classification using 16S rRNA sequences
In order to do taxonomic classification we need to get hold of all the 16S rRNA reads in our whole genome shotgun dataset. Since 16S rRNA sequences are relatively conserved and present in all prokaryotic species, they can answer the question "Who is there?".

a] Log in to Taito and load the biokit module. Then move to the directory where the sequence data from the previous exercise is, and view what is in the directory:
```text
module load biokit
cd $WRKDIR/metagenomics
ls -l
```

b] First we need to predict/find rRNA sequences. The script predict_16s.py takes a single fasta file as input. You should perform the taxonomic classification on the sequences that passed the quality filtering. Convert the trimmed and filtered reads in the FASTQ files from the previous exercise to FASTA format:
```text
seqtk seq -a sample1_seqprep_trimmomatic_R1.fastq.gz > sample1_seqprep_trimmomatic_R1.fasta
seqtk seq -a sample1_seqprep_trimmomatic_R2.fastq.gz > sample1_seqprep_trimmomatic_R2.fasta
seqtk seq -a sample1_seqprep_merged_trimmomatic.fastq.gz > sample1_seqprep_merged_trimmomatic.fasta
``` 

c] Concatenate the three fasta files into a single fasta file:
```text
cat sample1_*trimmomatic*.fasta > sample1_catAll.fasta
```

d] Run the script predict_16s.py on the concatenated FASTA file:
```text
predict_16s.py -t -b -n 2 -o sample1_16S.fasta sample1_catAll.fasta
```

e] Count the number of reads with 16S rRNA sequences:
```text
grep -c "^>" sample1_16S.fasta
```

f] LCAClassifier comes with a custom database called Silvamod which is based on the Silva rRNA database. We first need to blast our 16S rRNA reads against this database. Run Megablast on the 16S rRNA reads against the Silvamod database. This step takes a while to run.
```text
pb blastn -query sample1_16S.fasta -db silvamod -out  sample1_16S_megablast.xml -outfmt 5 -num_alignments 100 -evalue 0.001
``` 

g] The blast output is an xml file that the LCAClassifier uses to produce a taxonomic classification. In the following command the parameters "composition", "tree" and "assignments" will give three different output file formats. "2" is the precision (number of correct assignments/number of assignments made), and "155" is the similarity threshold.
```text
LCAClassifier.py sample1_16S_megablast.xml sample1_composition sample1_tree sample1_assignments 2 155
```

h] KronaTools is one of many ways to visualize a classification. It is a set of perl scripts that can generate a pie chart that can be browsed interactively. To use Krona tools, run the set-up command:
```text
module load krona
```

The file "assignments" you produced in the previous step is a modified version of the original assignments file from LCAClassifier that works with Krona. Convert this file to an html file using the ktImportText command in Krona tools:
```text
ktImportText -o sample1_16S.html sample1_assignments
```

i] To open the Krona chart (sample1_16S.html) you should download it to your local computer, or open Firefox in Taito with command (note that in order to use Firefox directly from Taito, you need to use the NoMachine virtual desktop or ssh terminal connection that has X-term tunneling enabled.):
```text
firefox --no-remote "file:///$(pwd)/sample1_16S.html"
```

What is the most abundant genus?
What is the most abundant species and its relative abundance?
The Classification should have a certain percentage of "No Hits". Why do you think this is?

## 2.2 Taxonomic classification using clade-specific marker genes

In this part of the exercise you will perform taxonomic profiling using a tool called MetaPhlAn2 , which comes with a database of 1 million markers from more than 7,500 species. When running MetaPhlAn2, sequences in your sample will be mapped against the markers, and based on the marker coverage the relative abundance will be estimated.

a] Move to the directory where the sequence data from the previous exercise is and view what is in the directory

b] Because sequence contaminations and poor quality reads may give erroneous hits against marker genes, you will run the analysis on the trimmed data:
```text
metaphlan2.py sample1_seqprep_trimmomatic_R1.fastq.gz,sample1_seqprep_trimmomatic_R2.fastq.gz,sample1_seqprep_merged_trimmomatic.fastq.gz –mpa_pkl /appl/bio/metaphlan/db_v20/mpa_v20_m200.pkl --bowtie2db  /appl/bio/metaphlan/db_v20/mpa_v20_m200 --bowtie2out sample1.bowtie2.bz2 --input_type fastq --ignore_viruses  --ignore_eukaryotes -o sample1_metaphlan.txt
```

c] The taxonomic profile for this sample is in the sample1_metaphlan.txt. Open the file "sample1_metaphlan.txt" in a text editor. What is the most abundant genus?

d] Convert the output to a Krona chart using the metaphlan2krona script in MetaPhlAn2 and the ImportText in KronaTools:
```text
metaphlan2krona.py -p sample1_metaphlan.txt -k sample1_metaphlan.out ImportText.pl -o sample1_metaphlan.html sample1_metaphlan.out
```

e] Open the sample1_krona.html in a web browser.
```text
firefox --no-remote "file:///$(pwd)/sample1_krona.html"
```
What is the most abundant genus?
What is the most abundant species and its relative abundance?
