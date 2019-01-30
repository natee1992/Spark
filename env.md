### 环境搭建

- java

- Scala

- Hadoop

- Maven

- Python3
 
- Spark

---

#### 环境变量

> vim ~/.bash_profile
```
export JAVA_HOME=/home/hadoop/app/jdk1.8.0_91
export PATH=$JAVA_HOME/bin:$PATH

export SCALA_HOME=/home/hadoop/app/scala-2.11.8
export PATH=$SCALA_HOME/bin:$PATH


export HADOOP_HOME=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0
export PATH=$HADOOP_HOME/bin:$PATH

export MAVEN_HOME=/home/hadoop/app/apache-maven-3.3.9
export PATH=$MAVEN_HOME/bin:$PATH

export PATH=/home/hadoop/app/python3/bin:/usr/bin/python:$PATH

export PYSPARK_PYTHON=python

export SPARK_HOME=/home/hadoop/app/spark-2.3.0-bin-2.6.0-cdh5.7.0
export PATH=$SPARK_HOME/bin:$PATH


```
- hadoop 环境搭建

**HDFS相关**

*hadoop-env.sh*

> vim hadoop-2.6.0-cdh5.7.0/etc/hadoop/hadoop-env.sh

查看java环境

> echo $JAVA_HOME

```
# 将环境变量配入
#export JAVA_HOME=${JAVA_HOME}   # -----  注释


export JAVA_HOME=/home/hadoop/app/jdk1.8.0_91

```
*core-site.xml*

```
# 插入node-name
<configuration>
<property>
  <name>fs.default.name</name>
  <value>hdfs://hadoop000:8020</value>
</property>
</configuration>
```

*hdfs-site.xml*

```
<configuration>
<property>
<name>dfs.namenode.name.dir</name>
<value>/home/hadoop/app/tmp/dfs/name</value>
</property>

<property>
<name>dfs.datanode.data.dir</name>
<value>/home/hadoop/app/tmp/dfs/data</value>
</property>


<property>
<name>dfs.replication</name>
<value>1</value>
</property>
</configuration>
```
**yarn相关**

*mapred-site.xml*

```
<configuration>
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
</configuration>
```

*yarn-site.xml*

```
configuration>

<!-- Site specific YARN configuration properties -->

<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
</configuration>
```

#### 启动

- 启动HDFS前格式化

*格式化之前需要将tmp中的文件删除，否则会出现datanode无法启动，原因如下*

```
当我们使用hadoop namenode -format格式化namenode时，会在namenode数据文件夹（这个文件夹为自己配置文件中dfs.name.dir的路径）中保存一个current/VERSION文件，记录clusterID，datanode中保存的current/VERSION文件中的clustreID的值是上一次格式化保存的clusterID，这样，datanode和namenode之间的ID不一致。
```

> cd hadoop-2.6.0-cdh5.7.0/bin
> ./hadoop namenode -format

- 启动HDFS

> cd hadoop-2.6.0-cdh5.7.0/sbin
> ./start-dfs.sh 
> jps

查看启动情况

```
15600 Jps
14824 NameNode
13595 SecondaryNameNode
15372 DataNode
```
查看hadoop
> hadoop fs -ls /

创建test
> hadoop fs -mkdir /test

*此操作有可能出现：Cannot create directory /tmp. Name node is in safe mode.*
*原因：hdfs在启动开始时会进入安全模式，这时文件系统中的内容不允许修改也不允许删除，直到安全模式结束。安全模式主要是为了系统启动的时候检查各个DataNode上数据块的有效性，同时根据策略必要的复制或者删除部分数据块。运行期通过命令也可以进入安全模式。在实践过程中，系统启动的时候去修改和删除文件也会有安全模式不允许修改的出错提示*

可以手动取消安全模式
>  hadoop dfsadmin -safemode leave

上传小文件测试
> hadoop fs -put xxxx /test/

查看是否上传成功
> hadoop fs -ls /test/

读取上传文件
> hadoop fs -text /test/xxxx

*打开网页查看hadoop ip:50070*

- 启动YARN

> ./start-yarn.sh

> jps

```
17008 Jps
16739 NodeManager
14824 NameNode
16571 ResourceManager
13595 SecondaryNameNode
15372 DataNode
```
*打开网页查看hadoop ip:8088*

创建maven本地资源库

> mkdir maven_repository

修改maven设置
> vim /conf/settings

```
<localRepository>/home/hadoop/maven_repository</localRepository>
```

验证mvn安装成功
> mvn

- Spark安装

选择2.3以上的版本，soucecode 模式安装

> wget https://archive.apache.org/dist/spark/spark-2.3.2/spark-2.3.2.tgz

> ./dev/make-distribution.sh --name 2.6.0-chd5.7.0 --tgz -Pyarn -Phadoop-2.6 -Phive -Phive-thriftserver -Dhadoop.version=2.6.0-cdh5.7.0

*解压spark*

> tar -zxvf  spark-2.3.0-bin-2.6.0-cdh5.7.0.tgz  -C ~/app/
*设置spark env*
> cp spark-env.sh.template spark-env.sh
> vim spark-env.sh
```

HADOOP_CONF_DIR=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop
SCALA_HOME=/home/hadoop/app/scala-2.11.8
JAVA_HOME=/home/hadoop/app/jdk1.8.0_91
PYTHON_HOME=/home/hadoop/app/python3/bin


SPARK_HISTORY_OPTS="-Dspark.history.fs.logDirectory=hdfs://hadoop000:8020/directory"


```
---

### Spark Core

- 什么是RDD

- RDD特性

- RDD特性在源码中的体现

- 图解RDD

- SparkContext & SprakConf

- pyspark 脚本解析

- RDD的创建方式

- Spark应用程序开发和运行

