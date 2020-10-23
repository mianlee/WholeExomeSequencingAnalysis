# 全外显子测序分析

参考生信技能树，全外显子测序数据分析视频来做练习

2020/10/23, 工作生活趋于稳定，借全外显子测序分析的学习，开始记录每天的生物信息学自我学习的学习过程。

目标是成为基因组学，转录组学，蛋白质组学，代谢组学，微生物组学，实验+生物信息学方向的大牛！


## WES 的应用方向

1.人类基因组中低频变异分析

- 尤其是那些芯片平台中不涵盖的稀有变异

2.孟德尔单基因遗传病致病基因分析

3.癌症等复杂疾病相关基因变异分析

## 首先安装软件

```
conda install sra-tools
conda install samtools
conda install -y bcftools vcftools  snpeff
conda install -y multiqc qualimap trim_galore

 
# 建立gatk文件夹，然后从官网上找到下载链接地址用wget下载.

mkdir -p Software/gatk4 && cd Sofeware/gatk4
wget  https://github.com/broadinstitute/gatk/releases/download/4.1.9.0/gatk-4.1.9.0.zip
unzip gatk-4.0.6.0.zip

#add gatk to you PATH

export PATH="/path/to/gatk-package/:$PATH" 

# where /path/to/gatk-package/ is the path to the location of the gatk executable

```

**sra-tools** : The SRA Toolkit and SDK from NCBI is a collection of tools and libraries for using data in the INSDC Sequence Read Archives, SRA (Sequence Read Archive).


## 熟悉参考基因组及必备数据库

```
/public/biosoft/GATK/resources/bundle/hg38/bwa_index/
|-- [ 20K]  gatk_hg38.amb
|-- [445K]  gatk_hg38.ann
|-- [3.0G]  gatk_hg38.bwt
|-- [767M]  gatk_hg38.pac
|-- [1.5G]  gatk_hg38.sa
|-- [6.2K]  hg38.bwa_index.log 
`-- [ 566]  run.sh

/public/biosoft/GATK/resources/bundle/hg38/
|-- [1.8G]  1000G_phase1.snps.high_confidence.hg38.vcf.gz
|-- [2.0M]  1000G_phase1.snps.high_confidence.hg38.vcf.gz.tbi
|-- [3.2G]  dbsnp_146.hg38.vcf.gz
|-- [3.0M]  dbsnp_146.hg38.vcf.gz.tbi
|-- [ 59M]  hapmap_3.3.hg38.vcf.gz
|-- [1.5M]  hapmap_3.3.hg38.vcf.gz.tbi
|-- [568K]  Homo_sapiens_assembly38.dict
|-- [3.0G]  Homo_sapiens_assembly38.fasta
|-- [157K]  Homo_sapiens_assembly38.fasta.fai
|-- [ 20M]  Mills_and_1000G_gold_standard.indels.hg38.vcf.gz
|-- [1.4M]  Mills_and_1000G_gold_standard.indels.hg38.vcf.gz.tbi
```

## Directly download the prepared Human reference files (hg19 or other reference version GRch38) from GATK Resource Bundle

I found out that the GATK resource bundle is a collection of standard files for working with human resequencing data with the GATK. They provide several versions of the bundle corresponding to the various reference builds. So you don't have to build it by your self......

The ```bundle/``` directory contains five subdirectories, one for each build of the human genome that we have resources for: b36, b37, hg18, hg19 and hg38 (aka GRCh38). Here you can go to the hg19 folder and download related files, in our case, ```ucsc.hg19.fasta.gz```, ```ucsc.hg19.fasta.fai.gz``` and ```ucsc.hg19.dict.gz```.


**ftp tool**


Using lftp tool to visit and download reference data from ftp. If you don't have lftp command installed on your server or local computer, you need to intall it first. After installation, enter the following command in your terminal:

```
lftp ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/

# There is no password, just hit the enter key
# gsapubftp-anonymous is the user name, @ftp.broadinstitute.org/bundle/ is the address of the GATK server
If you connected to the server, you will see something like below. You can use cd and ls command to navigate those files and folders.
```


You can use ```get``` command to download the file you need. Then you will get e.g. ```ucsc.hg19.fasta.gz```, ```ucsc.hg19.fasta.fai.gz``` and ```ucsc.hg19.dict.gz```.

```
get ucsc.hg19.fasta.gz
get ucsc.hg19.fasta.fai.gz
get ucsc.hg19.dict.gz
```

If you want to download all files under a certain folder, you can use mirror command.

```
mirror hg19
```

**Reference**

[GATK Resource Bundle](https://gatk.broadinstitute.org/hc/en-us/articles/360035890811-Resource-bundle).

[GATK数据下载](https://blog.csdn.net/xxxie_/article/details/100111991).

[Differences between b37 and hg19](https://github.com/bahlolab/bioinfotools/blob/master/GATK/resource_bundle.md).





