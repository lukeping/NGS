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
	--right /bs1/data/NGS/data/fq/WLD-1.R2.fastq.gz,/bs1/data/NGS/data/fq/WLD-2.R2.fastq.gz,/bs1/data/NGS/data/fq/WLD-3.R2.fastq.gz,/bs1/data/NGS/data/fq/MUT-1.R2.fastq.gz,/bs1/data/NGS/data/fq/MUT-2.R2.fastq.gz,/bs1/data/NGS/data/fq/MUT-3.R2.fastq.gz
```

用`qsub`提交任务，`qsub work.sh`  

任务完成后，组装结果存放在目录trinity_out_dir/下，

**STEP 2: 统计组装结果信息**  

```
$ /home/biosoft/trinityrnaseq-2.2.0/util/TrinityStats.pl trinity_out_dir/Trinity.fasta
################################
## Counts of transcripts, etc.
################################
Total trinity 'genes':	65
Total trinity transcripts:	103
Percent GC: 48.55

########################################
Stats based on ALL transcript contigs:
########################################

	Contig N10: 7194
	Contig N20: 5851
	Contig N30: 5519
	Contig N40: 3918
	Contig N50: 3647

	Median contig length: 422
	Average contig: 1548.14
	Total assembled bases: 159458


#####################################################
## Stats based on ONLY LONGEST ISOFORM per 'GENE':
#####################################################

	Contig N10: 8756
	Contig N20: 7194
	Contig N30: 5854
	Contig N40: 5519
	Contig N50: 3745

	Median contig length: 316
	Average contig: 1040.43
	Total assembled bases: 67628


```

**STEP 3: 计算每个基因（转录本）的reads数（use RSEM to estimate read abundance）** 

创建任务脚本文件rsem.sh，包含以下内容：  

```
#!bin/bash
#$ -N wang_RSEM
#$ -cwd

module add bioinfo
/home/biosoft/trinityrnaseq-2.2.0/util/align_and_estimate_abundance.pl \
	--transcripts trinity_out_dir/Trinity.fasta \
	--seqType fq \
	--left /bs1/data/NGS/data/fq/WLD-1.R1.fastq.gz \
	--right /bs1/data/NGS/data/fq/WLD-1.R2.fastq.gz \
	--SS_lib_type RF \
	--est_method RSEM \
	--aln_method bowtie \
	--trinity_mode \
	--prep_reference \
	--output_dir RSEM_wild_1
/home/biosoft/trinityrnaseq-2.2.0/util/align_and_estimate_abundance.pl \
        --transcripts trinity_out_dir/Trinity.fasta \
        --seqType fq \
        --left /bs1/data/NGS/data/fq/WLD-2.R1.fastq.gz \
        --right /bs1/data/NGS/data/fq/WLD-2.R2.fastq.gz \
        --SS_lib_type RF \
        --est_method RSEM \
        --aln_method bowtie \
        --trinity_mode \
        --output_dir RSEM_wild_2
/home/biosoft/trinityrnaseq-2.2.0/util/align_and_estimate_abundance.pl \
        --transcripts trinity_out_dir/Trinity.fasta \
        --seqType fq \
        --left /bs1/data/NGS/data/fq/WLD-3.R1.fastq.gz \
        --right /bs1/data/NGS/data/fq/WLD-3.R2.fastq.gz \
        --SS_lib_type RF \
        --est_method RSEM \
        --aln_method bowtie \
        --trinity_mode \
        --output_dir RSEM_wild_3
/home/biosoft/trinityrnaseq-2.2.0/util/align_and_estimate_abundance.pl \
        --transcripts trinity_out_dir/Trinity.fasta \
        --seqType fq \
        --left /bs1/data/NGS/data/fq/MUT-1.R1.fastq.gz \
        --right /bs1/data/NGS/data/fq/MUT-1.R2.fastq.gz \
        --SS_lib_type RF \
        --est_method RSEM \
        --aln_method bowtie \
        --trinity_mode \
        --output_dir RSEM_mutant_1
/home/biosoft/trinityrnaseq-2.2.0/util/align_and_estimate_abundance.pl \
        --transcripts trinity_out_dir/Trinity.fasta \
        --seqType fq \
        --left /bs1/data/NGS/data/fq/MUT-2.R1.fastq.gz \
        --right /bs1/data/NGS/data/fq/MUT-2.R2.fastq.gz \
        --SS_lib_type RF \
        --est_method RSEM \
        --aln_method bowtie \
        --trinity_mode \
        --output_dir RSEM_mutant_2
/home/biosoft/trinityrnaseq-2.2.0/util/align_and_estimate_abundance.pl \
        --transcripts trinity_out_dir/Trinity.fasta \
        --seqType fq \
        --left /bs1/data/NGS/data/fq/MUT-3.R1.fastq.gz \
        --right /bs1/data/NGS/data/fq/MUT-3.R2.fastq.gz \
        --SS_lib_type RF \
        --est_method RSEM \
        --aln_method bowtie \
        --trinity_mode \
        --output_dir RSEM_mutant_3
```

用`qsub`提交任务，`qsub rsem.sh`。  
任务完成后检查结果文件。  

##参考资料  

1. http://trinityrnaseq.github.io  
2. [De novo transcript sequence reconstruction from RNA-Seq: reference generation and analysis with Trinity](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3875132/)
