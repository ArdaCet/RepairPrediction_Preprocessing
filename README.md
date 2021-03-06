# Welcome to RepairPrediction_Preprocessing Repository
Key words: research, project, repository, NGS, data analysis, XR-seq, damage-seq, ChIP-seq, DNase-seq, histone markers

Hello all,

This repository includes pipelines, python codes, and the NGS data used in the research project. It is the exact copy of [another repository](https://github.com/CompGenomeLab/globalNERepair) which was creted and edited by me.

You can find the pipelines and Python codes used in the research at this repository.

# Project Aim
Revealing one of the causes of genome-wide nucleotide excision repair heterogeneity mainly using histone markers.

# globalNERepair
Instead of globalRepair Repository, I am using globalNERepair repository!

###  This repository includes globalRepair project's scripts and pipelines.
For pipelines, please visit [NGS preprocessesing Pipelines](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines) folder.

For Scripts, please visit [Python scripts](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/Python_Scripts) folder.

##  Setup
Custom setup assumes that you have the necessary programs installed and they are exectuble in your $PATH.

The list of the necessary programs are:

* Bowtie2
* cutadapt
* sra toolkit
* samtools
* bedtools
* ucsc tools
* NGStoolkit

In order to download and run the NGStoolkit, the direction are taken by the author of this toolkit Ogun Adebali, PhD.
>To place the source code of this repository in your path, please follow these commands:

```
cd ~
git clone git@bitbucket.org:adebali/NGStoolkit.git
cd NGStoolkit
bash setup.sh
```
To reach the original page for NGStoolkit, you can visit [Ogun Adebali Github page](https://github.com/adebali/NGStoolkit).

##  Work with your own data
Below, instructions are exemplified based on "XR-seq pipeline editing" but they are also valid for other three NGS methods as well.

Irrespective of the any NGS methods ([XR-seq](https://github.com/CompGenomeLab/globalNERepair/tree/master/pipelines/My_XR_seq.sh), [damage-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_Damage_seq.sh), [ChIP-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_Chip_seq.sh) or [DNase-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_DNAse_seq.sh) ) for preprocessing you should move your .fastq file into the data directory.

Edit the My_XR-seq.sh and replace the SAMPLE variable with the base sample name in your file. For example if you file is named as myFile.fastq the base name will be myFile.

If you want to retrieve the existing data set from SRA please see the fastq-dump command and replace the SRA acccession number with the one of interest. If you use your own file please comment out that two lines in My_XR-seq.sh.

In the project, un-subsampled and subsampled version of the data were used. The subsampling threshold was determined based on the total number of reads coming from the raw sequencing data and subsampling performed completely randomly. The build-in python package _sample_ was used. Since each line represents a genomic coordinate and corresponding genetic information inside the .BED files, a [python script](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/Python_Scripts) selecting random rows was run and new subsampled ".BED" files was then used for for the further experiements.

>_"sample"_ is a build-in python package which accepts dataframe.

```
seed_num = 1
sampling = 2458200

ran_sampled = data.sample(n=sampling, random_state=seed_num)

```
To reach the original page for  random package, you can visit [Python Documantation page](https://docs.python.org/3/library/random.html).

# Research Protocol

You should know what sam, bam, and bed files are. [Too learn click](http://software.broadinstitute.org/software/igv/?q=book/export/html/16)

To retrieve all the NGS data utilzied in the research please see the [Data](#Data) section below.

To go to the python codes for RPKM calculation, subsampling, gene annotation, and normalization please [click](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/Python_Scripts/RPKM.py).

To go to the preprocessing pipelines for the [XR-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_XR_seq.sh), [damage-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_Damage_seq.sh), [ChIP-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_Chip_seq.sh), and [DNase-seq](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/pipelines/My_DNAse_seq.sh) 

1. Run XR-seq, ChIP-seq, DNase-seq, and damage-seq pipelines with appropriate PATH and file names.

2. Pipelines consist of aligning reads to the human reference genome (hg19), data formatting, mapping genomic positions on the 5kb divided reference genome for acquiring read count numbers, [RPKM calculation](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/Python_Scripts/RPKM.py), and data laballing.

  * In each pipeline, should be run from top to bottom for once to create the outputs. Then, the second run should performed but this time just before getting read count numbers (before the _bedtools intersect_) [subsample](#Work-with-your-own-data) your corresponding file according to the minimum read number which should be determined by looking through all the sequencing files' read numbers. Here, you need to check all XR-seq, ChIP-seq, DNase-seq, and damage-seq raw reads to see which file has the smallest read coverage in other words number of reads.

3. Downsampling should be applied on all the files except the one having the lowest read count (see Python_scripts directory and [Work with your own data](#Work-with-your-own-data) headings at the [README](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/README.md) page). The number of read sampling value should equal to the file including the smallest number of reads.
 
  * After downsampling the corresponding [.BED](http://software.broadinstitute.org/software/igv/?q=book/export/html/16) file, find read overlaps using the subsampled data and continue to apply exact same steps as your first run.
 
4. Convert read count values to [RPKM values](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/Python_Scripts/RPKM.py).

5. Collect the outputs of the each sequencing pipeline for filtering and [normalizing](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/Python_Scripts/DMG_Replicate-A_filtering.py).

  * Remove rows which are having zero RPKM value from the  damage-seq files (filtering step), then normalize repair by damage.
  
  * Each row of XR-seq RPKM value should be divided by its corresponding RPKM row of the related damage-seq file.
  
     * Each repair and its relavent damage type (UV damage types: Cisplatin, (6-4)PP) must be filtered and normalized during the filtering step.
     
     * e.g    XR-seq-(6-4)PP-repA / damage-seq-(6-4)PP-repA    XR-seq-(6-4)PP-repB / damage-seq-(6-4)PP-repB    XR-seq-Cisplatin-repA / damage-seq-Cisplatin-repA and etc.

6. Creating a dataframe in which each column will be chromosome_name, position_start, position_end, XR-seq, damage-seq, ChIP-seq, and DNase-seq RPKM values, sequentially.

  * In most cases, there will be technical and/or biological repliciates of each sequencing in this case they should be sequentially followed. 
  
     * e.g    (6-4)PP_Repair_damage_normalized_repA   (6-4)PP_Repair_damage_normalized_RepB   (6-4)PP_Repair_damage_normalized_RepC   and so on.
     
7. Apply same subsampling steps again (1-6) for all NGS data but this time after subsampling the ".BED" file, for intersencting the downsampled file use gene annotation (Gene and Intergene region files) file which can be created by the Python script [_Genes_Intergenes_](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/Python_Scripts/Genes_Intergenes.ipynb). For any information regarding the script and its format see section [Gene Annotation](#Gene-Annotation).
  * **Attention!** The bedtools intersect step for gene annotation needs 4 files, which can be created using the _Genes_Intergenes.ipynb_, not the 5kb divided human reference genome. Therefore, user should repeat the same step for four times for each sequencing type and if there are replicates then four times for them too.
    ** For XR-seq-(6-4)PP-repA, map the reads on three gene and one intergenes file. So, use that four files as a a reference genome file.

# Gene Annotation
For retrieveing the hg19 gtf file manually by downloading it from its original source please go to the link below (or see [section Data](#Data)):

ftp://ftp.ensembl.org/pub/grch37/release-98/gtf/homo_sapiens/

and download the file with a name:

File name is _Homo_sapiens.GRCh37.87.gtf.gz_

There are various ways to retrieve the data but personally best bets in terms of easiness and conciseness would be:

>Using wget:
```
import wget

gtf_link = 'ftp://ftp.ensembl.org/pub/grch37/release-98/gtf/homo_sapiens/Homo_sapiens.GRCh37.87.gtf.gz'
wget.download(gtf_link)

```

>Using urllib2:
```
import urllib2

gtf_req = urllib2.Request('ftp://ftp.ensembl.org/pub/grch37/release-98/gtf/homo_sapiens/Homo_sapiens.GRCh37.87.gtf.gz')
web_response = urllib2.urlopen(gtf_req)
the_page = response.read()

```

>If necessary extract the gtf file using:

```
from sh import gunzip

gunzip('/content/Homo_sapiens.GRCh37.87.gtf.gz')
```

Then, you can apply the [_Genes_Intergenes_](https://github.com/ArdaCet/RepairPrediction_Preprocessing/tree/main/Python_Scripts/Genes_Intergenes.ipynb) python script in order to get the exact data we have obtained and used for data analysis in the research or you can use this script and chage it depending on your research purpose. 

The script has an extension _".ipynb"_ meaning that it was written in the Jupyter Notebooks using google coolab. The purpose of usng the google coolab was its convenience in storing data at Google Drive and avaialability of writting and running codes seperately and seeing outputs nicely and continously.

If you want to work on the "_Genes_Intergenes.ipynb_" as a python file, you can convert it using:

>First install required packages and follow the guide:
```
pip install ipynb-py-convert
```

>Then run the below line of code:
```
ipynb-py-convert examples/plot.ipynb examples/plot.py
```

>Or you may want to convert python script into a Jupyter Notebook file use:
```
ipynb-py-convert examples/plot.py examples/plot.ipynb
```


This will turn markdown text into multiline strings ' _'''_ ' format. Moreover, after the conversion each Jupyter Notebooks cell as a "normal" python code line will have a marker "_# %%_". This indicates that until reaching another marker, all the codes within two markers previously belong in a single Jupyter Notebook cell.

# Data
The Hg19 genome which is divided into 5000 bases can be acces through [here](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/Data/5kb_divided_hg19.bed).

The gene annotation GTF file includes transcripts and gene information. It was used to map repair, damage, histone markers (ChIP-seq), and DNase-seq data on the genes and intergens for better interpretation of biological knowledge from the analysis. To download the GTF file:

>Go to its original ftp source: ftp://ftp.ensembl.org/pub/grch37/release-98/gtf/homo_sapiens/

The ChIP-seq data used in the project can be retrieved using the given ENCODE IDs.

![image](https://user-images.githubusercontent.com/57831340/105982928-0421de00-60a9-11eb-9745-4248b874e56e.png)

In GM12878, damage-seq and XR-seq 1.5hr data can be retrieved from GSE98025 and GSE82213, respectively. NHF1 cell line’s XR-seq data generated after one-hour UV exposure are from GSE67941 and DNA repair after four hours XR-seq data are from GSE76391. The both 1- and 4-hours UV treatment data on NHF1 cells line could be accessed at GSE98025.

The DNaseseq data for the HeLa and GM12878 cell lines can be accessed from GSE32970 and GSE32970, sequentially. The DNase-seq datum for the normal skin fibroblast cell
line (NHDF) can be retrieved from GEO ID GSE2969.

# Caution
The pipelines and scripts were not for general usage purposes, they have been developed for achieving specific purpose hence, the line of codes can vary depending on the purpose.

Even for reproducing the same experiements, you should be carefull to apply same pipelines or scripts for different sort of cell lines types. Although, four pipelines were used in the preprocessing of four NGS data (Xr-seq, damage-seq, DNase-seq, and ChIP-seq), within each pipeline the parameters for the tool commands varied. For example, you should aware whether the NGS datum layout is paired-ended or single-ended. Besides, you need to know wheter you should trimm or remove the adaptors completely from the data. 

Also, depending on the sequencing data quality, deduplicates might need to be removed and for those who would like to test the reproducibility of our work, please try to apply the same steps as much as possible. The most important difference could emerge at performing the subsampling step. Please read [Work with your own data](#Work-with-your-own-data) section above to get the idea.

#  Author
Arda Cetin

To check out my other [repository](https://github.com/CompGenomeLab/globalNERepair)

To reach me about any question please send me an [email](mailto:ardacetin_work@hotmail.com).

# Licence
This project is licensed under the MIT License [see](https://github.com/ArdaCet/RepairPrediction_Preprocessing/blob/main/LICENSE).
