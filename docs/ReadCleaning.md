# Read Cleaning and Quality Control Practical

## [8th Viral Bioinformatics and Genomics Training Course](https://github.com/centre-for-virus-research/CVR-VGB-2025)
* Monday 16th - Friday 20th June 2025
* Glasgow, UK
* [MRC-University of Glasgow Centre for Virus Research](https://www.gla.ac.uk/research/az/cvr/)

## Contact

[Richard Orton](https://www.gla.ac.uk/schools/infectionimmunity/staff/richardorton/)   
[MRC-University of Glasgow Centre for Virus Research](https://www.gla.ac.uk/research/az/cvr/)  
464 Bearsden Road  
Glasgow  
G61 1QH  
UK  
E-mail: Richard.Orton@glasgow.ac.uk  

## Contents
This practical is associated with a lecture on Read Cleaning and Quality Control reads.

In this practical we are going to be working with two paired-end FASTQ samples, for which we will perform some basic quality control of the reads. There will be step by step instructions for how to process the first sample, and you will then need to apply what you have learned to process the second sample.
 
Sample names:

* U2008751
* U2012100

## 1: Setup and Data

We first need to copy the data folder that we will need for this practical into your own home directory.

First, lets make sure we are in the right location on the server by moving into our home directory:

```
cd ~
```

Now enter the following command to copy the data folder for the practical:

```
cp -r /home4/VBG_data/Richard/QC .
```

Command breakdown:

* **cp** = copy
* **-r** = recursive, copy the folder and all its contents
* **/home4/VBG_data/Richard/QC** = the folder to copy
* **.** = the destination to the copy it into (. = current folder)

If you list the contents of the new copied QC folder:

```
ls QC
```

You should see the following two subfolders: U2008751 and U2012100


Now let’s change directory (cd) into the first sample's folder:

```
cd ~/QC/U2008751
```

If you now list (ls) the contents of this directory you should see a pair of FASTQ reads:

```
ls
```

You should see that there are is a set of paired end reads. Files belonging to the same pair have exactly the same name except that one is labelled with \_R1\_ and one is labelled with \_R2\_ which signifies that these are the Read1 and Read2 files of the pair. NB: Sometimes instead of R1 and R2 they are simply labelled 1 and 2, either at the end of somewhere in the middle of the filename.
 
* U2008751-n5\_S4\_L001\_R1\_001.fastq.gz
* U2008751-n5\_S4\_L001\_R2\_001.fastq.gz

Currently, the files have been zipped (compressed) using the gzip program hence the .gz file extension on the filenames. So, we will first unzip them using the gunzip command:

``` 
gunzip *.gz
```

The * is a wildcard and tells the computer to **gunzip** all files that end in .gz (***.gz**)
 
If you list (ls) the directory contents now you should see the unzipped files:
 
* U2008751-n5\_S4\_L001\_R1\_001.fastq
* U2008751-n5\_S4\_L001\_R2\_001.fastq
 
One useful thing to check initially is that the R1 and R2 files for each pair contain the same number of reads. If they don’t then it suggests something is wrong, the two files might be out of sync or one file may be truncated. A simple way to accomplish this is to count the number of lines in the file – as in the FASTQ format each read is represented with 4 lines: 

**Command to count the number of lines in a file:**

```
wc -l *.fastq
```

Command breakdown: this command uses the wordcount (**wc**) program to count the number of lines (**-l**) in each file that ends with **.fastq** (the * is again used as a wildcard).
 
You should see something like this (3.075,444) lines which / 4 = 768,861 reads in each file:
 
* 3075444 U2008751-n5\_S4\_L001\_R1\_001.fastq
* 3075444 U2008751-n5\_S4\_L001\_R2\_001.fastq
 
A more detailed overview of the FASTQ reads can be viewed by using a program called prinseq which has a number of stats options which can give us a summary of the number of reads, their average lengths, and how many have an N base:
 
**Command to get basic reads stats from prinseq:**

```
prinseq-lite.pl -stats_info -stats_len -stats_ns -fastq U2008751-n5_S4_L001_R1_001.fastq.gz -fastq2 U2008751-n5_S4_L001_R2_001.fastq.gz
```

**NB:** The above is one command not two different ones!
 
Command breakdown: we run the prinseq program which is a perl script called **prinseq-lite.pl** and we tell it to output basic stats (**-stats\_info**), length stats (**-stats\_len**) and N base stats (**-stats\_ns**) on the FASTQ file (**-fastq**) and it’s pair (**-fastq2**)

```
stats_info       bases     208619557
stats_info       reads     768861
stats_info2      bases     209950349
stats_info2      reads     768861
stats_len        max       301
stats_len        mean      271.34
stats_len        median    300
stats_len        min       35
stats_len        mode      301
stats_len        modeval   377409
stats_len        range     267
stats_len        stddev    57.62
stats_len2       max       301
stats_len2       mean      273.07
stats_len2       median    301
stats_len2       min       35
stats_len2       mode      301
stats_len2       modeval   413728
stats_len2       range     267
stats_len2       stddev    57.57
stats_ns         maxn      77
stats_ns         maxp      100
stats_ns         seqswithn 341
stats_ns2        maxn      86
stats_ns2        maxp      100
stats_ns2        seqswithn 5114
stats_ns2        seqswithn 2899
```

The statistics are split into those for the first FASTQ file of the read pair (e.g. stats\_info, stats\_len, etc) and those for the second FASTQ file of the read pair (e.g. stats\_info2, stats\_len2, etc)

## 2: FASTQC

We are still working in this folder:

```
cd ~/QC/U2008751
````
 
We can visually inspect the quality of our paired end reads by using a tool called fastqc, enter this command to run fastqc on our paired end reads:

```
fastqc U2008751-n5_S4_L001_R1_001.fastq U2008751-n5_S4_L001_R2_001.fastq
```

**NB:** Remember we can use TAB completion to help enter the filenames!
 
If you list the contents if the directory now you should see two .html files outputted by fastqc which contain the fastqc report
 
* U2008751-n5\_S4\_L001\_R1\_001\_fastqc.html
* U2008751-n5\_S4\_L001\_R2\_001\_fastqc.html
 
To open these files you can use the MobaXterm file browser on the left hand side to navigate to the ~/QC/U2008751 folder and download the files locally onto your laptop, or enter:

```
firefox U2008751-n5_S4_L001_R1_001_fastqc.html
```

A help file explaining the different fastqc plots and analyses is here:

[https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/)

# 3: Trim Galore
 
Typically, the first thing you want to do with your FASTQ reads is some basic quality control. The primary goal of this is to remove low quality reads and low quality ends of reads from the data as low quality = high error rate.
 
1. Remove any illumina adapter sequences that are within the read sequences
2. Trim off poor quality sections from the 5’ ends of reads
3. Remove short reads
4. Remove any sequences with an N (as an N can signify a major quality issue with the read)
 
A common tool used for trimming reads is called Trim Galore. The command to run it on our first sample is:

```
trim_galore -q 20 --length 50 --max_n 0 --paired U2008751-n5_S4_L001_R1_001.fastq U2008751-n5_S4_L001_R2_001.fastq
```

**NB:** The above is one command not two different ones!
 
Command breakdown: we run the **trim\_galore** program using a quality score threshold (**-q**) of **20**, using a minimum length (**--length**) of **50**, we remove reads that have an N base count (**--max_n**) of greater than **0**, and we run it on the paired end reads (**--paired**) U2008751-n5\_S4\_L001\_R1\_001.fastq and U2008751-n5\_S4\_L001\_R2\_001.fastq
 
If you now list the contents of the directory, we should see the newly created FASTQ paired files containing our trimmed/filtered reads:
```
ls
```

You should see these trimmed FASTQ files:
 
* U2008751-n5\_S4\_L001\_R1\_001\_val\_1.fq    
* U2008751-n5\_S4\_L001\_R2\_001\_val\_2.fq

## 4: QC Notes
 
**What quality threshold should you use?** 

There is no simple answer to this question. It depends on many things including what is the objective (e.g. consensus vs quasispecies), how much data there is (e.g. viral load), and what sequencing technology was used. In general for Illumina data I use a quality threshold of **20** when doing consensus genome sequence generation, but increase this to quality **30** when doing quasispecies low frequency variant analyses. However, if you have a poor run and/or there are not many viral reads in the data, you could try lowering the quality threshold – BUT on the understanding that you are accepting poorer quality data which may influenze your results. It is the same with minimum read length, if you originally had 300 base reads but after trimming only 30 bases are left, although those remaining 30 bases may be above your quality threshold, 90% of the read was removed due to poor quality – do you trust the rest of it? These are not straightforward questions to answer.
 
**Additional filtering?** For quasispecies analyses, I typically add an additional prinseq filtering step using to remove reads that have an average quality that is less than the threshold (using the -min_qual_mean argument) as trim_galore does not do this (it just trims low quality sections from reads from the 3’ end)  
 
**Primers?** If you have used specific amplicon primers to amplify the viral genome before sequencing then you will most likely need to remove them from you reads (as the primer does not come from viral genome). There are a number of tools that can trim primer sequences from FASTQ reads such as trimmomatic [http://www.usadellab.org/cms/?page=trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic). Alternatively, tools such as iVar [https://andersen-lab.github.io/ivar/html/manualpage.html](https://andersen-lab.github.io/ivar/html/manualpage.html) can be used to do a post read alignment primer trimming.
