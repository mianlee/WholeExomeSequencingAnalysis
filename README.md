# 全外显子测序分析

参考生信技能树，全外显子测序数据分析视频来做练习

2020/10/23, 工作生活趋于稳定，借全外显子测序分析的学习，开始记录每天的生物信息学自我学习的学习过程。

目标是成为基因组学，转录组学，蛋白质组学，代谢组学，微生物组学，实验+生物信息学方向的大牛！


## WES 的应用方向

1. 人类基因组中低频变异分析

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


```
# 查看文件夹和文件大小
du -h 

# 一次建立多个文件夹

mkdir {apple, pear, peach}

# find

find /folder/ -name *.gz

# xargs, xargs 结合 find 使用

find /folder/ -name *gz | xargs ls -la

```
