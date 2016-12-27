---
layout: post
title: Spark Cluster Set up 2
category: Spark
tags: Spark
description: Spark Learning (set up Hive, Zookeeper)
---

- Set up Spark Cluster in Virtual Box

## 1. Hive

1)Download Hive zip

2)unzip Hive file, and then change the name to hive

3)set up hive environment

	vi .bashrc
	export HIVE_HOME=/usr/local/hive
	export PATH=$HIVE_HOME/bin
	source .bashrc

4)Install mysql on spark1, follow below instructions

	yum install -y mysql-server
	service mysqld start
	chkconfig mysqld on
	yum install -y mysql-connector-java
	cp /usr/share/java/mysql-connector-java-5.1.17.jar /usr/local/hive/lib

5)set up hive meta database in mysql, and then authorize

	create database if not exists hive_metadata;
	grant all privileges on hive_metadata.* to 'hive'@'%' identified by 'hive';
	grant all privileges on hive_metadata.* to 'hive'@'localhost' identified by 'hive';
	grant all privileges on hive_metadata.* to 'hive'@'spark1' identified by 'hive';
	flush privileges;
	use hive_metadata;

6)Modify hive-site.xml

	mv hive-default.xml.template hive-site.xml
	vi hive-site.xml
	<property>
	  <name>javax.jdo.option.ConnectionURL</name>
	  <value>jdbc:mysql://spark1:3306/hive_metadata?createDatabaseIfNotExist=true</value>
	</property>
	<property>
	  <name>javax.jdo.option.ConnectionDriverName</name>
	  <value>com.mysql.jdbc.Driver</value>
	</property>
	<property>
	  <name>javax.jdo.option.ConnectionUserName</name>
	  <value>hive</value>
	</property>
	<property>
	  <name>javax.jdo.option.ConnectionPassword</name>
	  <value>hive</value>
	</property>
	<property>
	  <name>hive.metastore.warehouse.dir</name>
	  <value>/user/hive/warehouse</value>
	</property>

7)Modify hive-env.sh and hive-config.sh

	mv hive-env.sh.template hive-env.sh

	vi /usr/local/hive/bin/hive-config.sh
	export JAVA_HOME=/usr/java/latest
	export HIVE_HOME=/usr/local/hive
	export HADOOP_HOME=/usr/local/hadoop

8)type hive

## 2. ZooKeeper

1)Download ZooKeeper zip

2)unzip ZooKeeper file, and then change the name to zk

3)set up ZooKeeper environment

	vi .bashrc
	export ZOOKEEPER_HOME=/usr/local/zk
	export PATH=$ZOOKEEPER_HOME/bin
	source .bashrc

4)Modify zoo.cfg

	cd zk/conf
	mv zoo_sample.cfg zoo.cfg
	vi zoo.cfg
	Modify:
	dataDir=/usr/local/zk/data
	Add:
	server.0=spark1:2888:3888	
	server.1=spark2:2888:3888
	server.2=spark3:2888:3888

5)Set up zk node identity

	cd zk
	mkdir data
	cd data
	vi myid
	0

6)Set up Zookeeper Cluster

Copy the setup from spark1 to spark2 and spark3

Only differenct is change myid to 1 and 2.

7)Start ZooKeep on All machine

	zkServer.sh start

8)Check ZooKeeper Status

	zkServer.sh status
