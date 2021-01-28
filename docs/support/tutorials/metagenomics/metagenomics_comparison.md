# 3. Comparison and Visualization

In this assignment we compare the taxonomic profiles from the same sample obtained with different methods in the previous exercise. You will also compare taxonomic profiles from two samples, and visualise the comparison with different tools. 

## 3.1 Compare the taxonomic profile of the same sample obtained with 16S rRNA and Clade-specific markers
a] You used KronaTools to visualise the taxonomic profile of the sample in the previous exercise. It is also possible to compare two samples using a modified version of Krona - Krona Compare. In this exercise you should compare the profiles (html files) you made in the previous exercise from the same sample using two different methods - the 16S rRNA and the Clade-specific markers.

Unfortunately Krona Compare only accept integers (numbers without decimals), and MetaPhlAn2 outputs the relative abundance values with decimals. We have modified the MetaPhlAn2 output by multiplying all values with 10, rounding up the values and made integers (an acceptable operation since the relative differences between the talons remains the same). The modified Krona chart from MetaPhlAn2 and a copy of the 16S rRNA Krona chart are available in the Taito directory /proj/csc/biosci/metagenomics/comp_same_sample. To copy this data to your metagenomics directory, execute commands:
```text
module load biokit
module load krona
cd $WRKDIR/metagenomics
cp -r /proj/csc/biosci/metagenomics/comp_same_sample ./
cd comp_same_sample
ls -l
``` 

b] Run Krona compare on the two Krona charts
```text
krona_compare.py sample1_16S.html sample1_metaphlan_int.html sample1_comp_same.html
```

c] Open the comparison in a web browser.
```text
firefox --no-remote "file:///$(pwd)/sample1_comp_same.html"
```
You can switch between the two Krona charts in the upper left corner. In addition to the percentage of abundance, these Krona charts now have also a colour describing similar and different taxonomic groups in the two charts. Groups with similar abundance will appear in green (not many), while groups that differ will appear in red (most of them). Can you identify any taxonomic group that is similar between the two samples? What does this comparison of the taxonomic profile from the same sample using two different methods tell you?


## 3.2 Compare taxonomic profiles of two different samples with Krona Compare
We have pre-run the analysis of a similar sample from human baby faecals using the 16S rRNA method. This baby had a different treatment than the sample 1 baby. This should also be reflected in the gut microbiota. The files from pre-run analysis are in directory /proj/csc/biosci/metagenomics/comp_diff_sample. Copy the data to your metagenomics directory:
```text
cd $WRKDIR/metagenomics
cp -r /proj/csc/biosci/metagenomics/comp_diff_sample ./
cd comp_diff_sample
ls -l
``` 

a] Run Krona compare on the two Krona charts
```text
krona_compare.py sample1_16S.html sample2_16S.html sample1_comp_diff.htmlb] What are the main differences in the taxonomic profile between sample 1 and 2? The two babies was both premature. One of them was treated with a probiotics containing bacteria from two different genus (Bifidobacterium and Lactobacillus). Which of the samples do you think came from the baby that had the probiotic treatment?
```
## 3.3 Run MetaQUAST on the assemblies of two different samples
a] In the folder /proj/csc/biosci/metagenomics/3.3 we have put your assembly of the trimmed reads from sample 1 together with the assembly of the trimmed reads from sample 2 (which we have pre-run) and a new reference list (reference_list_2.txt). Obtain the files with commands:
```text
mkdir 3.3
cd 3.3
cp /proj/csc/biosci/metagenomics/3.3 ./
```
Run MetaQUAST on the two assemblies. This will take approximately 20 minutes.
```text
metaquast.py sample1_trim.fasta sample1_raw.fasta sample1_500.fasta sample1_metaspades —references-list reference_list_2.txt -t 4
``` 

b] Open the MetaQUAST report in a web browser

Which of the samples produced the largest assembly?
Which of the assemblies produced the longest contig, and what is the size of this?
Looking at the plot of contigs aligning to the reference genomes, do these results support your finding in 2 b]?
Try to open the Icarus contig browser and select a Bifidobacterium with a high fraction of the genome covered by contigs from your samples, eg. Bifidobacterium longum subsp. infants and see in more detail how the two different samples cover this genome
