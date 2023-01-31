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
cd ~/intro_to_linux/results
mkdir -p sam bam bcf vcf
``` 

## Index the reference genome 

The first step of alignment is to index the reference genome, it allows the aligner to find potential alignment sites for query sequences quickly. Indexing the genome only has to be run once, but if you work on a different genome or using a different alignment tool, you have to do the index again. 

```sh
cd ~/intro_to_linux/data/ref_genome
bwa index ecoli_rel606.fasta 
```

The output on screen should look like this:

```
[bwa_index] Pack FASTA... 0.04 sec
[bwa_index] Construct BWT for the packed sequence...
[bwa_index] 1.05 seconds elapse.
[bwa_index] Update BWT... 0.03 sec
[bwa_index] Pack forward-only FASTA... 0.02 sec
[bwa_index] Construct SA from BWT and Occ... 0.57 sec
[main] Version: 0.7.17-r1188
[main] CMD: bwa index data/ref_genome/ecoli_rel606.fasta
[main] Real time: 1.765 sec; CPU: 1.715 sec 
```

## Align reads to reference genome 

After finishing index out reference genome, the next step is to map our reads against the reference genome. There are a few algorithms to choose from in the BWA, for our sequence data we will use BWA-MEM algorithm. BWA-MEM is good for 70bp or longer Illumina, 454, Ion Torrent and Sanger reads, assembly contigs and BAC sequences. 

An example of how ```bwa``` command works:

```sh
bwa mem ref_genome.fasta input_file_R1.fastq input_file_R2.fastq > output.sam 
```

* ```mem``` means the algorithm we are using
* ```ref_genome.fasta``` is our reference genome
* ```input_file_R1.fastq``` and ```input_file_R2.fastq``` are our sequencing files
* ```output.sam``` is the output file from the alignment 

There are many more options you can use for the alignment, please see the [BWA manual page](https://bio-bwa.sourceforge.net/bwa.shtml). 

We are going to use only one sample ```SRR2584866``` in our dataset for the alignment. Later, we will iterate the process on all of our samples. 

```sh
cd ~/intro_to_linux
bwa mem data/ref_genome/ecoli_rel606.fasta data/trimmed_fastq_small/SRR2584866_1.trim.sub.fastq data/trimmed_fastq_small/SRR2584866_2.trim.sub.fastq > results/sam/SRR2584866.aligned.sam 
```

The correct output should look like this:

```
[main] Version: 0.7.17-r1188
[main] CMD: bwa mem data/ref_genome/ecoli_rel606.fasta data/trimmed_fastq_small/SRR2584866_1.trim.sub.fastq data/trimmed_fastq_small/SRR2584866_2.trim.sub.fastq
[main] Real time: 10.141 sec; CPU: 10.094 sec
```

You can inspect the SAM file we got by using the ```head``` command. 

### SAM/BAM format

SAM stands for Sequence Alignment Map. It is a tab-delimited text file that stores information for each individual read and its alignment to the genome. Most often it is generated as a human readable version of its sister BAM format, which stores the same data in a compressed, indexed, binary form. BAM enables efficient random access of the data contained within the file. 

SAM file begins with a head, although it is optional. The header is used to describe the source of data, reference genome, method of alignment etc. Following the header is the alignment section, each line corresponds to alignment information for a single read. Each alignment line has 11 mandatory fields for essential mapping information, and a variable number of other fields for aligner specific informaiton. 

An example line of the SAM format is displayed below:

![sam-photo1](https://datacarpentry.org/wrangling-genomics/img/sam_bam.png)
![sam-photo2](https://datacarpentry.org/wrangling-genomics/img/sam_bam3.png)

Now, let's convert our SAM file to BAM format. We will use ```samtools``` with ```view``` command and option ```-S``` and ```-b``` to do that. ```-S``` means the input file is in SAM format, ```-b``` means we want the output file to be in BAM format. 

```sh
cd ~/intro_to_linux
samtools view -S -b results/sam/SRR2584866.aligned.sam > results/bam/SRR2584866.aligned.bam 
```

We can also inspect the BAM file we got using command ```head```. 

## Sort BAM file by coordinates 

Next, we sort the BAM file using the ```sort``` command from ```samtools```. ```-o``` option tells the command where to write the output. 

```sh
samtools sort -o results/bam/SRR2584866.aligned.sorted.bam results/bam/SRR2584866.aligned.bam 
```

SAM/BAM files can be sorted in multiple ways, such as by location of alignement, by read name etc. It is important to know that different alignment tools will output differently sorted SAM/BAM files, and different downstream tools require differently sorted SAM/BAM files as input. 

We can use samtools to learn more about this sorted BAM file as well. 

```sh
samtools flagstat results/bam/SRR2584866.aligned.sorted.bam
```

The output should look like this:

```
351169 + 0 in total (QC-passed reads + QC-failed reads)
0 + 0 secondary
1169 + 0 supplementary
0 + 0 duplicates
351103 + 0 mapped (99.98% : N/A)
350000 + 0 paired in sequencing
175000 + 0 read1
175000 + 0 read2
346688 + 0 properly paired (99.05% : N/A)
349876 + 0 with itself and mate mapped
58 + 0 singletons (0.02% : N/A)
0 + 0 with mate mapped to a different chr
0 + 0 with mate mapped to a different chr (mapQ>=5)
```

# Variant Calling 

A variant call is a finding that there is a difference in a single letter of DNA in a sequence compared to the reference genome. The difference is called a Single Nucleotide Variant (SNV). When making a variant call, a tool is used to compare the sample's sequence to a reference genome and determine the frequency of the variant and the confidence in the call. There are many tools available for making variant calls. 

Here, we will use ```bcftools```. There are a few things need to do before the actual calling step. 

![variant-calling-workflow](https://datacarpentry.org/wrangling-genomics/img/variant_calling_workflow.png) 

## Step 1: Calculate the read coverage of positions in the genome 

We will use ```bcftools``` with command ```mpileup``` to generate a file with coverage information for every base. 

```sh
bcftools mpileup -O b \
                -o results/bcf/SRR2584866_raw.bcf \
                -f data/ref_genome/ecoli_rel606.fasta \
                results/bam/SRR2584866.aligned.sorted.bam 
```

* ```-O``` tells bcftools what type of output file you want
* ```-o``` tells bcftools where to write the ouput file
* ```-f``` tells bcftools the path to the reference genome
* the last file is the input file 

NOTE: there might be an error saying: 

```
bcftools: error while loading shared libraries: libcrypto.so.1.0.0: cannot open shared object file: No such file or directory
```

Try ```conda install openssl=1.0``` to solve the problem. 

## Step 2: Detect the single nucleotide vatiants (SNVs)

Using ```bcftools``` with ```call```. 

```sh
bcftools call --ploidy 1 \
                -m -v \
                -o results/vcf/SRR2584866_variants.vcf \
                results/bcf/SRR2584866_raw.bcf
```

* ```--ploidy``` to specify how many ploidy does this genome has
* ```-m``` allows for multiallelic and rare-variant calling
* ```-v``` tells the program to output variant site only (not every site in the genome)
* ```-o``` specifies where to write the output file
* the last one is the path to the input file

## Step 3: Filter and report the SNVs in variant calling format (VCF)

Filtering short/false positive SNVs for the final output in VCF format using ```vcfutils.pl```:

```sh
vcfutils.pl varFilter results/vcf/SRR2584866_variants.vcf > results/vcf/SRR2584866_final_variants.vcf 
```

## Explore the VCF format 

```sh
less -S results/vcf/SRR2584866_final_variants.vcf 
```

You will see information such as the file format, the version of bcftools used, the command line that has been used, and some other information in the beginning of the vcf file. 

Then, following that there are variants information:

```
#CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO    FORMAT  results/bam/SRR2584866.aligned.sorted.bam
CP000819.1      1521    .       C       T       207     .       DP=9;VDB=0.993024;SGB=-0.662043;MQSB=0.974597;MQ0F=0;AC=1;AN=1;DP4=0,0,4,5;MQ=60        GT:PL   1:237,0
CP000819.1      1612    .       A       G       225     .       DP=13;VDB=0.52194;SGB=-0.676189;MQSB=0.950952;MQ0F=0;AC=1;AN=1;DP4=0,0,6,5;MQ=60        GT:PL   1:255,0
CP000819.1      9092    .       A       G       225     .       DP=14;VDB=0.717543;SGB=-0.670168;MQSB=0.916482;MQ0F=0;AC=1;AN=1;DP4=0,0,7,3;MQ=60       GT:PL   1:255,0
CP000819.1      9972    .       T       G       214     .       DP=10;VDB=0.022095;SGB=-0.670168;MQSB=1;MQ0F=0;AC=1;AN=1;DP4=0,0,2,8;MQ=60      GT:PL   1:244,0
CP000819.1      10563   .       G       A       225     .       DP=11;VDB=0.958658;SGB=-0.670168;MQSB=0.952347;MQ0F=0;AC=1;AN=1;DP4=0,0,5,5;MQ=60       GT:PL   1:255,0
CP000819.1      22257   .       C       T       127     .       DP=5;VDB=0.0765947;SGB=-0.590765;MQSB=1;MQ0F=0;AC=1;AN=1;DP4=0,0,2,3;MQ=60      GT:PL   1:157,0
CP000819.1      38971   .       A       G       225     .       DP=14;VDB=0.872139;SGB=-0.680642;MQSB=1;MQ0F=0;AC=1;AN=1;DP4=0,0,4,8;MQ=60      GT:PL   1:255,0
CP000819.1      42306   .       A       G       225     .       DP=15;VDB=0.969686;SGB=-0.686358;MQSB=1;MQ0F=0;AC=1;AN=1;DP4=0,0,5,9;MQ=60      GT:PL   1:255,0
CP000819.1      45277   .       A       G       225     .       DP=15;VDB=0.470998;SGB=-0.680642;MQSB=0.95494;MQ0F=0;AC=1;AN=1;DP4=0,0,7,5;MQ=60        GT:PL   1:255,0
CP000819.1      56613   .       C       G       183     .       DP=12;VDB=0.879703;SGB=-0.676189;MQSB=1;MQ0F=0;AC=1;AN=1;DP4=0,0,8,3;MQ=60      GT:PL   1:213,0
```

* ```CHROM```: contig location where the variation occurs
* ```POS```: position within the contig where the variation occurs
* ```ID```: a ```.``` until we add annotation information
* ```REF```: reference genotype (forward strand)
* ```ALT```: sample genotype (forward strand) 
* ```QUAL```: Phred-scaled probability that the observed variant exists at this site (higher is better) 
* ```FILTER```: a ```.``` if no quality filters have been applied, PASS if a filter is passed, or the name of the filters this variant failed 

Ideally, the information in the ```QUAL``` column would be enough for us to filter out bad variant calls. But in reality, there are other metrics we need to considerate. 

The last two columns contain the genotypes and can be tricky to decode:

* ```FORMAT```: lists in order the metrics presented in the final column
* ```results```: lists the values associated with those metrics in order 

For our file, the metrics presented are GT:PL.

* ```GT```: the genotype for the sample at this location. For a diploid, the GT field indicates the two alleles carried by the sample, ```0``` for the REF allele, ```1``` for the first ALT allele, ```2``` for the second ALT allele etc. ```0/0``` means homozygous reference, ```0/1``` means heterozygous, and ```1/1``` means homozygous for the alternate allele. 
* ```PL```: the likelihood for the given genotypes

The Broad Institute's [VCF Guide](https://gatk.broadinstitute.org/hc/en-us/articles/360035531692-VCF-Variant-Call-Format) is an excellent place to learn more about the VCF file format. 

## Assess the alignment (visualisation) 

## Viewing with IGV 

# References 

* Data Carpentry - [Data Wrangling and Processing for Genomics](https://datacarpentry.org/wrangling-genomics/)
* Centre for Statistical Genetics - [SAM](https://genome.sph.umich.edu/wiki/SAM) 
* OpenAI - [ChatGPT](https://chat.openai.com/chat) 