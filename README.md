# 高通量测序数据分析实践操作
RNA-Seq分析流程  

```
module add bioinfo
```

一、数据前处理  

1. 获取数据
```
试验数据服务器存放地址
/bs1/data/NGS/data/
测序原始数据
/bs1/data/NGS/data/fq/
参考基因组
/bs1/data/NGS/data/ref/

准备工作目录
mkdir work work/00.fq work/db
cd work/00.fq
ln -s /bs1/data/NGS/data/fq/*.gz ./
cd ../db
ln -s /bs1/data/NGS/data/ref/genome.fa ./
```
2. 质控（QC）
```
cd 00.fq/
fastqc -o qc/ *.fastq.gz 
# fastqc后不接参数，则是以交互式图形界面运行，需要支持WindowsX server的支持
```
fastqc使用请参考，[http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/)  
[质量好的报告](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/good_sequence_short_fastqc.html)  
[质量差的报告](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/bad_sequence_fastqc.html)  

二、基本分析  

RNA-Seq项目分析主要可分为3种应用情景  

1. 有参考基因组，需要预测新转录本（a）
2. 有参考基因组，不需要预测新转录本（b）
3. 无参考基因组（c）

![Image]
(https://static-content.springer.com/image/art%3A10.1186%2Fs13059-016-0881-8/MediaObjects/13059_2016_881_Fig2_HTML.gif)

[分析流程a：]()  
[分析流程b：]()  
[分析流程c：]()  

三、高级分析  



