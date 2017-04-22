# 高通量测序数据分析实践操作  

## 一、操作说明    


**说明：`$`后为命令，其他的为输出或说明。**  

```
ssh登录服务器172.28.215.241，用户名：ngs

加载环境变量
$ module add bioinfo

对于计算量较大的任务，请不要在登录节点上直接运行，用qsub提交任务
```
### Linux基本操作  

**掌握以下常用的Linux命令：**

|  |  |  |  |  |   |   
|  --- | --- | --- | --- | --- | ---  |
|  ls | cd | mv | rm | pwd | top  |
|  ps | grep | kill | file | tar | cat  |
|  less | more | chmod | chown | vim (vi) | time  |
|  cp | scp | ln | mkdir | wget | git  |
|  find | export | source | mount | head | tail  |

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

## 模块  
1. RNA-Seq  
2. 基因组组装（PacBio序列）  
