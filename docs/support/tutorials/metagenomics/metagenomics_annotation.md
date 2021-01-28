4. Functional annotation with the META-pipe server
Functional annotation of a metagenomics samples is based on comparing the assembled sequence contigs to reference databases like SwissProt or InterPro. As the metagenomics datasets can be very large, this analysis step requires much computational resources and can take several days.

In the workflow discussed here, functional annotation is performed using the META-pipe server that is designed for marine metagenomics samples. This server is maintained by the University of Tromsö and is at the moment directly available for only Norwegian researchers. The Finnish users can use this service too, but only with the support of CSC as described below.

 4.1 Accessing META-pipe with the support of CSC
If you want to use META-pipe functional annotation server in your research project, send a request e-mail to address: servicedesk@csc.fi. Please include following information to the request.

Basic information about the research project including the name and affiliation of the principal investigator.
How many metagenome assemblies are planned to be analyzed and how large the assemblies are (rough estimates).
When the functional annotation is planned to be executed.
Contact information of the researcher carrying out the analysis.
After the request has been accepted. CSC will set up a virtual server for the annotation task and submit the server access code to the researcher carrying out the analysis.

4.2 Performing the analysis
The META-pipe annotation server takes assembled metagenome as an input (see chapter 1.5). The annotation pipeline executes following steps:

MetaGeneAnnotator   Prediction of genes.
PRIAM  Annotation of EC-­numbers with RPSblast
Uniref50  BLAST comparison against  clustered UniProt 
InterPro  comparison against collection of 14 protein domain databases with InterProScan  
MarRef  BLAST search against MarRef reference database for completely sequenced marine prokaryotic genomes.
To start the analysis open web browser session to address:
   https://metapipe.uit.no/

Note, that if your assembled genome locates in Taito, you can use NoMachine virtual desktop to launch a Firefox session running in Taito.

In The META-pipe web interface click "Annotate" button. This takes you to ELIXIR AAI authentication page. In the case of most Finnish universities, you should be able to use your home organization credentials for signing in.

Once logged in, press the Upload New button in the Select Dataset field. Choose the dataset (fasta formatted metagenomics assembly ) from your local machine (or from Taito if you have started Firefox there). Once the file has been uploaded, check the parameters and modify them if needed.

Remember to add the metapipe job-tag you got from CSC before submitting the job. To do this go to the bottom of the META-pipe page and tick on: Enable dev tools option
This  opens two new fields. You should now type the job-tag code to the "job-tag" field.

After this you can submit the job by pressing the Start Meta-pipe button.

Once submitted, you can check the status of your annotation task using the "Running jobs" lab and once the annotation has finished, use "See results" tab to download the results. The output folder of your annotation job contain tree files:

genes.nuc.fasta - predicted genes

genes.prot.fasta - protein translations of the gene predictions

export.gb - functional annotations in GenBank format

The results can be visualized with for example Artemis software.

4.3 Related materials
META-pipe demo video. A video recording demonstrating the usage of META-pipe WWW interface. This video was created with an earlier version of the META-pipe functional annotation server so it does not match exactly with the current WWW interface. However all the essential features and functions are the same.
