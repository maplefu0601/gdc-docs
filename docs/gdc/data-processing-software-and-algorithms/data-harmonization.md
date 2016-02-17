# Introduction

GDC draws upon the expertise of collaborators in the development of pipelines supporting the [harmonization](../gdc-data-harmonization) of DNA and RNA sequence data and the generation of derived data. GDC derived data types include DNA sequence variants, mutation analyses, array-based copy number segmentation, mRNA and miRNA digital expression analyses, and splice-junction quantification. GDC pipelines are implemented using algorithms, software, parameters, input file selection and output file format details that are selected in consultation with the GDC Bioinformatics Advisory Group.

This document describes the strategies employed by GDC for harmonizing data against a common reference genome and generating derived data types. The software and algorithms used by GDC to process GDC data are identified within each strategy.

# Data Harmonization

GDC [harmonizes](../gdc-data-harmonization) core genomic sequence, administrative, biospecimen and clinical data. The following sections detail the harmonization and annotation methods for each of these data types.

## Genomic Data

One of GDC's primary tasks is to ensure that genomic data provided to users are comparable. GDC performs alignments or realignments of BAM/FASTQ sequence data against the latest reference genome major build (currently [GRCh38](http://www.ncbi.nlm.nih.gov/projects/genome/assembly/grc/human/#GRCh38)). In order to increase the accuracy and efficacy of alignment, as well as obtain information regarding the presence of oncoviruses, GDC adds multiple decoy sequences to the GRCh38 reference genome. These decoys include genomes of common human retroviruses and cancer-related DNA viruses, as well as human genome sequences that are not included in this major assembly. The current virus decoy contains 10 types of human viruses, including human cytomegalovirus (CMV), epstein-barr virus (EBV), hepatitis B (HBV), hepatitis C (HCV), human immunodeficiency virus (HIV), human herpes virus 8 (HHV-8), human T-lymphotropic virus 1 (HTLV-1), merkel cell polyomavirus (MCV), simian vacuolating virus 40 (SV40) and human papillomavirus (HPV). The exact decoy sequences are selected in consultation with the GDC Bioinformatics Advisory Group. GDC documents the accession numbers or exact sequences of these decoys.

Currently, GDC accepts genomic data in either BAM or FASTQ format. After data has been successfully submitted to GDC, harmonization processes identify unharmonized genomic data and pull these files into local storage to begin processing.

GDC performs initial BAM format check using [Picard Tools](http://broadinstitute.github.io/picard/) for validation. Validated BAM files are split and converted to FASTQ files by read groups. GDC performs read quality check on these splited or submitted FASTQ files using [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/). Submitted data QC summary report will be available for the submitter, and later, for all users after data is released. In addition, based on the severity of any QC problems, GDC may put annotation on the data, and make decisions about further data processing and release. Data submitters can direct questions about the reported QC metrics to GDC User Services.

Next, data-type specific alignment pipelines realign sequence reads to the reference genome. All read group BAMs generated from a single original data file are then merged into a single BAM. The processes then register the new BAM file with the GDC API, upload it to the Object Store, and update the GDC data model. Meanwhile, key QC metrics, such as mapping statistics, insert size distribution and RNA-Seq coverage depth, are recorded and used to update the annotation if appropriate. Finally, GDC implements data versioning and creates a data freeze after each harmonization.

Depicted below is a high level overview of the sequence realignment pipelines (Image 2.1-1). The specific tools or algorithms used for each step are based on data type and platform.

[[nid:8096]]

*Image 2.1-1 - Overview of GDC Sequence Realignment Pipelines*

In cases of large reference genome updates or significant pipeline improvements in the future, it will be occasionally necessary for GDC to reharmonize data based on the new reference genome, software tools and other parameters. The GDC workflow system allows modular replacement of reference genome, pipeline steps and other parameters. This, in combination with the digital ID system, makes the data reharmonization process highly automatable.

GDC's current data harmonization pipeline for whole genome sequence (WGS), whole exome sequence (WXS), mRNA-seq and miRNA-seq data is implemented as follows, with minor differences in computational strategy for each data type.

### Pre-processing

Files in BAM format are first validated with  [Picard Tools](http://broadinstitute.github.io/picard/) (Broad Institute) ValidateSamFile function. After successful validation, the BAM split process generates read group FASTQ files using [Biobambam](https://github.com/gt1/biobambam) (Wellcome Sanger Trust Institute) bamtofastq function. [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) (Babraham  Institute) is then used to obtain various quality control metrics on each FASTQ file. If necessary, the 3' and 5' ends of reads can be soft-clipped to remove low quality bases that can negatively influence alignment.

### Read Alignment

The read groups from each sample are aligned independently to the same human reference genome build. GDC has tested and evaluated a variety of different aligners. Currently GDC uses [Burrows-Wheeler Aligner (BWA)](http://bio-bwa.sourceforge.net) for DNA-Seq (including WXS and WGS) and miRNA-Seq alignment, and [STAR](https://github.com/alexdobin/STAR) (Cold Spring Harbor Laboratory) for RNA-Seq alignment.

### Post-processing

After alignment, all read group BAMs belonging to a single sample are sorted and merged into a single BAM. It is well known that PCR artifacts can lead to non-uniform amplification of reads and subsequent calling errors during downstream genotyping. Thus GDC also uses Picard Tools to identify and mark duplicate reads in harmonized DNA-Seq BAM files.

### Alignment Quality Control

To ensure the best practice of data generation, GDC collects many data quality metrics during the harmonization process. These include not only raw reads quality measurement by FASTQC during pre-processing, but also multiple post-alignment QC metrics collection. Two general examples are [Samtools](http://www.htslib.org/) flagstat and idxstat, that are used to determine the number of mapped, uniquely mapped, duplicate, and properly paired reads in general, and mapped reads to each reference genome contig. GDC also collects data type specific metrics based on suggestions from the scientific community.

The following subsections describe the details of GDC harmonization workflow.

## Biospecimen Data

Biospecimen data refers to information associated with the physical sample taken from a participant and its processing down to the analyte or aliquot level for sequencing experiments. These data fall into several key categories:

* Standard Identifiers : project-unique identifiers and universally unique identifiers (UUIDs) that enable cases and samples to be referenced and linked to associated clinical and analysis data
* Provenance : metadata that indicate the upstream sources of the sample (research program, research project, and donor individual) as well as the downstream products of sample processing (e.g., extracted DNA or RNA analyte)</li>
* Quality Control : metadata that express the values of quality control tests performed on biospecimens and analyzed products (e.g., percent tumor nuclei, RIN values, A260/A240 values)

For major NCI CCG programs, biospecimen data are provided by a Biospecimen Core Resource (BCR) under contract to the NCI. Data is submitted in an established, schema-valid XML format. This data includes program and project identifiers, item UUIDs and the relationships between case, sample, and aliquot. UUIDs submitted by BCRs are adopted by GDC.

For other submitters, data in the GDC-compatible JavaScript Object Notation [(JSON)](www.json.org/) format, or BCR XML format are accepted. However, GDC also provides a simpler means for submission of a minimal set of biospecimen data, in which a submitter may format the data in a tab-delimited text file and submit via a GDC web page.

GDC's [data model](/developers/gdc-data-model) uses a graph representation that has no technical limits on adjusting the entities and relationships. GDC collects many biospecimen elements, such as portion, analyte, slide, report, etc. Currently, the key biospecimen elements that GDC focuses on are:
* Case : also known as patient or participant, is someone who contributes one or more samples for a particular disease study
* Sample : a tumor or normal tissue, cell or blood sample provided by a case
* Aliquot : the unit of analysis for genomic data, often in the form of DNA, RNA or proteins

## Clinical Data

Clinical data on cases enrolled in major NCI CCG programs are provided by BCRs in schema-valid XML format. Other submitters may provide clinical data in a simplified, tab-delimited text format via a GDC web page. Clinical data elements indexed by the GDC are described in the following table.

[[nid:8444]]

Each clinical dataset is persisted as a node in the GDC data model. The node properties contain the value of each of the harmonized/indexed clinical data elements and an edge between the clinical node and a case will encode what case is being represented by the clinical information. Because all of the clinical data elements are encoded as properties in the node, it is technically easy to remove or add clinical data elements.

GDC dictionaries contain clinical element terms, semantic information, and accepted data types and values.
