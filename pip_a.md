# 有参考基因组，但需要组装新的转录本  
`hisat2` -> `StringTie` -> `Ballgown`  
 说明：结果文件放到`result/01.hisat`目录中。  
 
```
$ cd result
$ mkdir 01.hisat
$ mkdir 01.hisat/db
$ cd 01.hisat/db/
$ ln -s /bs1/data/NGS/data/ref/genome.fa ./
```

**参考基因组建索引**  

>**时间估计：5 min**

```
$ hisat2-build genome.fa genome
```
建完索引后会生成几个索引文件  
```
$ ls -l
-rw-rw-r--. 1 public public 31720791 11月  8 08:12 genome.1.ht2
-rw-rw-r--. 1 public public 20523760 11月  8 08:12 genome.2.ht2
-rw-rw-r--. 1 public public    85850 11月  8 08:10 genome.3.ht2
-rw-rw-r--. 1 public public 20523755 11月  8 08:10 genome.4.ht2
-rw-rw-r--. 1 public public 53804023 11月  8 08:12 genome.5.ht2
-rw-rw-r--. 1 public public 20682414 11月  8 08:12 genome.6.ht2
-rw-rw-r--. 1 public public        8 11月  8 08:10 genome.7.ht2
-rw-rw-r--. 1 public public        8 11月  8 08:10 genome.8.ht2
lrwxrwxrwx. 1 public public       32 11月  8 08:06 genome.fa -> /bs1/data/NGS/data/ref/genome.fa
```
**Mapping**

>**时间估计：4 h**

```
$ cd ../
$ mkdir mapping
$ cd mapping
$ hisat2 -x ../db/genome -1 ../../00.fq/WLD-1.R1.fastq.gz -2 ../../00.fq/WLD-1.R2.fastq.gz -S wild_1.sam
$ hisat2 -x ../db/genome -1 ../../00.fq/WLD-2.R1.fastq.gz -2 ../../00.fq/WLD-2.R2.fastq.gz -S wild_2.sam
$ hisat2 -x ../db/genome -1 ../../00.fq/WLD-3.R1.fastq.gz -2 ../../00.fq/WLD-3.R2.fastq.gz -S wild_3.sam
$ hisat2 -x ../db/genome -1 ../../00.fq/MUT-1.R1.fastq.gz -2 ../../00.fq/MUT-1.R2.fastq.gz -S mutant_1.sam
$ hisat2 -x ../db/genome -1 ../../00.fq/MUT-2.R1.fastq.gz -2 ../../00.fq/MUT-2.R2.fastq.gz -S mutant_2.sam
$ hisat2 -x ../db/genome -1 ../../00.fq/MUT-3.R1.fastq.gz -2 ../../00.fq/MUT-3.R2.fastq.gz -S mutant_3.sam
$ ll ./*.sam

```
**Sort & index BAM files**  

>**时间估计：5 h**

```
$ samtools view -b wild_1.sam | samtools sort -o wild_1.bam - 
$ samtools view -b wild_2.sam | samtools sort -o wild_2.bam - 
$ samtools view -b wild_3.sam | samtools sort -o wild_3.bam - 
$ samtools view -b mutant_1.sam | samtools sort -o mutant_1.bam - 
$ samtools view -b mutant_2.sam | samtools sort -o mutant_2.bam - 
$ samtools view -b mutant_3.sam | samtools sort -o mutant_3.bam - 
删除中间文件
$ rm *.sam

```
**组装**  

>**时间估计：1 h**

```
$ cd ../
$ mkdir assem
$ cd assem
$ stringtie -G /bs1/data/NGS/data/ref/gene.gff -o wild_1.gtf -l wild_1 ../mapping/wild_1.bam
$ stringtie -G /bs1/data/NGS/data/ref/gene.gff -o wild_2.gtf -l wild_2 ../mapping/wild_2.bam
$ stringtie -G /bs1/data/NGS/data/ref/gene.gff -o wild_3.gtf -l wild_3 ../mapping/wild_3.bam
$ stringtie -G /bs1/data/NGS/data/ref/gene.gff -o mutant_1.gtf -l mutant_1 ../mapping/mutant_1.bam
$ stringtie -G /bs1/data/NGS/data/ref/gene.gff -o mutant_2.gtf -l mutant_2 ../mapping/mutant_2.bam
$ stringtie -G /bs1/data/NGS/data/ref/gene.gff -o mutant_3.gtf -l mutant_3 ../mapping/mutant_3.bam
合并
$ ls *.gtf > mergelist.txt
$ stringtie --merge -G /bs1/data/NGS/data/ref/gene.gff -o stringtie_merge.gtf mergelist.txt

```
