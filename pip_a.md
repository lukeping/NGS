# 有参考基因组，但需要组装新的转录本  
`hisat2` -> `StringTie` -> `Ballgown`  
 说明：结果文件放到`result/01.hisat`目录中。  
 
```
> cd result
> mkdir 01.hisat
> mkdir 01.hisat/db
> cd 01.hisat/db/
> ln -s /bs1/data/NGS/data/ref/genome.fa ./
```

** 参考基因组建索引  
```
> hisat2-build genome.fa genome
```
建完索引后会生成几个索引文件，  
```
> ls -l
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
