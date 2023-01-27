# Variant Calling Workflow 

The data we are working with is come from a long-term evolution study of an *E. coli* population. Now that we have cleaned the data, we can perform variant calling to see how the population changed over time. We want to know how the population changed compare to the original population *E. coli* strain REL606, therefore we will align each of our samples to the *E. coli* REL606 genome to see what's the difference between them. 

# Alignment to a reference genome 

![workflow](https://datacarpentry.org/wrangling-genomics/img/variant_calling_workflow_align.png) 

We perform read alignment or mapping to determine where in the genome our reads originated from. 

There are a number of tools to choose from, some tools are better suited for particular NGS analyses. In this experiment, we will use the [Burrows Wheeler Aligner (BWA)](https://bio-bwa.sourceforge.net/) which is a software for mapping low-divergent sequences against a large reference genome. 

The alignment process consists of two steps:
1. Indexing the reference genome
2. Aligning the reads to the reference genome. 

## Setting up 

Download the reference genome of *E. coli* REL606:

```sh
mkdir -p ~/intro_to_linux/data/ref_genome
cd ~/intro_to_linux/data/ref_genome
curl -L -o ecoli_rel606.fasta.gz http://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/017/985/GCA_000017985.1_ASM1798v1/GCA_000017985.1_ASM1798v1_genomic.fna.gz 
gunzip ecoli_rel606.fasta.gz
```

We will also need to download a set of trimmed fastq file, it is a small subset of the original trimmed fastq file to let us run the variant calling workflow quickly. 

```sh
mkdir ~/intro_to_linux/data/trimmed_fastq_small
cd ~/intro_to_linux/data/trimmed_fastq_small
curl -L -o sub.tar.gz https://ndownloader.figshare.com/files/14418248
tar xvf sub.tar.gz
ls 
```

There is a new directory called ```sub``` that includes all the files in. To reduce the unnecessary layers of path to our files, we can move all the files in the ```sub``` directory to the ```trimmed_fastq_small``` directory.

```sh
ls trimmed_fastq_small
mv trimmed_fastq_small/* .
ls
```

The output should look like this:

```
SRR2584863_1.trim.sub.fastq  SRR2584866_2.trim.sub.fastq  sub.tar.gz
SRR2584863_2.trim.sub.fastq  SRR2589044_1.trim.sub.fastq
SRR2584866_1.trim.sub.fastq  SRR2589044_2.trim.sub.fastq 
```

We will also need to create some new directories for our output files from the workflow. ```mkdir``` can take multiple arguments as input to create many new directories at once. 

```sh
cd ~/intro_to_linux/data
mkdir -p results/sam results/bam results/bcf results/vcf 
``` 

## Index the reference genome 



## Align reads to reference genome 

## Sort BAM file by coordinates 

# Variant Calling 

## Step 1: Calculate the read coverage of positions in the genome 

## Step 2: Detect the single nucleotide vatiants (SNVs)

## Step 3: Filter and report the SNVs in variant calling format (VCF)

## Explore the VCF format 

## Assess the alignment (visualisation) 

## Viewing with IGV 

# References 

* Data Carpentry - [Data Wrangling and Processing for Genomics](https://datacarpentry.org/wrangling-genomics/)
