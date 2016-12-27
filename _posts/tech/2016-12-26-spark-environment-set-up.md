---
layout: post
title: Spark Cluster Set up
category: Spark
tags: Spark
description: Spark Learning
---

- Set up Spark Cluster in Virtual Box

## 1. Virtual Box

1)Download Virtual Box from below <a href="https://www.virtualbox.org/wiki/Downloads">Link</a>

2)Using Centos As the Image.

3)Create three VMs(Spark1, Spark2, Spark3)

4)Setup /etc/sysconfig/network-scripts/ifcfg-eth0 for network

5)turn off firwall(Testing and exercise only)

	service iptables stop
	chkconfig iptables off
	vi /etc/selinux/config
	SELINUX=disabled

6)yum install telnet

7)install java, set up java environment

	export JAVA_HOME=/usr/java/latest
	export PATH=$PATH:$JAVA_HOME/bin
	source .bashrc

8)check jdk working: java -version

## 2.Hadoop Install

1)download hadoop into /usr/local

2)upzip hadoop file

3)change hadoop folder name to hadoop

4)set up hadoop environment

	vi .bashrc
	export HADOOP_HOME=/usr/local/hadoop
	export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin
	source .bashrc

5)Modify core-site.xml (Set up hdfs cluster for outside)

	<property>
  	  <name>fs.default.name</name>
  	  <value>hdfs://spark1:9000</value>
	</property>

6)Modify hdfs-site.xml

	<property>
	  <name>dfs.name.dir</name>
	  <value>/usr/local/data/namenode</value>
	</property>
	<property>
	  <name>dfs.data.dir</name>
	  <value>/usr/local/data/datanode</value>
	</property>
	<property>
	  <name>dfs.tmp.dir</name>
	  <value>/usr/local/data/tmp</value>
	</property>
	<property>
	  <name>dfs.replication</name>
	  <value>3</value>
	</property>

Manually  create data folder under /usr/local

7)Modify mapred-site.xml

	<property>
	  <name>mapreduce.framework.name</name>
	  <value>yarn</value>
	</property>

8)Modify yarn-site.xml

	<property>
	  <name>yarn.resourcemanager.hostname</name>
	  <value>spark1</value>
	</property>
	<property>
	  <name>yarn.nodemanager.aux-services</name>
	  <value>mapreduce_shuffle</value>
	</property>

9)Modify slaves file

	spark1
	spark2
	spark3

10)copy configuration to spark2, spark3

11)format namenode

	In spark1: run hdfs namenode -format
	start hdfs cluster: start-dfs
	Verify Success: jps(show below name)
	spark1：namenode、datanode、secondarynamenode
	spark2：datanode
	spark3：datanode
	Run spark1:50070 for checking the page

12)Start yarn cluster

	start-yarn.sh
	Verify Success: jps(show below name)
	spark1：resourcemanager、nodemanager
	spark2：nodemanager
	spark3：nodemanager
	Run spark1:8088 for checking the page

