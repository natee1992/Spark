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

