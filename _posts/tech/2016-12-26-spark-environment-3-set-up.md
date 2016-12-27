---
layout: post
title: Spark Cluster Set up 3
category: Spark
tags: Spark
description: Spark Learning (set up Kafka, Spark)
---

- Set up Spark Cluster in Virtual Box

## 1. Kafka

1)Install Scala First

2)Download Kafka zip

2)unzip Kafka file, and then change the name to kafka

3)set up kafka environment

	vi /usr/local/kafka/config/server.properties
	broker.id: 0、1、2、3,Cluster Brokeris the only id
	zookeeper.connect=192.168.1.107:2181,192.168.1.108:2181,192.168.1.109:2181

4)install slf4j, and then copy slf4j-nop-1.7.6.jar under kafka/libs

5)start, go to the folder of kafka

	nohup bin/kafka-server-start.sh config/server.properties &

6)use jps to check whether it is success

## 2.Spark

1)Download Spark zip file

2)unzip Spark file, and then change the name to spark

3)set up Spark environment

	vi .bashrc
	export SPARK_HOME=/usr/local/spark
	export PATH=$SPARK_HOME/bin
	export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
	source .bashrc

4)Modify spark-env.sh

	cd /usr/local/spark/conf
	cp spark-env.sh.template spark-env.sh
	vi spark-env.sh
	export JAVA_HOME=/usr/java/latest
	export SCALA_HOME=/usr/local/scala
	export SPARK_MASTER_IP=192.168.1.107
	export SPARK_WORKER_MEMORY=1g
	export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop

5)Modify slaves file

	spark1
	spark2
	spark3

6)Start

	got to spark/sbin
	run ./start-all.sh
	use jps and spark1:8080 to check whether it is success







