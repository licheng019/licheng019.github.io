---
layout: post
title: Set up Hadoop fully distributed Cluster on AWS
category: Hadoop
tags: Hadoop
description: Big Data Learning -- Hadoop
---

- Set up Hadoop fully distributed Cluster on AWS (This is a instruction of four machines on AWS, one Name Node, One Secondary Node and two Data Node)

## 0. Hadoop 2.x Introduction

There are four parts components for Hadoop 2.x

1). Common Component

Hadoop Common refers to the collection of common utilities and libraries that support other Hadoop modules.

2). HDFS

HDFS is a Java-based file system that provides scalable and reliable data storage, and it was designed to span large clusters of commodity servers.

3). MapReduce

MR is a framework to define a data processing task, was focused on what logical operations you want to perform on the raw data that you have.

4). YARN

YARN was the framework to run the processing task across multiple machines, manage resources, manage memory, manage storage requirments. It didn't care what the data processing task was.

## 1. Preparation on Local

1). Launch 4 Instances on AWS with Ubuntu System(don't recommend use t2.micro, This will be extermely slow to run the mapreduce)

2). Download Java Jdk 1.8

3). Download Hadoop ( I am using Hadoop.2.7.3.tar.gz as the example)

## 2. Preparation on AWS

1). Upload Jdk and Hadoop to AWS

	scp -i "HadoopTest.pem" hadoop-2.7.3.tar.gz ubuntu@ec2-xx-xxx-xxx-xxx.compute-1.amazonaws.com:~/ (HadoopTest.pem is the keyPair file download from aws when the instance is launched.)

2). Login to the AWS

	ssh -i "HadoopTest.pem" ubuntu@ec2-xx-xxx-xxx-xxx.compute-1.amazonaws.com

3). Unzip File

	tar xzvf hadoop-2.7.3.tar.gz
	tar zxvf jdk-18u3xxxx.tar.gz

4). Copy JDK to /usr/lib

	cp -R jdk1.8 /usr/lib

5). Set up Environment Variable
	
	sudo vi ~/.bashrc
	export HADOOP_PREFIX=/home/ubuntu/hadoop-2.7.3
	#Set JAVA_HOME
	export JAVA_HOME=/usr/lib/jdk1.8.0_131
	# Add Hadoop bin/ directory to path
	export PATH=$PATH:$HADOOP_PREFIX/bin

	sudo vi /etc/profile
	JAVA_HOME=/usr/lib/jdk1.8.0_131
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin
	export JAVA_HOME
	export PATH

	source ~/.bashrc
	source /etc/profile

6). Modify hosts File

Add all ip -> address map in /etc/hosts file.

7). Set up SSH

	eval `ssh-agent -s`
	ssh-add hadoopTest.pem

Because the characteristic of ssh-agent, This command needs to run every time when the user login. So add above command under $HOME/.bash_profile


## 3. Configure the Hadoop Configuration File
Going to the folder under hadoop/etc/hadoop

1). hadoop-env.sh, yarn-env.sh, mapred-env.sh

Add below command in above three *-env.sh file.

	export JAVA_HOME=/home/ubuntu/jdk1.8.0_131

2). core-site.xml
This is a configuration common across Hadoop, it’s not component-specific configuration file

	<property>
        <name>fs.defaultFS</name>
        <value>hdfs://<address>:9000</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/home/ubuntu/hdfstmp</value>
	</property>

The "fs.defaultFs" property points to what file system that want to use with Hadoop(use hdfs, and can be accessed on 9000).

The "hadoop.tmp.dir" is used to saving hadoop fsimage and edits file.(It needs to be initialize format, will mention this in the end of the blog).

3). hdfs-site.xml
Hdfs-site.xml

	(Optional)
	<property>
	    <name>dfs.replication</name> 
	    <value>1</value>
	</property>

replication is a way by which fault tolerance and recovery is built into Hadoop,default value is 3. 

4). mapred-site.xml
There is no mapred-site.xml exists under /etc/hadoop. copy or modifh the existing file mapred-site.xml.template to mapred-site.xml 

cp mapred-site.xml.template mapred-site.xml

    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

 The "mapreduce.framework.name" specified the resource manager or the recource negotiator that Mapreduce framework is going to use, available value can be local, classic and yarn). What is the difference? Check below.

	classic: Mapreduce version 1
	local: running Mapreduce locally
	yarn: MapReduce version 2

5). yarn-site.xml

	<property>
	  <name>yarn.resourcemanager.hostname</name>
	  <value>address</value>
	</property>
	<property>
	  <name>yarn.nodemanager.aux-services</name>
	  <value>mapreduce_shuffle</value>
	</property>

The basic Configuration is done here. I am going to explore more configuration and detail in the later blog.

6). Format NameNode 

Before start, the first thing is format the name node. The name node is the master node in cluster, it keeps track of all the other nodes on which processes run below command:

	bin/hdfs namenode –format (make sure there is no error)


7). Start Hadoop

	sbin/start-dfs.sh (Start NameNode and DataNode)
	sbin/start-yarn.sh (Start ResourceManager and NodeManager)

8). How to check whether the Hadoop is running

Open browser, input address:50070, and then you will see the hadoop page.


