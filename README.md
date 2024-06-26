# KG Vejle Germline pipeline

## General info:
This pipeline is used for the following NGS designs at KG Vejle:
1. Panels (AV1, MV1 and CV5 currently supported)
2. WES
3. WGS & WGS CNV


The script takes mapped data (CRAM files) or raw data (fastq) as input.
Hg38 (v3) assembly is used by default. Hg19 is no longer supported. 



# Usage

The tools used and output generated depends on how the pipeline is run, i.e. if it is used for panels or WGS. See below for instructions.



## Panels (AV1, CV5, WES)

Analysis steps:
- If fastq is used as input (using --fastq /path/to/fastq/) perform read preprocessing, alignment, variantcalling and joint genotyping.
- If CRAM is used as input (using --cram /path/to/cram/) perform variantcalling and joint genotyping.
- Creates a merged (joint Genotyped) VCF for all analyzed samples.
- For AV1 panels and WES (ALM and ONK), joint genotyping is also run per subpanel.

NOTE: CRAM should be used as input, if possible.

NOTE: This pipeline can be run from both servers (lnx1 and lnx02). The script assumes that the analysis is run on the lnx01 server by default. Change to lnx02 with --server lnx02


#### Run the pipeline with --help to see available options and default parameters:

    nextflow run KGVejle/germlineNGS -r main --help


### Common use cases:

For panel analysis, the user must use either --cram /path/to/cram/ or --fastq /path/to/fastq/ 

#### Analyze AV1 samples, starting with cram
   
    nextflow run KGVejle/germlineNGS -r main --panel AV1 --cram /path/to/cram/

#### Analyze AV1 samples, starting with fastq
   
    nextflow run KGVejle/germlineNGS -r main --panel AV1 --fastq /path/to/fastq/

#### Analyze ALM / ONK WES samples, starting with fastq:

    nextflow run KGVejle/germlineNGS -r main --panel WES --fastq /path/to/fastq/

#### Analyze all WES (EV8), starting from cram:

    nextflow run KGVejle/germlineNGS -r main --panel WES_2 --cram /path/to/cram/


## Panels (MV1)

Input for the MV1 analysis is always fastq

### Common use cases:

#### Analyze MV1 samples from the lnx01 server (default)
    nextflow run KGVejle/germlineNGS -r main --panel MV1 --fastq /path/to/fastq/

#### Analyze MV1 samples from the lnx02 server
    nextflow run KGVejle/germlineNGS -r main --panel MV1 --fastq /path/to/fastq/ --server lnx02

## WGS

WGS analysis requires the user to point to a folder with inputdata (FastQ or CRAM) OR a tab-delimited samplesheet containing 4 columns without headerline in this specific order:

caseID/projectID, NPN, Relation, SampleStatus

Example samplesheet for standard trio:

johnDoe 123456789012    index   affected 

johnDoe 234567890123    mater   normal

johnDoe 345678901234    pater   normal

The above information can usually be extracted directly from the sample overview excel file

If the inputdata (FastQ or CRAM) has been transferred to the data archive (which it is by default), the pipeline will automatically find the relevant inputdata and create symlinks for them in the output folder, if --samplesheet /path/to/samplesheet/ is used.

The user can point to a specific folder containing raw data (FastQ) using the --fastq option or alignment data (CRAM) using the --cram option
This is only needed if input data (FastQ or CRAM) exists outside the data archive (e.g. if data are in personal folders), or if the pipeline is run without samplesheet.

### Default settings:

By default, the pipeline includes the following modules for the WGS analysis:

1. Preprocessing if FastQ is used as input (fastq --> CRAM or BAM).
2. Variantcalling (GATK Haplotypecaller + jointGenotyping)
3. QC module 
4. CNV and SV calling (CNVkit, Manta, Lumpy and Delly + merging of SV calls using SVDB)
5. STR analysis (ExpansionHunter and Stripy)
6. SMNcaller (SMNCopyNumberCaller)

Submodules can be disabled at command line.



### Common use cases:

#### Analyze WGS CNV, starting from cram:

    nextflow run KGVejle/germlineNGS -r main --panel WGS_CNV --cram /path/to/cram/

#### Analyze WGS CNV, starting from cram:

    nextflow run KGVejle/germlineNGS -r main --panel WGS_CNV --cram /path/to/cram/

#### Analyze samples in samplesheet, starting with CRAM. Run full pipeline:
   
    nextflow run KGVejle/germlineNGS -r main --samplesheet /path/to/samplesheet/


#### Analyze samples in samplesheet, starting with fastq:
   
    nextflow run KGVejle/germlineNGS -r main --samplesheet /path/to/samplesheet/ --fastqInput 
    

#### Analyze samples in samplesheet, starting with CRAM. Do not run QC or STR analysis:
   
    nextflow run KGVejle/germlineNGS -r main --samplesheet /path/to/samplesheet/ --skipQC --skipSTR


#### Analyze all WGS data in folder (no samplesheet), starting with CRAM files:

    nextflow run KGVejle/germlineNGS -r main --cram /path/to/cram/ 
    














