#转录组从头组装（trinity）  

## 上机操作  

**STEP 1: 组装**

>运行时间：~ 2 d

```
新建一个目录
$ mkdir 03.trinity
$ cd 03.trinity

```

创建任务脚本文件work.sh，包含以下内容：  

```
#!bin/bash
#$ -N wang_trinity
#$ -cwd

module add bioinfo
Trinity --no_bowtie --seqType fq --max_memory 20G \
	--SS_lib_type RF --CPU 8 \
	--normalize_reads \
	--left /bs1/data/NGS/data/fq/WLD-1.R1.fastq.gz,/bs1/data/NGS/data/fq/WLD-2.R1.fastq.gz,/bs1/data/NGS/data/fq/WLD-3.R1.fastq.gz,/bs1/data/NGS/data/fq/MUT-1.R1.fastq.gz,/bs1/data/NGS/data/fq/MUT-2.R1.fastq.gz,/bs1/data/NGS/data/fq/MUT-3.R1.fastq.gz\
	--right /bs1/data/NGS/data/fq/WLD-1.R2.fastq.gz,/bs1/data/NGS/data/fq/WLD-2.R2.fastq.gz,/bs1/data/NGS/data/fq/WLD-3.R2.fastq.gz,/bs1/data/NGS/data/fq/MUT-1.R2.fastq.gz,/bs1/data/NGS/data/fq/MUT-2.R2.fastq.gz,/bs1/data/NGS/data/fq/MUT-3.R2.fastq.gz\
```

用`qsub`提交任务，`qsub work.sh`  


##参考资料  

1. http://trinityrnaseq.github.io  
2. [De novo transcript sequence reconstruction from RNA-Seq: reference generation and analysis with Trinity](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3875132/)
