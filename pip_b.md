# 有参考基因组，不组装新的转录本（Kallisto -> sleuth）  

## 流程说明  

1. Kallisto计算每个基因的reads number
2. sleuth差异基因分析

结果存放在`02.kallisto/`目录中。

```
$ mkdir 02.kallisto
$ cd 02.kallisto
```

## 上机操作  

**1. 参考基因集建索引（index）**  

>运行时间：~2 min

```
$ kallisto index -i transdb /bs1/data/NGS/data/ref/transcripts.fa
```

**2. 计算每个基因的reads number (count)**  

>运行时间：~

```
$ kallisto quant -i transdb -o wild_1 ../00.fq/WLD-1.R1.fastq.gz ../00.fq/WLD-1.R2.fastq.gz
$ kallisto quant -i transdb -o wild_2 ../00.fq/WLD-2.R1.fastq.gz ../00.fq/WLD-2.R2.fastq.gz
$ kallisto quant -i transdb -o wild_3 ../00.fq/WLD-3.R1.fastq.gz ../00.fq/WLD-3.R2.fastq.gz
$ kallisto quant -i transdb -o mutant_1 ../00.fq/MUT-1.R1.fastq.gz ../00.fq/MUT-1.R2.fastq.gz
$ kallisto quant -i transdb -o mutant_2 ../00.fq/MUT-2.R1.fastq.gz ../00.fq/MUT-2.R2.fastq.gz
$ kallisto quant -i transdb -o mutant_3 ../00.fq/MUT-3.R1.fastq.gz ../00.fq/MUT-3.R2.fastq.gz
结果文件保存在各子目录中，abundance.tsv
$ ls */abundance.tsv

```


