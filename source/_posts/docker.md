---
title: 大数据平台搭建(容器环境)
date: 2024-11-26 19:46:26
tags:
author: 你好
cover: /images/BigData.jpeg # 文章封面
---

# 大数据平台搭建(容器环境)

## 1. hadoop 完全分布式

### 把 jdk 文件从 software 解压到 module 目录

```
tar -zxvf jdk-8u212-linux-x64.tar.gz -C ../module/
```

### 配置 Java 环境并使其生效

```
vi /erc/profile
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/sbin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

```

### 退出刷新环境变量文件(没事修改环境文件都得刷新)

source /etc/profile

### 验证 JAVA 环境

java -version

### 配置免密登录(进入 hosts 文件配置上另外两个节点的 ip，三个节点但都需要配)

vi /etc/hosts

|     ip     | hostname  |
| :--------: | :-------: |
| 172.17.0.2 | bigdata01 |
| 172.17.0.3 | bigdata02 |
| 172.17.0.4 | bigdata03 |

### 修改 root 密码

passwd root

### 生成公钥和私钥(全部 Enter)

ssh-keygen

### 把公钥传给另外两个节点(需输入节点密码)

ssh-copy-id 节点名称

### 查看 ssh 服务是否成功

ssh localhost

### 把产生的公钥放置到 authorized_keys 文件中

cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

### 把配置好的 jdk 文件和环境变量文件传给另外两个节点

scp -r 文件路径 节点名称:文件路径

###

将 hadoop 与 flume 中 guava-27.0-jre.jar 包版本保持一致( 因为 hadoop 中此包版本是 27 而 flume 中版本是 11 )
rm -rf /opt/module/flume-1.9.0/lib/guava-11.0.2.jar
然后将 hadoop 里面的此包复制给 flume
cp /opt/module/hadoop-3.1.3/share/hadoop/common/lib/guava-27.0-jre.jar /opt/module/flume-1.9.0/lib/

修改 flume-env.sh 配置文件内容
export JAVA_HOME=/opt/module/jdk1.8.0_212

创建 flume-conf.properties 配置文件内容
a1.sources=r1
a1.sinks=k1
a1.channels=c1

a1.sources.r1.type=exec
a1.sources.r1.command=tail /opt/module/hadoop-3.1.3/logs/hadoop-root-namenode-bigdata01.log

a1.sinks.k1.type=hdfs
a1.sinks.k1.hdfs.path=hdfs://bigdata01:9000/tmp/flume/%Y%m%d
a1.sinks.k1.hdfs.filePrefix=log-
a1.sinks.k1.hdfs.fileType=DataStream
a1.sinks.k1.hdfs.useLocalTimeStamp=true

a1.channels.c1.type=memory
a1.sources.r1.channels=c1
a1.sinks.k1.channel=c1

将 Hadoop 的包复制到 flume 的 lib 目录下
/opt/module/hadoop-3.1.3/share/hadoop/common/hadoop-common-3.1.3.jar
/opt/module/hadoop-3.1.3/share/hadoop/common/lib/下的 hadoop-auth-3.1.3.jar 和 commons-configuration2-2.1.1.jar
将/opt/module/hadoop-3.1.3/etc/hadoop 下的 core-site.xml 和 hdfs-site.xm 文件复制到 conf 目录里
启动全部进程
start-all
在后台启动 flume
nohup flume-ng agent --conf conf/ --conf-file /opt/module/flume-1.9.0/conf/flume-conf.properties --name a1 -Dflume.root.logger=DEBUG,console &

查看生成的文件
hdfs dfs -ls /tmp/flume
hdfs dfs -ls /tmp/flume/20240108

flink on yarn
解压 flink 文件到 module
tar -zxvf /opt/software/flink-1.14.0-bin-scala_2.12.tgz -C /opt/module/
配置环境变量
vi /etc/profile
export FLINK_HOME=/opt/module/flink-1.14.0
export PATH=$FLINK_HOME/bin:$FINK_HOME/sbin:$PATH
export HADOOP_CLASSPATH=`hadoop classpath`
export HADOOP_CONF_DIR=/opt/module/hadoop-3.1.3/etc/hadoop

验证环境变量正确性
source /etc/profile
flink –version
配置相关文件
修改 conf 目录下的 flink-conf.yaml 文件
classloader.check-leaked-classloader:空格 false
启动 flink 集群
./ flink-console.sh
使用 jps 查看进程

在 bin 目录下提交 flink
flink run -m yarn-cluster -p 2 -yjm 2G -ytm 2G $FLINK_HOME/examples/batch/WordCount.jar

Hive
复制 hive-env.sh.template 和 hive-default.xml.template 并改名
cp hive-env.sh.template hive-env.sh
cp hive-default.xml.template hive-site.xml

配置 hive-env.sh.template 和 hive-default.xml.template 文件

env
export JAVA_HOME=/opt/module/jdk
export HADOOP_HOME=/opt/module/hadoop
export HIVE_HOME=/opt/module/hive
export HIVE_CONF_DIR=$HIVE_HOME/conf

site(修改配置)
<property>
<name>javax.jdo.option.ConnectionURL</name>
<value>jdbc:mysql://你的虚拟机 IP:3306/hivedb?createDatabaseIfNotExist=true&amp;useSSL=false</value>
<description> localhost 虚拟机 IP 地址，hivedb 数据库的名字（随意）createDatabaseIfNotExist 如果数据库不存在自动创建</description>
</property>

<!--配置数据库驱动-->
<property>
	<name>javax.jdo.option.ConnectionDriverName</name>
	<value>com.mysql.jdbc.Driver</value>
</property>
<!--配置数据库用户名-->
<property>
	<name>javax.jdo.option.ConnectionUserName</name>
	<value>你的用户（一般为root）</value>
</property>
<!--配置MySQL数据库root的密码-->
<property>
	<name>javax.jdo.option.ConnectionPassword</name>
	<value>你的密码</value>
</property>

把 Hadoop 的 guava 文件复制到 hive 下的 lib 目录(再把 hive 的低版本 guava 包删除)
cp /opt/module/hadoop-3.1.3/share/hadoop/common/lib/guava-27.0-jre.jar /opt/module/hive-3.1.2/lib
rm -rf rm -rf guava-19.0-jre.jar

Spark
vi spark-env.sh
export JAVA_HOME=/opt/module/jdk1.8.0_212
export SPARK_BIGDATA01_IP=bigdata01
export SPARK_BIGDATA01_HOST=bigdata01
export SPARK_BIGDATA01_PORT=7077
export HADOOP_HOME=/opt/module/hadoop-3.1.3
export YARN_CONF_DIR=${HADOOP_HOME}/etc/hadoop
export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop

Hbase
site
进入/opt/module/hbase-2.2.3/conf 下编辑 hbase-site.xml 文件
<configuration>
<property>
<name>hbase.zookeeper.quorum</name> <value>master,slave1,slave2</value>
</property>
<property>
<name>hbase.rootdir</name>
<value>hdfs://master:9000/hbase</value>
</property>
<property>
<name>hbase.cluster.distributed</name>
<value>true</value> </property>
<property>
<name>hbase.master.info.port</name>
<value>60010</value>
</property>
<property>
<name>hbase.unsafe.stream.capability.enforce</name>
<value>false</value>
</property>
</configuration>
env
JAVA_HOME

kafka
修改 Kafka 里的 conf 目录里的 server.properties 文件(找到并修改)
log.dirs=/opt/module/kafka_2.12-2.4.1/logs
zookeeper.connect=bigdata01:2181, bigdata02:2181, bigdata03:2181
kafka-server-start.sh –version
在/opt/module/kafka_2.12-2.4.1/conf 下启动 Kafka(在三个节点都需要)
kafka-server-start.sh -daemon server.properties
创建 Topic 并命名为 installtopic，分区数为 2，副本数为 2

clickhouse
启动 clickhouse
systemctl start clickhouse-server
查看运行状态
systemctl status clickhouse-server
