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



## 第一步是QC

包括使用fasqc和multiqc两个软件查看测序质量，以及使用trim_galore软件进行过滤低质量reads和去除接头。


```
mkdir ~/project/boy
wkd=/home/jmzeng/project/boy
mkdir {raw,clean,qc,align,mutation} #一次创建多个文件夹，将不同步骤处理的数据分别放置。
cd qc 
find /public/project/clinical/beijing_boy  -name *gz |grep -v '\._'|xargs fastqc -t 10 -o ./     # -t threads

```

假设质量很差就过滤

```
### step3: filter the bad quality reads and remove adaptors. 
mkdir $wkd/clean 
cd $wkd/clean

find /public/project/clinical/beijing_boy  -name *gz |grep -v '\._'|grep 1.fastq.gz > 1
find /public/project/clinical/beijing_boy  -name *gz |grep -v '\._'|grep 2.fastq.gz > 2
paste 1 2  > config
### 打开文件 qc.sh ，并且写入内容如下： 
source activate wes

bin_trim_galore=trim_galore
dir=$wkd/clean
cat config | while read id
do
        arr=(${id})
        fq1=${arr[0]}
        fq2=${arr[1]} 
        echo  $dir  $fq1 $fq2 
nohup $bin_trim_galore -q 25 --phred33 --length 36 -e 0.1 --stringency 3 --paired -o $dir $fq1 $fq2 & 
done 

source deactivate
```

## 读质量较好的测序数据进行比对


BWA，即Burrows-Wheeler-Alignment Tool。BWA 是一种能够将差异度较小的序列比对到一个较大的参考基因组上的软件包。它由三个不同的算法：

- BWA-backtrack: 是用来比对 Illumina 的序列的，reads 长度最长能到 100bp。-
- BWA-SW: 用于比对 long-read ，支持的长度为 70bp-1Mbp；同时支持剪接性比对。
- BWA-MEM: 推荐使用的算法，支持较长的read长度，同时支持剪接性比对（split alignments)，但是BWA-MEM是更新的算法，也更快，更准确，且 BWA-MEM 对于 70bp-100bp 的 Illumina 数据来说，效果也更好些。
对于上述三种算法，首先需要使用索引命令构建参考基因组的索引，用于后面的比对。所以，使用BWA整个比对过程主要分为两步，第一步建索引，第二步使用BWA MEM进行比对。

bwa的使用需要两中输入文件：

- Reference genome data（fasta格式 .fa, .fasta, .fna）
- Short reads data (fastaq格式 .fastaq, .fq)


[bam文件flag的释义](https://www.jianshu.com/p/57680d72a452)
