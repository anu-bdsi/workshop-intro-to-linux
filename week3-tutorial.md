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
conda install -c bioconda fastqc=0.11.7 
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
conda install openssl=1.0 # only if got error using bcftools 
``` 

## Background 


# Homework

* To understand the fastqc results. 

# References 

* Data Carpentry - [Data Wrangling and Processing for Genomics](https://datacarpentry.org/wrangling-genomics/) 
* OpenAI - [ChatGPT](https://chat.openai.com/chat)

