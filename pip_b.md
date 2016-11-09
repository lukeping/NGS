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
$ mkdir db
$ ln -s /bs1/data/NGS/data/ref/transcripts.fa ./
$ kallisto index -i transdb transcripts.fa
```
