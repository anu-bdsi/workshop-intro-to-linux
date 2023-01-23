# Use conda environment for projects 

## Install miniconda 

As we have miniconda installed on Dayhoff, so we don't have to install it. If you would like to install it on your own machine, google how to do it or ask ChatGPT. 

## Set up Channels for Bioconda

Bioconda is a distribution of bioinformatics software packages for the conda package manager. It is designed to provide a simple and convenient way to install and manage bioinformatics software on various platforms, including Linux, macOS, and Windows.

To set up channels for Bioconda, you will need to add the Bioconda channel to your conda configuration. This can be done by running the following command in your command prompt or terminal:

```sh
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

This will add the Bioconda channel as well as the conda-forge channel to your conda configuration. Once this is done, you should be able to install packages from the Bioconda channel using the conda install command. 

## Create a conda environment for your project

It is important we use conda to build the environment for each project because different project requires difference software with different versions. A conda environment can store all the dependent software for a project so it doesn't mixed up with others. 

Use the following code to create a new conda environment for your first genomics project.

```sh
conda create --name intro-to-linux
```

Type ```yes``` to proceed when prompted with questions.

After you succesfully created an environment, activate it by:

```sh
conda activate intro-to-linux
```

After that, you will see there is your environment name with a pair of brackets at the beginning of the prompt. It means now you are in the (intro-to-linux) conda environment. 

Now, you can start to install software. 

# Data Wrangling and Processing for Genomics 

## Required Software

* FastQC 0.11.7

```sh
conda install -c bioconda fastqc=0.11.7=5
```

* Trimmomatic 0.38

```sh
conda install -c bioconda trimmomatic=0.38
```

* BWA 0.7.17

```sh
conda install -c bioconda bwa=0.7.17 
```

* SAMtools 1.9

```sh
conda install -c bioconda samtools=1.9
```

* BCFtools 1.8

```sh
conda install -c bioconda bcftools=1.8
``` 

* Some other installation when error occurs

```sh
conda install -c conda-forge openjdk # when using fastqc 
conda install openssl=1.0 # when using bcftools
```


## The Data 

The data we are going to use is part of a long-term evolution experiment led by [Richard Lenski](https://en.wikipedia.org/wiki/E._coli_long-term_evolution_experiment).

The experiment was designed to assess adaptation in E. coli. A population was propagated for more than 40,000 generations in a glucose-limited minimal medium (in most conditions glucose is the best carbon source for E. coli, providing faster growth than other sugars). This medium was supplemented with citrate, which E. coli cannot metabolize in the aerobic conditions of the experiment. Sequencing of the populations at regular time points revealed that spontaneous citrate-using variant (Cit+) appeared between 31,000 and 31,500 generations, causing an increase in population size and diversity. In addition, this experiment showed hypermutability in certain regions. Hypermutability is important and can help accelerate adaptation to novel environments, but also can be selected against in well-adapted populations.

## Assessing Read Quality 

### Bioinformatic Workflows 

When working with high-throughput sequencing data, the raw reads you get off of the sequencer will need to pass through a number of different tools in order to generate your final desired output. 

An example of the workflow we will be using for our variant calling analysis is provided below with a brief description of each step.

![example](https://datacarpentry.org/wrangling-genomics/img/variant_calling_workflow.png) 

1. Quality control - Assessing quality using FastQC
2. Quality control - Trimming and/or filtering reads (if necessary)
3. Align reads to reference genome
4. Perform post-alignment clean-up
5. Variant calling

These workflows in bioinformatics adopt a plug-and-play approach in that the output of one tool can be easily used as input to another tool without any extensive configuration. Having standards for data formats is what makes this feasible. Standards ensure that data is stored in a way that is generally accepted and agreed upon within the community. The tools that are used to analyze data at different stages of the workflow are therefore built under the assumption that the data will be provided in a specific format.

### Downloading the Data

We are studying a population of *Escherichia coli* (designated Ara-3), which were propagated for more than 50,000 generations in a glucose-limited minimal medium. We will be working with three samples from this experiment, one from 5,000 generations, one from 15,000 generations, and one from 50,000 generations. The population changed substantially during the course of the experiment, and we will be exploring how with our variant calling workflow.

The ```-p``` option for ```mkdir``` allows it to create the new directory, even if one of the parent directories does not already exist. It also supresses errors if the directory already exists, without overwriting that directory.

The data are paired-end, so we have two files for each sample. The files are big and it takes some time to download, I have it downloaded already so you can copy it to your folder using commands below. 

```sh 
mkdir -p ~/intro_to_linux/data/untrimmed_fastq/
cd ~/intro_to_linux/data/untrimmed_fastq
cp /mnt/data/dayhoff/home/u1133824/intro_to_linux/data/untrimmed_fastq/*.gz . 
```

The data comes in a compressed format, which is why there is a ```.gz``` at the end of the file names. This makes it faster to transfer, and allows it to take up less space on our computer. Let’s unzip one of the files so that we can look at the fastq format. 

```sh
gunzip SRR2584863_1.fastq.gz
```

### Quality Control 

To assess the quality of our sequence reads. 

#### How to Read the FASTQ Format File 

* Line 1 - Always begins with an '@' and then information about the read.
* Line 2 - The actual DNA sequence.
* Line 3 - Always begins with a '+' and sometimes the same information as Line 1.
* Line 4 - Has a string of characters which represent the quality scores, must have same number os characters as line 2. 

Here are the quality value characters in left-to-right increasing order of quality. ```!``` is the lowest quality and ```~``` is the highest. 

```
!"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~ 
```

The numerical value assigned to each of these characters depends on the sequencing platform that generated the reads. The sequencing machine used to generate our data uses the standard Sanger quality PHRED score encoding, using Illumina version 1.8 onwards. Each character is assigned a quality score between 0 and 41 as shown in the chart below. 

We can view the first complete read in one of the files our dataset by using ```head``` to look at the first four lines.

```sh
head -n 4 SRR2584863_1.fastq
``` 

__Exercise:__ What is the last read in the ```SRR2584863_1.fastq``` file? How confident are you in this read?

Now, let's validate if the ```fastqc``` tool has been successfully installed. 

```sh
fastqc -h 
```

If you get the help manual it means the ```fastqc``` has been successfully installed, if you get an error message it means it has not been installed. 

### Assessing Quality using FastQC 

In real life, you will not be assessing the quality of your reads by visually inspecting your FASTQ files. Rather, you will be using a software program to assess read quality and filter out poor quality reads. We will first use a program called FastQC to visualize the quality of our reads. Later in our workflow, we will use another program to filter out poor quality reads.

FastQC has a number of features which can give you a quick impression of any problems your data may have, so you can take these issues into consideration before moving forward with your analyses. Rather than looking at quality scores for each individual read, FastQC looks at quality collectively across all reads within a sample. The image below shows one FastQC-generated plot that indicates a very high quality sample:

![fastqc example](https://datacarpentry.org/wrangling-genomics/img/good_quality1.8.png)

The x-axis displays the base position in the read, and the y-axis shows quality scores. In this example, the sample contains reads that are 40 bp long. This is much shorter than the reads we are working with in our workflow. For each position, there is a box-and-whisker plot showing the distribution of quality scores for all reads at that position. The horizontal red line indicates the median quality score and the yellow box shows the 1st to 3rd quartile range. This means that 50% of reads have a quality score that falls within the range of the yellow box at that position. The whiskers show the absolute range, which covers the lowest (0th quartile) to highest (4th quartile) values.

For each position in this sample, the quality values do not drop much lower than 32. This is a high quality score. The plot background is also color-coded to identify good (green), acceptable (yellow), and bad (red) quality scores.

Now let’s take a look at a quality plot on the other end of the spectrum.

![fastqc example 2](https://datacarpentry.org/wrangling-genomics/img/bad_quality1.8.png) 

Here, we see positions within the read in which the boxes span a much wider range. Also, quality scores drop quite low into the “bad” range, particularly on the tail end of the reads. The FastQC tool produces several other diagnostic plots to assess sample quality, in addition to the one plotted above.

### Running FastQC

We will now assess the quality of the reads that we downloaded. First, make sure you are still in the ```untrimmed_fastq``` directory.

```sh
cd ~/intro_to_linux/data/untrimmed_fastq/
```

__Excercise:__ How big are the files? (using ```ls```) 

FastQC can accept multiple file names as input, and on both zipped and unzipped files, so we can use the ```*.fastq*``` wildcard to run FastQC on all of the FASTQ files in this directory.

```sh
fastqc *.fastq* 
```

You will see an automatically updating output message telling you the progress of the analysis. It will start like this:

```
Started analysis of SRR2584863_1.fastq
Approx 5% complete for SRR2584863_1.fastq
Approx 10% complete for SRR2584863_1.fastq
Approx 15% complete for SRR2584863_1.fastq
Approx 20% complete for SRR2584863_1.fastq
Approx 25% complete for SRR2584863_1.fastq
Approx 30% complete for SRR2584863_1.fastq
Approx 35% complete for SRR2584863_1.fastq
Approx 40% complete for SRR2584863_1.fastq
Approx 45% complete for SRR2584863_1.fastq
```

In total, it should take about five minutes for FastQC to run on all six of our FASTQ files. When the analysis completes, your prompt will return. So your screen will look something like this:

```
Approx 80% complete for SRR2589044_2.fastq.gz
Approx 85% complete for SRR2589044_2.fastq.gz
Approx 90% complete for SRR2589044_2.fastq.gz
Approx 95% complete for SRR2589044_2.fastq.gz
Analysis complete for SRR2589044_2.fastq.gz
```

The FastQC program has created several new files within our ```data/untrimmed_fastq/``` directory. Using ```ls``` to have a look. The result will look like this: 

```
SRR2584863_1.fastq        SRR2584866_1_fastqc.html  SRR2589044_1_fastqc.html
SRR2584863_1_fastqc.html  SRR2584866_1_fastqc.zip   SRR2589044_1_fastqc.zip
SRR2584863_1_fastqc.zip   SRR2584866_1.fastq.gz     SRR2589044_1.fastq.gz
SRR2584863_2_fastqc.html  SRR2584866_2_fastqc.html  SRR2589044_2_fastqc.html
SRR2584863_2_fastqc.zip   SRR2584866_2_fastqc.zip   SRR2589044_2_fastqc.zip
SRR2584863_2.fastq.gz     SRR2584866_2.fastq.gz     SRR2589044_2.fastq.gz
```

For each input FASTQ file, FastQC has created a ```.zip``` file and a ```.html``` file. The ```.zip``` file extension indicates that this is actually a compressed set of multiple output files. We will be working with these output files soon. The ```.html``` file is a stable webpage displaying the summary report for each of our samples.

We want to keep our data files and our results files separate, so we will move these output files into a new directory within our ```results/``` directory.

```sh
mkdir -p ~/intro_to_linux/results/fastqc_untrimmed_reads
mv *.zip ~/intro_to_linux/results/fastqc_untrimmed_reads/
mv *.html ~/intro_to_linux/results/fastqc_untrimmed_reads/
```

Now we can navigate into this results directory and do some closer inspection of our output files.

```sh
cd ~/intro_to_linux/results/fastqc_untrimmed_reads/ 
```

### Viewing the FastQC Results 

To view the HTML files, first we need to download it to our local computer because we can't view the HTML files in out Linux system. 

To transfer files from remote computers to local computers, we can use ```scp``` command.

First let's create a new directory on our local computer to store the HTML files. Open a new terminal on your local computer, and then run the code below:

```sh
mkdir -p /mnt/c/Users/u1122333/Desktop/fastqc_html
```

Now we can download the HTML files from the remote computer using ```scp```.

```sh
scp u1122333@dayhoff.rsb.anu.edu.au:~/intro_to_linux/results/fastqc_untrimmed_reads/*.hmtl /mnt/c/Users/u1122333/Desktop/fastqc_html 
```


# Homework

* To understand the fastqc results. 

# References 

* Data Carpentry - [Data Wrangling and Processing for Genomics](https://datacarpentry.org/wrangling-genomics/) 
* OpenAI - [ChatGPT](https://chat.openai.com/chat)
* Wikipedia - [FASTQ format](https://en.wikipedia.org/wiki/FASTQ_format) 
