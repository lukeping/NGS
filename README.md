# 高通量测序数据分析实践操作  

## 一、操作说明    


**说明：`$`后为命令，其他的为输出或说明。**  

```
ssh登录服务器172.28.137.55，用户名：ngs

加载环境变量
$ module add bioinfo

对于计算量较大的任务，请不要在登录节点上直接运行，用qsub提交任务
```
### Linux基本操作  

**掌握以下常用的Linux命令：**

  |  |  |  |  |   |   
--- | --- | --- | --- | --- | ---  
ls | cd | mv | rm | pwd | top
ps | grep | kill | file | tar | cat
less | more | chmod | chown | vim (vi) | time
cp | scp | ln | mkdir | wget | git
find | export | source | mount | head | tail

**使用集群**  
尽量不要在登录节点（管理节点）上运行大型程序，ssh到其他计算节点上之后再运行其他程序。登录到172.28.137.55管理节点后，`ssh 计算节点名`，计算节点名可以是c2, c3, c4, c5, c6 (目前有问题), c8  
推荐使用SGE作业管理系统提交任务，先将任务脚本存放在一个文件中，然后用`qsub`提交任务，下面是一个简单的任务`work.sh`, 任务的脚本文件work.sh中包含以下内容：  

```
#!/bin/bash
#$ -S /bin/bash
#$ -N JobName
#$ -cwd
sleep 60
```

用`qsub`提交任务，`qstat`查看任务运行情况  

```
$ qsub work.sh
$ qstat
job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID 
-----------------------------------------------------------------------------------------------------------------
     44 0.55500 trinity    ngs          r     11/17/2016 21:58:30 all.q@c8                           1        
     45 0.55500 JobName    ngs          r     11/18/2016 15:00:00 all.q@c2                           1        

```
## 二、试验设计 与 试验目的  
比较野生型与突变体之间的基因表达差异，一组为野生型，一组为突变体，分别设了3次重复。  

序列文件 | 类型 | 样本编号
------- | ----- | ------
WLD-1.R1.fastq.gz, WLD-1.R2.fastq.gz | 野生型 | Wild-1
WLD-2.R1.fastq.gz, WLD-2.R2.fastq.gz | 野生型 | Wild-2
WLD-3.R1.fastq.gz, WLD-3.R2.fastq.gz | 野生型 | Wild-3
MUT-1.R1.fastq.gz, MUT-1.R2.fastq.gz | 突变体 | Mutant-1
MUT-2.R1.fastq.gz, MUT-2.R2.fastq.gz | 突变体 | Mutant-2
MUT-3.R1.fastq.gz, MUT-3.R2.fastq.gz | 突变体 | Mutant-3

我们分别用以下三种策略对试验数据进行分析：  

1. 有参考基因组，需要组装新的转录本
2. 有参考基因组，不需要组装新的转录本
3. 无参考基因组

## 三、上机操作  

### 1、数据前处理  

**获取数据**
```
试验数据服务器存放地址
/bs1/data/NGS/data/
测序原始数据
/bs1/data/NGS/data/fq/
参考基因组
/bs1/data/NGS/data/ref/

准备工作目录
$ mkdir work work/00.fq work/db
$ cd work/00.fq
$ ln -s /bs1/data/NGS/data/fq/*.gz ./
$ ls -l
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-1.R1.fastq.gz -> /bs1/data/NGS/data/fq/MUT-1.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-1.R2.fastq.gz -> /bs1/data/NGS/data/fq/MUT-1.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-2.R1.fastq.gz -> /bs1/data/NGS/data/fq/MUT-2.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-2.R2.fastq.gz -> /bs1/data/NGS/data/fq/MUT-2.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-3.R1.fastq.gz -> /bs1/data/NGS/data/fq/MUT-3.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-3.R2.fastq.gz -> /bs1/data/NGS/data/fq/MUT-3.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-1.R1.fastq.gz -> /bs1/data/NGS/data/fq/WLD-1.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-1.R2.fastq.gz -> /bs1/data/NGS/data/fq/WLD-1.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-2.R1.fastq.gz -> /bs1/data/NGS/data/fq/WLD-2.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-2.R2.fastq.gz -> /bs1/data/NGS/data/fq/WLD-2.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-3.R1.fastq.gz -> /bs1/data/NGS/data/fq/WLD-3.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-3.R2.fastq.gz -> /bs1/data/NGS/data/fq/WLD-3.R2.fastq.gz

```

**质控（QC）**

fastqc使用请参考，[http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/)  
[测序质量好的报告样本](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/good_sequence_short_fastqc.html)  
[测序质量差的报告样本](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/bad_sequence_fastqc.html)  

```
$ cd 00.fq/
$ mkdir qc
```

新建一个脚本文件，work.sh，包含以下内容：  

```
#!/bin/bash
#$ -S /bin/bash
#$ -cwd
module add bioinfo
fastqc -o qc/ *.fastq.gz
```

然后用`qsub work.sh`提交任务。用`qstat`查看job运行状态。  

```
$ qstat
job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID 
-----------------------------------------------------------------------------------------------------------------
     41 0.55500 work.sh    ngs          r     11/17/2016 20:41:45 all.q@c3                           1        
任务结束后，结果文件存在qc文件夹下
$ ll qc/
总用量 8752
-rw-rw-r--. 1 public public 334917 11月  8 09:05 MUT-1.R1_fastqc.html
-rw-rw-r--. 1 public public 381568 11月  8 09:05 MUT-1.R1_fastqc.zip
-rw-rw-r--. 1 public public 347082 11月  8 09:08 MUT-1.R2_fastqc.html
-rw-rw-r--. 1 public public 401704 11月  8 09:08 MUT-1.R2_fastqc.zip
-rw-rw-r--. 1 public public 313485 11月  8 09:11 MUT-2.R1_fastqc.html
-rw-rw-r--. 1 public public 393983 11月  8 09:11 MUT-2.R1_fastqc.zip
-rw-rw-r--. 1 public public 323387 11月  8 09:15 MUT-2.R2_fastqc.html
-rw-rw-r--. 1 public public 408992 11月  8 09:15 MUT-2.R2_fastqc.zip
-rw-rw-r--. 1 public public 335965 11月  8 09:19 MUT-3.R1_fastqc.html
-rw-rw-r--. 1 public public 430604 11月  8 09:19 MUT-3.R1_fastqc.zip
-rw-rw-r--. 1 public public 342367 11月  8 09:23 MUT-3.R2_fastqc.html
-rw-rw-r--. 1 public public 439796 11月  8 09:23 MUT-3.R2_fastqc.zip
-rw-rw-r--. 1 public public 335152 11月  8 09:26 WLD-1.R1_fastqc.html
-rw-rw-r--. 1 public public 381084 11月  8 09:26 WLD-1.R1_fastqc.zip
-rw-rw-r--. 1 public public 338596 11月  8 09:29 WLD-1.R2_fastqc.html
-rw-rw-r--. 1 public public 383675 11月  8 09:29 WLD-1.R2_fastqc.zip
-rw-rw-r--. 1 public public 314118 11月  8 09:33 WLD-2.R1_fastqc.html
-rw-rw-r--. 1 public public 398020 11月  8 09:33 WLD-2.R1_fastqc.zip
-rw-rw-r--. 1 public public 312072 11月  8 09:37 WLD-2.R2_fastqc.html
-rw-rw-r--. 1 public public 395426 11月  8 09:37 WLD-2.R2_fastqc.zip
-rw-rw-r--. 1 public public 308044 11月  8 09:41 WLD-3.R1_fastqc.html
-rw-rw-r--. 1 public public 385617 11月  8 09:41 WLD-3.R1_fastqc.zip
-rw-rw-r--. 1 public public 311030 11月  8 09:45 WLD-3.R2_fastqc.html
-rw-rw-r--. 1 public public 393542 11月  8 09:45 WLD-3.R2_fastqc.zip
```
结果文件存放在`qc/`子目录中。  
>fastqc后如不接任何参数，则是以交互式图形界面运行，需要WindowsX server的支持  

如果发现测序质量比较差，需要作进一步过滤处理的，可以用`trimmomatic`进行处理。  
[trimmomatic参考使用方法](http://www.usadellab.org/cms/?page=trimmomatic)  

### 2. 基本分析  

RNA-Seq项目分析主要可分为3种应用情景  

[1) 有参考基因组，需要预测新转录本（a）Hisat2, StringTie, ](https://github.com/lukeping/NGS/blob/master/pip_a.md)  
[2) 有参考基因组，不需要预测新转录本（b）]()  
[3) 无参考基因组（c）]()  

![Image]
(https://static-content.springer.com/image/art%3A10.1186%2Fs13059-016-0881-8/MediaObjects/13059_2016_881_Fig2_HTML.gif)

### 3. 特色分析  

1) 可变剪切分析
2) 融合基因分析
3) GO富集分析
4) Pathway富集分析

## 报告内容要求  
1. 测序质量报告  
2. Mapping结果统计  
3. 比较不同方法的差异


