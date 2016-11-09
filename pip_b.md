# 有参考基因组，不组装新的转录本（Kallisto -> sleuth）  

## 流程说明  

结果存放在`02.kallisto/`目录中。

```
$ mkdir 02.kallisto
$ cd 02.kallisto
```

1. Kallisto计算每个基因的reads number
2. sleuth差异基因分析

主要参考文献  

1. https://pachterlab.github.io/kallisto/manual
2. https://rawgit.com/pachterlab/sleuth/master/inst/doc/intro.html

## 上机操作  

**STEP1: 参考基因集建索引（index）**  

>运行时间：~ 2 min

```
$ kallisto index -i transdb /bs1/data/NGS/data/ref/transcripts.fa
```

**STEP2: 计算每个基因的reads number (count)**  

>运行时间：~ 1 h

```
$ kallisto quant -i transdb -o wild_1 ../00.fq/WLD-1.R1.fastq.gz ../00.fq/WLD-1.R2.fastq.gz
$ kallisto quant -i transdb -o wild_2 ../00.fq/WLD-2.R1.fastq.gz ../00.fq/WLD-2.R2.fastq.gz
$ kallisto quant -i transdb -o wild_3 ../00.fq/WLD-3.R1.fastq.gz ../00.fq/WLD-3.R2.fastq.gz
$ kallisto quant -i transdb -o mutant_1 ../00.fq/MUT-1.R1.fastq.gz ../00.fq/MUT-1.R2.fastq.gz
$ kallisto quant -i transdb -o mutant_2 ../00.fq/MUT-2.R1.fastq.gz ../00.fq/MUT-2.R2.fastq.gz
$ kallisto quant -i transdb -o mutant_3 ../00.fq/MUT-3.R1.fastq.gz ../00.fq/MUT-3.R2.fastq.gz
结果文件保存在各子目录中，文件名为abundance.tsv
$ ll */abundance.tsv
-rw-rw-r--. 1 public public 872385 11月  9 09:26 mutant_1/abundance.tsv
-rw-rw-r--. 1 public public 866615 11月  9 09:33 mutant_2/abundance.tsv
-rw-rw-r--. 1 public public 868078 11月  9 09:43 mutant_3/abundance.tsv
-rw-rw-r--. 1 public public 877250 11月  9 09:05 wild_1/abundance.tsv
-rw-rw-r--. 1 public public 873866 11月  9 09:13 wild_2/abundance.tsv
-rw-rw-r--. 1 public public 869286 11月  9 09:21 wild_3/abundance.tsv
查看文件内容及格式
$ head mutant_1/abundance.tsv
target_id	length	eff_length	est_counts	tpm
LongOrf.asmbl_22	1327	1134.14	177.148	5.20047
LongOrf.asmbl_32	413	220.234	8.32892	1.25915
Minc00001-1	603	410.234	0	0
Minc00002-1	138	49.9352	0	0
Minc00003-1	144	52.3017	0	0
Minc00004-1	132	48.2054	0	0
Minc00005-1	984	791.139	16	0.673349
Minc00006-1	342	149.295	1	0.223012
Minc00007-1	456	263.234	7.11	0.899293
```


