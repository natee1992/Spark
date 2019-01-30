### Spark Core

- 什么是RDD

- RDD特性

- RDD特性在源码中的体现

- 图解RDD

- SparkContext & SprakConf

- pyspark 脚本解析

- RDD的创建方式

- Spark应用程序开发和运行

### Spark源码

官网： spark.apache.org
源码： http://github.apache/spark

- RDD
A Resilient Distributed Dataset (RDD)
弹性  分布式    数据集

Represents an ：
    immutable, 不可变
    partitioned collection of elements 分区
        Array(1,2,3,4,5,6,7,8,9)   3个分区（1，2，3）（4，5，6）（7，8，9）
    that can be operated on in parallel. 并行计算的问题



```
abstract class RDD[T: ClassTag](
    @transient private var _sc: SparkContext,
    @transient private var deps: Seq[Dependency[_]]
  ) extends Serializable with Logging {

```

1.  RDD是一个抽象类
2.  带泛型的，可以支持多种类型： String、Person、User

单机存储/计算 ==》分布式储存/计算

1.  数据的存储：切割。 HDFS的Block
2.  数据的计算：切割（分布式并行计算） MapReduce/Spark
3.  存储+计算： HDFS/S3+MapReduce/Spark

- RDD特性

    - A list of partitions
        一个RDD有一系列的分片（partitions）类似HDFS有多个block

    - A function for computing each split/partitions
        y = f(x)
        rdd.map(_ + 1)  ==> 对所有split 执行相同函数操作

    - A list of dependencies on other RDDs
        rdd1 ==> rdd2 ==> rdd3(rdd不可变)(rdd之间存在依赖关系)
        dependencies: ******
        ep: rdda = 5个partitions
        ==>
        rddb = 5个partitions


    - Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)

    - Optionally, a list of preferred locations to compute each split on (e.g. block locations for
      an HDFS file)
        数据在哪，优先把作业调度到数据所在的节点进行计算：移动数据不如移动计算 

5大特性源码体现

def compute(split: Partition, context: TaskContext): Iterator[T]  特性2
protected def getPartitions: Array[Partition]   特性1
protected def getDependencies: Seq[Dependency[_]] = deps   特性3
def getPreferredLocations(split: Partition): Seq[String]   特性5
val partitioner: Option[Partitioner] = None  特性4
