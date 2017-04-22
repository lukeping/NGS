# 基因组组装及注释（PacBio序列）  

## 实践内容  
1. 基于第三代测序数据（PacBio）基因组组装  
2. 基因组注释  

## 数据  
```
# 原始测序存放在：
/bs1/data/NGS/pacbio

[wangys@c1 pacbio]$ ll /bs1/data/NGS/pacbio
total 15045772
-rw-r--r--. 1 wangys wangys 5640540317 Apr 22 12:27 m170309_165401_42221_c101149812550000001823255607191754_s1_p0.1.bax.h5
-rw-r--r--. 1 wangys wangys  445711165 Apr 22 12:27 m170309_165401_42221_c101149812550000001823255607191754_s1_p0.1.subreads.fastq
-rw-r--r--. 1 wangys wangys 4212270498 Apr 22 12:29 m170309_165401_42221_c101149812550000001823255607191754_s1_p0.2.bax.h5
-rw-r--r--. 1 wangys wangys  508932544 Apr 22 12:29 m170309_165401_42221_c101149812550000001823255607191754_s1_p0.2.subreads.fastq
-rw-r--r--. 1 wangys wangys 4157080944 Apr 22 12:30 m170309_165401_42221_c101149812550000001823255607191754_s1_p0.3.bax.h5
-rw-r--r--. 1 wangys wangys  427212843 Apr 22 12:30 m170309_165401_42221_c101149812550000001823255607191754_s1_p0.3.subreads.fastq
```

说明： 含有两种格式的数据，一类是fastq序列，一类是h5数据，组装我们用的是fastq数据，最后用quiver打磨(polish)基因组时要用到h5数据。

## 上机操作  

### 1. 准备工作目录和数据  

```
mkdir work #我以work目录为例，大家自己建一个自己的工作目录，注意，后面的目录名也要作相应改变
cd work
mkdir data assem annotation
ln -s /bs1/data/NGS/pacbio/* data/
cd assem
# 新建一个工作脚本文件work.sh，包含下列内容：
#!/bin/bash
#$ -S /bin/bash
#$ -N canu_wang
#$ -cwd
#$ -j y
module add bioinfo
canu -p test -d output genomeSize=6.5m \
	gridEngineThreadsOption="-pe smp THREADS" \
	gridEngineMemoryOption="-l mem_free=MEMORY" \
	-pacbio-raw ../data/*.fastq

# 提交任务
qsub work.sh

```
