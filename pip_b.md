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
$ kallisto quant -i transdb -b 100 -o kallisto/wild_1 ../00.fq/WLD-1.R1.fastq.gz ../00.fq/WLD-1.R2.fastq.gz
$ kallisto quant -i transdb -b 100 -o kallisto/wild_2 ../00.fq/WLD-2.R1.fastq.gz ../00.fq/WLD-2.R2.fastq.gz
$ kallisto quant -i transdb -b 100 -o kallisto/wild_3 ../00.fq/WLD-3.R1.fastq.gz ../00.fq/WLD-3.R2.fastq.gz
$ kallisto quant -i transdb -b 100 -o kallisto/mutant_1 ../00.fq/MUT-1.R1.fastq.gz ../00.fq/MUT-1.R2.fastq.gz
$ kallisto quant -i transdb -b 100 -o kallisto/mutant_2 ../00.fq/MUT-2.R1.fastq.gz ../00.fq/MUT-2.R2.fastq.gz
$ kallisto quant -i transdb -b 100 -o kallisto/mutant_3 ../00.fq/MUT-3.R1.fastq.gz ../00.fq/MUT-3.R2.fastq.gz
结果文件保存在各子目录中，文件名为abundance.tsv
$ ll kallisto/*/abundance.tsv
-rw-rw-r--. 1 public public 872385 11月  9 09:26 kallisto/mutant_1/abundance.tsv
-rw-rw-r--. 1 public public 866615 11月  9 09:33 kallisto/mutant_2/abundance.tsv
-rw-rw-r--. 1 public public 868078 11月  9 09:43 kallisto/mutant_3/abundance.tsv
-rw-rw-r--. 1 public public 877250 11月  9 09:05 kallisto/wild_1/abundance.tsv
-rw-rw-r--. 1 public public 873866 11月  9 09:13 kallisto/wild_2/abundance.tsv
-rw-rw-r--. 1 public public 869286 11月  9 09:21 kallisto/wild_3/abundance.tsv
查看文件内容及格式
$ head kallisto/mutant_1/abundance.tsv
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
**STEP3: sleuth分析差异基因**  

sleuth是R语言包，需要R语言环境。  

```
$ R
> setwd("~/wang/ngs/02.kallisto")
> library("sleuth")

> ref_dir <- "/bs1/data/NGS/data/ref"
> base_dir <- getwd()
> sample_id <- dir(file.path(base_dir, "kallisto"))
> kal_dirs <- sapply(sample_id, function(id) file.path(base_dir,"kallisto", id))
> s2c <- data.frame(sample=c("mutant_1","mutant_2","mutant_3","wild_1","wild_2","wild_3"),  
                  condition=c("mutant","mutant","mutant","wild","wild","wild"))
> s2c <- dplyr::mutate(s2c, path = kal_dirs)
> print(s2c)

# Transcripts level

> t2g <- read.table(file.path(ref_dir, "tid2gid.txt"), header = T)
> so <- sleuth_prep(s2c, ~ condition, target_mapping = t2g)
> so <- sleuth_fit(so)
> so <- sleuth_fit(so, ~1, 'reduced')
> so <- sleuth_lrt(so, 'reduced', 'full')
#sleuth_live(so)
> results_table <- sleuth_results(so, 'reduced:full', test_type = 'lrt')
> write.csv(results_table, file = "results_table.csv")

# Gene level
> so.gene <- sleuth_prep(s2c, ~condition, target_mapping = t2g,
                  aggregation_column = 'gene_id')
> so.gene <- sleuth_fit(so.gene)
> so.gene <- sleuth_fit(so.gene, ~1, 'reduced')
> so.gene <- sleuth_lrt(so.gene, 'reduced', 'full')
#sleuth_live(so.gene)
> results_table.gene <- sleuth_results(so.gene, 'reduced:full', test_type = 'lrt')
> write.csv(results_table.gene, file = "results_table.gene.csv")

```

