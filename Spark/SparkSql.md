## SparkSQL问题汇总
- [SparkSQL问题汇总](#sparksql问题汇总)
  - [1、distribute by-SQL语法](#1distribute-by-sql语法)
  - [2、分区列和内容列](#2分区列和内容列)
  - [3、textFile扫描HDFS文件时计算分区数](#3textfile扫描hdfs文件时计算分区数)

### 1、distribute by-SQL语法
```sql
insert overwrite table my_table partition(a,b)
select ta.c as c,
       ta.d as d,
       ta.a as a,
       ta.b as b 
from origin ta
inner join data_count tb
using(a,b)
distribute by a,d
```

这段sql会解析生成的AST，先进行select中的project算子，然后在进行distribute by的repartition操作。

也就是如果要在distribute by中使用的字段，一定要在select中出现，不然distribute by会识别不到对应的project的scheme信息

### 2、分区列和内容列
* 分区列按文件夹来组织
* 内容按文件来组织
* 实际针对每个分区下的具体hdfs文件，是不会把分区列的内容写进到hdfs的压缩文件中


### 3、textFile扫描HDFS文件时计算分区数
* 对输入的文件做切片处理，以切片读取，**分区数=切片数**
* maxSplit函数来计算切片大小
  ```scala
  def maxSplitBytes(
      sparkSession: SparkSession,
      selectedPartitions: Seq[PartitionDirectory]): Long = {
    //spark.sql.files.maxPartitionBytes default 128M 
    val defaultMaxSplitBytes = sparkSession.sessionState.conf.filesMaxPartitionBytes 
    
    // spark.sql.files.openCostInBytes default 4M
    val openCostInBytes = sparkSession.sessionState.conf.filesOpenCostInBytes 
    
    //spark.default.parallelism default 3
    val defaultParallelism = sparkSession.sparkContext.defaultParallelism
   
    val totalBytes = selectedPartitions.flatMap(_.files.map(_.getLen + openCostInBytes)).sum
    val bytesPerCore = totalBytes / defaultParallelism

    Math.min(defaultMaxSplitBytes, Math.max(openCostInBytes, bytesPerCore))
  }
  ```
* 从代码看：大数据量下影响这个切片大小主要因素是当天有多少数量的数据，跟 **文件数** 影响关系不大



