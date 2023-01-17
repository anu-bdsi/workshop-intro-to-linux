# Use conda environment for projects 

## Install miniconda 

As we have miniconda installed on Dayhoff, so we don't have to install it. If you would like to install it on your own machine, google how to do it or ask ChatGPT. 

## Create a conda environment for your project

It is important we use conda to build the environment for each project because different project requires difference software with different versions. A conda environment can restore all the dependent software for a project so it doesn't mixed up with others. 

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

* LibreOffice 7.3.7 (install on official website)
* FastQC 0.11.7
* Trimmomatic 0.38
* BWA 0.7.17
* SAMtools 1.9
* BCFtools 1.8
* IGV 

## Background 


