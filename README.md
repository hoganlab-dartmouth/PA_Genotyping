# PA_SNPcalling
Genotyping pipeline, align Illumina short reads data to a provided reference, label small and structural variants, and assemble/annotate a de novo genome draft for each sample's provided short reads.

Built from template pipeline created by Dartmouth GMBSR


# Hogan Lab Psuedomonas SNPcalling and Assembly workflow
Genome assembly and variant annotation relative to Pseudomonas aeruginosa PA14 and PAO1 (or other supplied/curated genome)

## Introduction 
The pipeline takes raw *FASTQ* files from *Pseudomonas aeruginosa* isolates as an input and returns *FASTA* file for a genome assembly, a *GBK* file for genome annotation, and a *VCF* file with annotated SNPs and structural variants relative to either PAO1 or PA14 as indicated by the user. This pipeline was written to be executed on high performance computing clusters (HPCs) using the slurm scheduler or using a single high CPU, high RAM machine, and has been made available by the *Data Analytics Core (DAC)* of the *Center for Quantitative Biology (CQB)*, located at Dartmouth College. 

Both single- and paired-end datasets are supported, in addition to both library preparation methods for full-length or 3'-only analysis. 

Required software can be installed using Conda with the environment file (environment.yml), or specified as paths in the config.yaml file.


## Pipeline summary:
The major steps implmented in the pipeline include: 

- FASTQ quality control assesment using [*FASTQC*](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)
- Read trimming for adapters and read quality using [*trimmomatic*](https://github.com/timflutre/trimmomatic)
- Alignment using [*bwa*](https://github.com/lh3/bwa)
- Duplicates are marked with [*MarkDuplicates*](https://gatk.broadinstitute.org/hc/en-us/articles/360037052812-MarkDuplicates-Picard-)
- Variant calling using [*freeBayes*](https://github.com/freebayes/freebayes)
- Variant annotation using [*SnpEff*](http://pcingola.github.io/SnpEff/)
- Genome assembly using [*Spades*](https://github.com/ablab/spades) or [*minimap*](https://lh3.github.io/minimap2/minimap2.html)
- Genome annotation using [*prokka*](https://github.com/tseemann/prokka)
- Quality control of genome assembly using [*quast*](https://github.com/ablab/quast)

All of these tools can be installed in a [conda environment](https://docs.conda.io/en/latest/) or on paths available to a computing server. As input, the pipeline takes raw data in FASTQ format, and produces an annotated VCF file. Quality control reports are aggregated into HTML files using *MultiQC*. 

## Implementation
The pipeline uses Snakemake to submit jobs to the scheduler, or spawn processes on a single machine, and requires several variables to be configured by the user when running the pipeline: 
* **sample_tsv** - A TSV file containing sample names and paths to fastq paths.  See example in this repository for formatting.
* **layout** - 'paired' 
* **aligner_index** - Path to Hisat genome reference index  
* **annotation_gtf** - Absolute path to genome annotation file (.gtf) 
* **reference_fasta** - Absolute path to a fasta file for the reference genome
* **snpeff_genome** - The name of the reference genome being used - no spaces in the name please.

  
## Running tests using pre-built environments on Discovery
Clone this repository:
```shell
git clone https://github.com/hoganlab-dartmouth/PA_SNPcalling.git
cd PA_SNPcalling
```
Create a conda environment containing Snakemake:
```shell
conda create -n snakemake -c bioconda snakemake
```

Activate an environment containing Snakemake:
```shell
conda activate snakemake
```

Check that the reference file you need is available (PAO1 or PA14):
*add command to check that those all required forematted references are accessible* 
```shell
snakemake -s Snakefile check_ref
```

Run the pipeline:
  - *-s Snakefile* specifies the Snakefile with the code to run
  - *--configfile* specifies the config file which defines variables for the Snakefile
  - *-j 6* asks for 6 cores
  - *-k* keep going until you can't move forward anymore
    
```shell
snakemake -s Snakefile  --configfile config.yaml -j 6 -k
```
  
**Contact & questions:** 
Please address questions to *DataAnalyticsCore@groups.dartmouth.edu* or submit an issue in the GitHub repository. 

**This pipeline was created with funds from the COBRE grant **P20GM130454**. 
If you use the pipeline in your own work, please acknowledge the work done by the core by citing the grant number in your manuscript.**

