---
title: HDFS (Hadoop Distributed File System) 
categories: 指南
tags:
  - Hadoop
  - HDFS
---

HDFS是Hadoop开发的前提，如果没有HDFS，一切大数据开发的设想都不可能实现。

<!-- more -->

#### HDFS 概述

回忆一下在HDFS出现之前我们如何处理海量数据？I/O读取文件，不论是逐行还是完全加载到内存，其处理效率与安全性都令人失望。当然，在绝大多数应用内，"海量"数据的数据量不会大到处理困难的地步，而且往往我们对于分析速度并非有过于苛刻的敏感，所以通常这似乎不是很大的问题，但如果每天甚至是每小时产生的数据量达到GB级别且对效率要求苛刻(分钟级别)，我们又该怎么做呢？多线程？磁盘阵列？这些看起来是不错的解决方案，但这样对数据安全来说风险过大，若要保证数据安全，又需要在前期耗费大量的精力进行设计。

但HDFS的出现使这些原始数据有了可靠的安身之所，也使得接下来的数据操作成为了可能，在实际使用中，每个文件只有一个绝对路径，我们只需要向HDFS内的namenode发出路径请求，就可以明确的访问需要访问的文件，而不需要关心其背后的磁盘阵列与线程分发，HDFS内建的多节点数据拷贝可以保证数据的存储安全与访问性能，除非所有namenode(最近新的版本已支持多个namenode)全部物理损坏，即便如此还有secondary namenode以尽量减少数据丢失。

#### HDFS Commands

完整的HDFS指令请参考官方文档:

- [HDFS Commands Guide (2.7.5)](https://hadoop.apache.org/docs/r2.7.5/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#dfs)

在HDFS指令中，最常用的是其`dfs`指令，其基本格式如下:

```shell
hdfs dfs [COMMAND [COMMAND_OPTIONS]]
```

完整的`COMMAND_OPTIONS `可以在这里找到:

- [File System Shell Guide (2.7.5)](https://hadoop.apache.org/docs/r2.7.5/hadoop-project-dist/hadoop-common/FileSystemShell.html)

常用的DFS指令如下:

```shell
#新建目录
hdfs dfs -makir /user/root/input

#列出目录内结构
hdfs dfs -ls /user/root/input

#查看文件内容(短文本)
hdfs dfs -cat /user/root/output/result-part-00000

#删除文件
hdfs dfs -rm /user/root/input/source.log

#删除目录
hdfs dfs -rm -r /user/root/output

#向HDFS系统内存储文件
hdfs dfs -put ~/source/2018-07-11.log hdfs://cdh0/user/root/input

#从HDFS拉取文件到本地
hdfs dfs -get hdfs://cdh0/user/root/input/2018-07-11.log ~/source/
```

{% note info%} 

在实际使用中，我较多的使用`hadoop fs`而非`hdfs dfs`，他们在大多数场景下基本是相同的，但总体来讲，`hadoop fs`所覆盖的范围更广，如果想了解更多关于这两个指令之间的区别，请访问 [what's the difference between “hadoop fs” shell commands and “hdfs dfs” shell commands?](https://stackoverflow.com/questions/18142960/whats-the-difference-between-hadoop-fs-shell-commands-and-hdfs-dfs-shell-co) 寻求参考或参与讨论

{% endnote %}

