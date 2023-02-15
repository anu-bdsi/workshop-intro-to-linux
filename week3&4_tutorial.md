# Use conda environment for projects 

It is important we use conda to build the environment for each project because different project requires difference software with different versions. A conda environment can store all the dependent software for a project so it doesn't mixed up with others. 

## Install miniconda 

As we have miniconda installed on Dayhoff, so we don't have to install it. If you would like to install it on your own machine, google how to do it. 

## Set up Channels for Bioconda

Bioconda is a distribution of bioinformatics software packages for the conda package manager. It is designed to provide a simple and convenient way to install and manage bioinformatics software on various platforms, including Linux, macOS, and Windows.

To set up channels for Bioconda, you will need to add the Bioconda channel to your conda configuration. This can be done by running the following command in your terminal:

```sh
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

This will add the Bioconda channel as well as the conda-forge channel to your conda configuration. Once this is done, you should be able to install packages from the Bioconda channel using the conda install command. 

## Create a conda environment for your project

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

# Assessing Read Quality 

## Bioinformatic Workflows 

When working with high-throughput sequencing data, the raw reads you get off of the sequencer will need to pass through a number of different tools in order to generate your final desired output. 

An example of the workflow we will be using for our variant calling analysis is provided below with a brief description of each step.

![example](https://datacarpentry.org/wrangling-genomics/img/variant_calling_workflow.png) 

1. Quality control - Assessing quality using FastQC
2. Quality control - Trimming and/or filtering reads (if necessary)
3. Align reads to reference genome
4. Perform post-alignment clean-up
5. Variant calling

These workflows in bioinformatics adopt a plug-and-play approach in that the output of one tool can be easily used as input to another tool without any extensive configuration. Having standards for data formats is what makes this feasible. Standards ensure that data is stored in a way that is generally accepted and agreed upon within the community. The tools that are used to analyze data at different stages of the workflow are therefore built under the assumption that the data will be provided in a specific format.

## Downloading the Data

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

# Quality Control 

To assess the quality of our sequence reads. 

## How to Read the FASTQ Format File 

* Line 1 - Always begins with an '@' and then information about the read.
* Line 2 - The actual DNA sequence.
* Line 3 - Always begins with a '+' and sometimes the same information as Line 1.
* Line 4 - Has a string of characters which represent the quality scores, must have same number os characters as line 2. 

Here are the quality value characters in left-to-right increasing order of quality. ```!``` is the lowest quality and ```~``` is the highest. 

```
!"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~ 
```

__Note:__ Different sequencing machines use different encoding system, sometimes a ```#``` may not means a poor quality base call. 

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

## Assessing Quality using FastQC 

In real life, you will not be assessing the quality of your reads by visually inspecting your FASTQ files. Rather, you will be using a software program to assess read quality and filter out poor quality reads. We will first use a program called FastQC to visualize the quality of our reads. Later in our workflow, we will use another program to filter out poor quality reads.

FastQC has a number of features which can give you a quick impression of any problems your data may have, so you can take these issues into consideration before moving forward with your analyses. Rather than looking at quality scores for each individual read, FastQC looks at quality collectively across all reads within a sample. The image below shows one FastQC-generated plot that indicates a very high quality sample:

![fastqc example](https://datacarpentry.org/wrangling-genomics/img/good_quality1.8.png)

The x-axis displays the base position in the read, and the y-axis shows quality scores. In this example, the sample contains reads that are 40 bp long. This is much shorter than the reads we are working with in our workflow. For each position, there is a box-and-whisker plot showing the distribution of quality scores for all reads at that position. The horizontal red line indicates the median quality score and the yellow box shows the 1st to 3rd quartile range. This means that 50% of reads have a quality score that falls within the range of the yellow box at that position. The whiskers show the absolute range, which covers the lowest (0th quartile) to highest (4th quartile) values.

For each position in this sample, the quality values do not drop much lower than 32. This is a high quality score. The plot background is also color-coded to identify good (green), acceptable (yellow), and bad (red) quality scores.

Now let’s take a look at a quality plot on the other end of the spectrum.

![fastqc example 2](https://datacarpentry.org/wrangling-genomics/img/bad_quality1.8.png) 

Here, we see positions within the read in which the boxes span a much wider range. Also, quality scores drop quite low into the “bad” range, particularly on the tail end of the reads. The FastQC tool produces several other diagnostic plots to assess sample quality, in addition to the one plotted above.

## Running FastQC

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

## Viewing the FastQC Results 

To view the HTML files, first we need to download it to our local computer because we can't view the HTML files in out Linux system. 

To transfer files from remote computers to local computers, we can use ```scp``` command.

First let's create a new directory on our local computer to store the HTML files. Open a new terminal on your local computer, and then run the code below:

```sh
mkdir -p /mnt/c/Users/u1122333/Desktop/fastqc_html
```

Now we can download the HTML files from the remote computer using ```scp```.

```sh
scp u1122333@dayhoff.rsb.anu.edu.au:~/intro_to_linux/results/fastqc_untrimmed_reads/*.html /mnt/c/Users/u1122333/Desktop/fastqc_html 
```

__Note:__ if you're using a MacOS machine, it is likely that a ```no matches found``` will be displayed. The reason for this is that the wildcard ```*``` is not correctly interpreted. To fix this problem the wildcard needs to be escaped with a ```\```:

```sh
scp u1122333@dayhoff.rsb.anu.edu.au:~/intro_to_linux/results/fastqc_untrimmed_reads/\*.html /mnt/c/Users/u1122333/Desktop/fastqc_html 
```

Alternatively, you can also change the shell to bash by running ```chsh -s /bin/bash```. 

You should see a status output like this after running the ```scp``` command:

```
SRR2584863_1_fastqc.html                      100%  626KB   4.0MB/s   00:00    
SRR2584863_2_fastqc.html                      100%  632KB   4.8MB/s   00:00     
SRR2584866_1_fastqc.html                      100%  631KB   5.2MB/s   00:00     
SRR2584866_2_fastqc.html                      100%  626KB   4.5MB/s   00:00     
SRR2589044_1_fastqc.html                      100%  626KB   5.1MB/s   00:00     
SRR2589044_2_fastqc.html                      100%  627KB   5.5MB/s   00:00
```

Now we can go to our new directory and open the 6 HTML files. 

## Decoding Other FastQC Outputs 

We have now looked at quite a few "Per base sequence quality" FastQC graphs, but there are nine other graphs that we have not talked about yet. Please see the FastQC [documentation](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/) for detailed explanation of each graph. 

## Working with the FastQC Text Output 

Now let's look at the other output files. Go back to the terminal that connected to Dayhoff and make sure you are in the results directory. 

```sh
cd ~/intro_to_linux/results/fastqc_untrimmed_reads/
ls 
```

Output:

```
SRR2584863_1_fastqc.html  SRR2584866_1_fastqc.html  SRR2589044_1_fastqc.html
SRR2584863_1_fastqc.zip   SRR2584866_1_fastqc.zip   SRR2589044_1_fastqc.zip
SRR2584863_2_fastqc.html  SRR2584866_2_fastqc.html  SRR2589044_2_fastqc.html
SRR2584863_2_fastqc.zip   SRR2584866_2_fastqc.zip   SRR2589044_2_fastqc.zip 
```

The ```.zip``` files contain multiple different types of output files for a single input FASTQ file. Let extract them first to see what are these files. We will use the command ```unzip``` to do it, ```unzip``` cannot take multiple input files at once so we'll use a for loop to iterate through the list. 

```sh
for filename in *.zip
do 
    unzip $filename 
done 
```

The ```unzip``` program is decompressing the ```.zip``` files and creating a new directory (with subdirectories) for each of our samples, to store all of the different output that is produced by FastQC. There are a lot of files here. The one we are going to focus on is the ```summary.txt``` file.

Let's see what files are present within one of these output directories. 

```sh
ls SRR2584863_1_fastqc/
```

Use ```less``` to preview the ```summary.txt``` file for this sample:

```sh
less SRR2584863_1_fastqc/summary.txt 
```

You will get something look like this:

```
PASS    Basic Statistics        SRR2584863_1.fastq
PASS    Per base sequence quality       SRR2584863_1.fastq
PASS    Per tile sequence quality       SRR2584863_1.fastq
PASS    Per sequence quality scores     SRR2584863_1.fastq
WARN    Per base sequence content       SRR2584863_1.fastq
WARN    Per sequence GC content SRR2584863_1.fastq
PASS    Per base N content      SRR2584863_1.fastq
PASS    Sequence Length Distribution    SRR2584863_1.fastq
PASS    Sequence Duplication Levels     SRR2584863_1.fastq
PASS    Overrepresented sequences       SRR2584863_1.fastq
WARN    Adapter Content SRR2584863_1.fastq
```

The summary gives us a list of tests that FastQC ran, and tells us wether this sample passes, failed, or is borderline (```WARN```). 

## Documenting the work 

We can make a record of the results we obtained for all of the samples by concatenating all of the ```summary.txt``` files into a single file using the ```cat``` command, and name it as ```fastqc_summaries.txt``` and move it to the ```~/intro_to_linux/docs``` directory. 

```sh
mkdir ~/intro_to_linux/docs
cat */summary.txt > ~/intro_to_linux/docs/fastqc_summaries.txt 
```

__Exercise:__ Which sample failed at least one of FastQC's quality test? What test did those samples fail? 

We can get the list of all failed tests using ```grep```:

```sh
cd ~/intro_to_linux/docs
grep FAIL fastqc_summaries.txt 
```

# Trimming and Filtering 

## Cleaning Reads

Previously, we checked the quality of our samples using a tool called FastQC. We looked at graphs that showed the quality of each base in the samples, and found out which samples failed certain quality checks. Just because some samples failed some checks, it doesn't mean we should get rid of them. It's normal for some checks to fail and it might not be a problem for what we want to do with the samples. For our next step, we will remove some of the low quality sequences to lower the chance of getting false results due to errors in the sequencing. 

We will use a program called ```Trimmomatic``` to filter poor quality reads and trim poor quality bases from our samples. 

## Trimmomatic Options

Trimmomatic has a variety of options to trim your reads. If we run the following command, we can see some of our options. 

```sh
trimmomatic
```

Which will give you the following output:

```
Usage:
       PE [-version] [-threads <threads>] [-phred33|-phred64] [-trimlog <trimLogFile>] [-summary <statsSummaryFile>] [-quiet] [-validatePairs] [-basein <inputBase> | <inputFile1> <inputFile2>] [-baseout <outputBase> | <outputFile1P> <outputFile1U> <outputFile2P> <outputFile2U>] <trimmer1>...
   or:
       SE [-version] [-threads <threads>] [-phred33|-phred64] [-trimlog <trimLogFile>] [-summary <statsSummaryFile>] [-quiet] <inputFile> <outputFile> <trimmer1>...
   or:
       -version
```

To use it, you first need to specify whether you're using paired end (PE) or single end (SE) reads. Then, you can specify optional settings called flags, such as the number of processors to use. These flags can give you more control over the command, but they're not required. After the flags, you need to specify the input and output files. For paired end mode, you need to provide two input files and two output files. For single end mode, you need to provide one input file, optional settings, and one output file. The order in which you specify these arguments is important. 

| Option | Meaning |
|--------|---------|
| \<inputFile1> | Input reads to be trimmed. Typically the file name will contain an ```_1``` or ```_R1``` in the name. | 
| \<inputFile2> | Input reads to be trimmed. Typically the file name will contain an ```_2``` or ```_R2``` in the name. | 
| \<outputFile1P> | Output file that contains surviving pairs from the ```_1``` file. | 
| \<outputFile1U> | Output file that contains orphaned reads from the ```_1``` file. |
| \<outputFile2P> | Output file that contains surviving pairs from the ```_2``` file. |
| \<outputFile2U> | Output file that contains orphaned reads from the ```_2``` file. | 

The last thing trimmomatic expects to see is the trimming parameters:

| Step | Meaning |
| ---- | ------- |
| ```ILLUMINACLIP``` | Perform adapter removal. |
| ```SLIDINGWINDOW``` | Perform sliding window trimming, cutting once the average quality within the window falls below a threshold. | 
| ```LEADING``` | Cut bases off the start of a read, if below a threshold quality. |
| ```TRAILING``` | Cut bases off the end of a read, if below a threshold quality. |
| ```CROP``` | Cut the read to a specified length |
| ```HEADCROP``` | Cut the specified number of bases from the start of the read. | 
| ```MINLEN``` | Drop an entire read if it is below a specified length. |
| ```TOPHRED33``` | Convert quality scores to Phred-33. |
| ```TOPHRED64``` | Convert quality scores to Phred-64. |

We will only use a few options and trimming steps in our analysis, it is important to understand the steps you use to clean your data. For more information, see [the Trimmomatic manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf). 

A complete Trimmomatic command example will look like the following code: 

```sh
trimmomatic PE -threads 4 SRR_1056_1.fastq SRR_1056_2.fastq \
            SRR_1056_1.trimmed.fastq SRR_1056_1un.trimmed.fastq \
            SRR_1056_2.trimmed.fastq SRR_1056_2un.trimmed.fastq \
            ILLUMINACLIP:SRR_adapters.fa SLIDINGWINDOW:4:20 
```

__Note:__ When writing a very long code, we can use ```\``` to separate long codes to different lines so it is easier to read. 

# Running Trimmomatic 

Let's start running Trimmomatic on our data. First, let's navigate to the ```untrimmed_fastq``` folder. 

```sh
cd ~/intro_to_linux/data/untrimmed_fastq
```

Our data are pair-ended data, we can see it from the name of our data. In the reports of FastQC, we can see that the Nextera adapters are present in our samples. The adpater sequences came in with the installation of the software so we can copy them from the installation path to our folder or we can use it directly from the installation folder by using the full path. 

For now, we will copy the adapter sequence to our ```untrimmed_fastq``` folder to use it. 

```sh
cp ~/.conda/pkgs/trimmomatic-0.38-1/share/trimmomatic-0.38-1/adapters/NexteraPE-PE.fa .
```

The code we are going to run for Trimmomatic as follow, it may take a few minutes to run: 

```sh
trimmomatic PE SRR2589044_1.fastq.gz SRR2589044_2.fastq.gz \
                SRR2589044_1.trim.fastq.gz SRR2589044_1un.trim.fastq.gz \
                SRR2589044_2.trim.fastq.gz SRR2589044_2un.trim.fastq.gz \
                SLIDINGWINDOW:4:20 MINLEN:25 ILLUMINACLIP:NexteraPE-PE.fa:2:40:15
```

We are using the option ```SLIDINGWINDOW:4:20``` for our data, it means the size of the sliding window is 4 bases long and any windows that has a score lower than 20 will be removes from the read. And the option ```MINLEN:25```, it means after the sliding window step, if the total length of the read are shorter than 25 bases will be removed. Additionally, we use the option ```ILLUMINACLIP:NexteraPE-PE.fa:2:40:15```, it tells the software how to handle sequences that match with adapter sequence but we won't cover how it works in this course, we just use it as a default setting. For more information, you can see [the Trimmomatic manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf). 


The correct output should look like this:

```
TrimmomaticPE: Started with arguments:
 SRR2589044_1.fastq.gz SRR2589044_2.fastq.gz SRR2589044_1.trim.fastq.gz SRR2589044_1un.trim.fastq.gz SRR2589044_2.trim.fastq.gz SRR2589044_2un.trim.fastq.gz SLIDINGWINDOW:4:20 MINLEN:25 ILLUMINACLIP:NexteraPE-PE.fa:2:40:15
Using PrefixPair: 'AGATGTGTATAAGAGACAG' and 'AGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'GTCTCGTGGGCTCGGAGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'TCGTCGGCAGCGTCAGATGTGTATAAGAGACAG'
Using Long Clipping Sequence: 'CTGTCTCTTATACACATCTCCGAGCCCACGAGAC'
Using Long Clipping Sequence: 'CTGTCTCTTATACACATCTGACGCTGCCGACGA'
ILLUMINACLIP: Using 1 prefix pairs, 4 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Quality encoding detected as phred33
Input Read Pairs: 1107090 Both Surviving: 885220 (79.96%) Forward Only Surviving: 216472 (19.55%) Reverse Only Surviving: 2850 (0.26%) Dropped: 2548 (0.23%)
TrimmomaticPE: Completed successfully 
```

__Exercise:__ What percent of reads did we remove from out sample? and what percent did we keep for both pairs? 

From the results we got, you may have noticed that Trimmomatic has automatically detected the encoding method of our data, but it is always good to double check if it is correct. 

We can check our output files by using ```ls SRR2589044*```, the correct output should look like this:

```
SRR2589044_1.fastq.gz       SRR2589044_1un.trim.fastq.gz  SRR2589044_2.trim.fastq.gz
SRR2589044_1.trim.fastq.gz  SRR2589044_2.fastq.gz         SRR2589044_2un.trim.fastq.gz
```

The output files are also FASTQ files but it should be smaller than the original file since we have removed some reads. We can confirm this by running ```ls -lh SRR2589044*```. The correct output should look like this:

```
-rw-rw-r-- 1 u1122333 domain users 124M Jan 15  2016 SRR2589044_1.fastq.gz
-rw-rw-r-- 1 u1122333 domain users  94M Jan 24 15:22 SRR2589044_1.trim.fastq.gz
-rw-rw-r-- 1 u1122333 domain users  18M Jan 24 15:22 SRR2589044_1un.trim.fastq.gz
-rw-rw-r-- 1 u1122333 domain users 128M Jan 15  2016 SRR2589044_2.fastq.gz
-rw-rw-r-- 1 u1122333 domain users  91M Jan 24 15:22 SRR2589044_2.trim.fastq.gz
-rw-rw-r-- 1 u1122333 domain users 271K Jan 24 15:22 SRR2589044_2un.trim.fastq.gz
```

Now, we have successfully run Trimmomatic on one of our files. However, Trimmomatic can only work on one sample at a time so we have to use ```for``` loop to iterate the command over our files. 

We have unzipped one of the files before, let's zip it again so all of our files have universal names. 

```sh
gzip SRR2584863_1.fastq 
```

The ```for``` loop to run Trimmomatic on all samples:

```sh
for infile in *_1.fastq.gz 
do 
    base=$(basename ${infile} _1.fastq.gz)
    trimmomatic PE ${infile} ${base}_2.fastq.gz \
                    ${base}_1.trim.fastq.gz ${base}_1un.trim.fastq.gz \
                    ${base}_2.trim.fastq.gz ${base}_2un.trim.fastq.gz \
                    SLIDINGWINDOW:4:20 MINLEN:25 ILLUMINACLIP:NexteraPE-PE.fa:2:40:15
done 
```

__Exercise:__ What does the command ```basename``` do? 

It may take more than a few minutes to run, once it finishes running, take a look at your folder. You should get output files for each of our samples. 

__Exercise:__ We trimmed our fastq files with Nextera adapters, but there are other adapters that are commonly used. What other adapter files came with Trimmomatic? 

Now, let's move all the trimmed files to a new folder ```trimmed_fastq```.

```sh
mkdir ~/intro_to_linux/data/trimmed_fastq
cd ~/intro_to_linux/data/untrimmed_fastq
mv *.trim.* ../trimmed_fastq
cd ../trimmed_fastq
ls 
```

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

__Note:__: there might be an error saying: 

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

__Exercise:__ Use the ```grep``` and ```wc``` commands to assess how many variants are in the vcf file?

```sh
grep -v "#" results/vcf/SRR2584866_final_variants.vcf | wc -l 
```

The correct output should be ```766```, there are 766 variants in this file. 

# Automating a Variant Calling Workflow 

## What is a shell script?

A shell script is a program written in the shell programming language for Unix-based operating systems, which automates commands and tasks. It is interpreted by the shell and can contain shell commands, functions, variables, and control structures.

With shell scripts, we can put all the steps for variant calling into a single script and run it all at once. We don't have to type commands in each step and wait for the program to finish for the next step. 

In this lesson, we will use two shell scripts for the variant calling workflow. One for FastQC analysis, and the other one for the remaining steps.  

### Creating Variables 

Within the Bash shell, you can create variables at any time. To create a variable, you can use the assignment operator ```=``` to assign values to a name. Such as ```lucky_number=5```, you assigned ```5``` to the name ```lucky_number```. To check the current defination of a variable, you can use ```echo $lucky_number```. 

## Analysing quality with FastQC 

First, let's create a new direcotory ```scripts``` to store our scripts, and a file ```read_qc.sh``` for the FastQC analysis. 

```sh
mkdir -p ~/intro_to_linux/scripts 
cd ~/intro_to_linux/scripts 
touch read_qc.sh 
```

We can use ```nano``` to open our file and edit it. 

```sh
nano read_qc.sh
```

After you're in the ```nano``` interface, input the following code into your script:

```sh
#!/bin/bash

set -e 
cd ~/intro_to_linux/data/untrimmed_fastq 

echo "Running FastQC ..." 
fastqc *.fastq* 

mkdir -p ~/intro_to_linux/results/fastqc_untrimmed_reads 

echo "Saving FastQC results ..." 
mv *.zip ~/intro_to_linux/results/fastqc_untrimmed_reads 
mv *.html ~/intro_to_linux/results/fastqc_untrimmed_reads 

cd ~/intro_to_linux/results/fastqc_untrimmed_reads 

echo "Unzipping ..."
for file in *.zip
do
    unzip $file 
done 

echo "Saving summary ..."
cat */summary.txt > ~/intro_to_linux/docs/fastqc_summaries.txt 
```

Save and exit ```nano```. 

Now, we can run our script file by:

```sh 
bash read_qc.sh 
```

In the process of running, FastQC will ask if you want to replace some files. That's because we have run the FastQC before, so we have the results already. Go ahead and press ```A``` + ```enter``` each time it occurs. 

## Automating the rest of our variant calling workflow 

Create a new file called ```variant_calling.sh```:

```sh
nano variant_calling.sh 
```

and input the following codes:

```sh
#!/bin/bash 

set -e
cd ~/intro_to_linux/results

genome=~/intro_to_linux/data/ref_genome/ecoli_rel606.fasta 
bwa index $genome 

mkdir -p sam bam bcf vcf 

for fq1 in ~/intro_to_linux/data/trimmed_fastq_small/*_1.trim.sub.fastq 
do 
    echo "Working with file $fq1"
    base=$(basename $fq1 _1.trim.sub.fastq) 
    echo "base name is $base" 

    fq1=~/intro_to_linux/data/trimmed_fastq_small/${base}_1.trim.sub.fastq 
    fq2=~/intro_to_linux/data/trimmed_fastq_small/${base}_2.trim.sub.fastq 
    sam=~/intro_to_linux/results/sam/${base}.aligned.sam
    bam=~/intro_to_linux/results/bam/${base}.aligned.bam
    sorted_bam=~/intro_to_linux/results/bam/${base}.aligned.sorted.bam 
    raw_bcf=~/intro_to_linux/results/bcf/${base}_raw.bcf 
    variants=~/intro_to_linux/results/vcf/${base}_variants.vcf 
    final_variants=~/intro_to_linux/results/vcf/${base}_final_variants.vcf 

    bwa mem $genome $fq1 $fq2 > $sam
    samtools view -S -b $sam > $bam 
    samtools sort -o $sorted_bam $bam 
    samtools index $sorted_bam 
    bcftools mpileup -O b -o $raw_bcf -f $genome $sorted_bam 
    bcftools call --ploidy 1 -m -v -o $variants $raw_bcf 
    vcfutils.pl varFilter $variants > $final_variants 
done 
```

Save and exit, now you can run the workflow by:

```sh
bash variant_calling.sh 
```

__Exercise:__ The samples we just did variant calling on are part of the long-term evolution. The ```SRR2589044``` sample was from generation 5000, ```SRR2584863``` was from generation 15000, and ```SRR2584866``` was from generation 50000. How did the number of mutations change in the sample over time? 

```sh
for file in ~/intro_to_linux/results/vcf/*_final_variants.vcf 
do 
    echo ${file}
    grep -v "#" ${file} | wc -l
done 
```

For ```SRR2589044``` from generation 5000 there were 10 mutations, for ```SRR2584863``` from generation 15000 there were 25 mutations, and ```SRR2584866``` from generation 766 mutations.  

# Use SLURM to run your jobs on RSB IT infrastructure  

Slurm is an open source, fault-tolerant, and highly scalable cluster management and job scheduling system for large and small Linux clusters. It allows users to allocate resources for their jobs rather than taking up whatever resources available. It also let you run jobs in the background rather than looking at the screen all the time. 

## Data storage locations 

There are three nodes on Dayhoff: ```dayhoff```, ```wright```, and ```fisher```. 

If you run ```pwd``` in your home directory, you'll probably see something like ```/home/UID```. But in reality, that's only the path from your home node. For cluster-wide shared data namespace, we have to add ```/mnt/data/(server)``` before the path we get from ```pwd```. 

## Writing a ```sbatch``` file

A few things to remember when submitting a SLURM job on the cluster:

* All medium to large processes or workloads need to be run via SLURM, not directly on the command line. If the job was run directly it will be terminated after a short time. 
* With a multinode cluster all the tools you need to use must be installed on all the nodes, and all the file paths need to use the cluster-wide shared data namespace with ```/mnt/data/(server)``` at front. 
* You can limit your job only running on one cluster if you don't want to set up your environment on all nodes. In ```sbatch``` and ```srun```, you can use ```--exclude=fisher, wright``` to remove the nodes you don't want to use. 
* The cluster has only one partition ```standard``` for using right now, but it will have more options in the future for urgent and GPU-intensive workloads. 
* When using multiple cores for a job, first you need to make sure the software you are using supports multi-core processing. Secondly, make sure you specifies the number of cores you want in the codes to run the software as well in the ```sbatch``` file. Sometimes SLURM and software cannot communicate very well so they don't know information from each other. 

A example of what a ```sbatch``` script looks like:

```sh
#!/bin/bash 
#SBATCH --job-name=JobX
#SBATCH --output=/mnt/data/(server)/home/uxxxxx/../%j.%x.out
#SBATCH --error=/mnt/data/(server)/home/uxxxxx/.../%j.%x.err
#SBATCH --partition=Standard
#SBATCH --exclude=wright,fisher 
#SBATCH --time=120:00:00    # 5 days then stop job if not complete
#SBATCH --mem-per-cpu=7000  # 7GB per cpu (rather than per node)
#SBATCH --nodes=2	    # use 2 nodes
#SBATCH --ntasks=Y	    # don't let more than Y tasks run at once
#SBATCH --mem=230G	    # reserve 230GB RAM per node (rather than per cpu)
#SBATCH --cpus-per-task=15  # reserve 15 cpus/threads per task
#SBATCH --ntasks-per-node=Z # only allow z tasks per node
#SBATCH --mail-user uxxxxxxx@anu.edu.au # mail user on job state changes
#SBATCH --mail-type TIME_LIMIT,FAIL		# state changes

srun my_script_1.sh & # each srun means one task 
srun -Nx -ny --exclusive my_script_A.sh -input a & # -N means --nodes, -n means --ntasks 
srun -Nx -ny --exclusive my_script_A.sh -input b &
..
..
srun -Nx -ny --exclusive my_script_B.sh &
wait
```

It has more options you can use to reserve the resources and manage the job, for more information you can see the [slurm documentation](https://slurm.schedmd.com/overview.html). 

## Parallel Processing 

In the sbatch script above, each srun means a task and it will run parallel with each other. 

In the variant calling workflow, we used 3 different samples, we can write a script to make the 3 samples run in parallel to save some time. 

Remember in the step of aligning reads to referece genome we only used the subset data, now let's try align the full-sized data to the reference genome using parallel processing. 

Create a file named ```run_bwa.sh``` in the directory ```scripts```, and input the following codes. 

```sh
#!/bin/bash

#SBATCH --job-name=alignment
#SBATCH --output=/mnt/data/dayhoff/home/u_id/intro_to_linux/alignment.out
#SBATCH --error=/mnt/data/dayhoff/home/u_id/intro_to_linux/alignment.err
#SBATCH --partition=Standard
#SBATCH --exclude=wright,fisher
#SBATCH --time=5:00:00
#SBATCH --mem-per-cpu=1000
#SBATCH --nodes=1
#SBATCH --ntasks=3
#SBATCH --cpus-per-task=2
#SBATCH --ntasks-per-node=3
#SBATCH --mail-user=email_address
#SBATCH --mail-type=ALL

source /opt/conda/bin/activate intro-to-linux 

srun --exclusive --ntasks=1 bwa mem -t 2 \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/ref_genome/ecoli_rel606.fasta \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/trimmed_fastq/SRR2584863_1.trim.fastq \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/trimmed_fastq/SRR2584863_2.trim.fastq \
            > /mnt/data/dayhoff/home/u_id/intro_to_linux/results/sam/SRR2584863.full.aligned.sam &

srun --exclusive --ntasks=1 bwa mem -t 2 \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/ref_genome/ecoli_rel606.fasta \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/trimmed_fastq/SRR2584866_1.trim.fastq \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/trimmed_fastq/SRR2584866_2.trim.fastq \
            > /mnt/data/dayhoff/home/u_id/intro_to_linux/results/sam/SRR2584866.full.aligned.sam &

srun --exclusive --ntasks=1 bwa mem -t 2 \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/ref_genome/ecoli_rel606.fasta \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/trimmed_fastq/SRR2589044_1.trim.fastq \
            /mnt/data/dayhoff/home/u_id/intro_to_linux/data/trimmed_fastq/SRR2589044_2.trim.fastq \
            > /mnt/data/dayhoff/home/u_id/intro_to_linux/results/sam/SRR2589044.full.aligned.sam &

wait
```

Save ane exit, run ```sbatch run_bwa.sh``` to submit the job. You'll see a job ID prompted on your screen. It means the job has been submitted. Then, you can run ```squeue``` to check your job status. 

Another useful command to check how much resources have been used for your job is:

```sh
sacct --format=JobID,JobName,State,Start,End,CPUTime,MaxRSS,NodeList,ExitCode --jobs=JOB_ID 
```

# Homework

* Run FastQC on trimmed fastq files, and see if the results got better? 
* Add comments on the two shell scripts we ran in today's exercise. 
* Run the variant calling workflow on the full-sized trimmed fastq file, and see if the number of variants has changed?

# References 

* Data Carpentry - [Data Wrangling and Processing for Genomics](https://datacarpentry.org/wrangling-genomics/) 
* OpenAI - [ChatGPT](https://chat.openai.com/chat)
* Wikipedia - [FASTQ format](https://en.wikipedia.org/wiki/FASTQ_format) 
* Centre for Statistical Genetics - [SAM](https://genome.sph.umich.edu/wiki/SAM) 
* slurm workload manager - [Documentation](https://slurm.schedmd.com/documentation.html) 
* RSB IT Infrastructure Wiki - [RSB bioinformatics server user guide](https://infrawiki.rsb.anu.edu.au/doku.php?id=rsb_it:infrastructure:userdoco:studentservers#running_workloads_and_processes_on_the_servers)
* RONIN - [A simple slurm guide for beginners](https://blog.ronin.cloud/slurm-intro/)
* stack overflow - [parallel but different Slurm job step invocations not working](https://stackoverflow.com/questions/35498763/parallel-but-different-slurm-srun-job-step-invocations-not-working) 