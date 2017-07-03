---
layout: post
title: Set Up Hadoop Fully Distributed Cluster On AWS -- Advanced
category: Hadoop
tags: Hadoop
description: Big Data Learning -- Hadoop
---

- Set up Hadoop fully distributed Cluster on AWS (This is a instruction of four machines on AWS, Two Name Node(HA), and two Data Node)
and use zookeeper to handle fail-over automcatically.

## 0. Overview for the first Blog of Hadoop Cluster set up.

1). Hadoop-env.xml,mapred-env.xml,yarn-en.xml  (change JAVA_HOME)

2).	core-site.xml,yarn-site,hdfs-site,mapred-site

core-site (fs.defaultFS = hdfs://address:8020

Hadoop.tmp.dir (Create the folder by yourself)

3).	hdfs-site.xml

dfs.replication(default = 3)

dfs.permissions.enables = false (for testing only)

dfs.namenode.secondary.http-address = address:50090

4).	slaves include all the hostname (copy from hosts and remove the ip address)

## 1. Add JobHistory

1). Modify mapred-site.xml

	<property>
      <name>mapreduce.jobhistory.address</name>
      <value>http://ec2-52-54-83-82.compute-1.amazonaws.com:10020</value>
	</property>
	<property>
	  <name>mapreduce.jobhistory.webapp.address</name>
      <value>http://ec2-52-54-83-82.compute-1.amazonaws.com:19888</value>
	</property>

2). Modify yarn-site.xml

	<property>
      <name>yarn.log-aggregation-enable</name>
      <value>true</value>
	</property>

	(Below is optional)
	<property>
      <name>yarn.log-aggregation.retain-seconds</name>
      <value>106800</value>
	</property>

Distributed all configuration to every node.

Use "sbin/mr-jobhistory-daemon.sh start historyserver" to start the jobHistory Server.

## 2. Install ZooKeeper

1). Download ZooKeeper

2). Upload zookeeper-3.4.10.tar.gz to the machine.

	scp -i "HadoopTest.pem" zookeeper-3.4.10.tar.gz ubuntu@ec2-xx-xxx-xxx-xxx.compute-1.amazonaws.com:~/ (HadoopTest.pem is the keyPair file download from aws when the instance is launched.)

3). Rename zoo.smaple.cfg to conf/zoo.cfg

	mv conf/zoo.smaple.cfg conf/zoo.cfg

4). Modify zoo.cfg

	dataDir=/home/ubuntu/zookeeper-3.4.10/data/zkdata (This depends on youreslf)
	server.1=ec2-xx-xx-xx-xx.compute-1.amazonaws.com:2888:3888
	server.2=ec2-xx-xx-xx-xx.compute-1.amazonaws.com:2888:3888
	server.3=ec2-xx-xx-xx-xx.compute-1.amazonaws.com:2888:3888

5). Create zkdata folder

	Based on the configuration of zoo.cfd, create folder under path /home/ubuntu/zookeeper-3.4.10/data/zkdata

6). Add myid files

	Create "myid" file under zkdata folder. and then add the corresponding number such as 1,2,3 to the corresponding machine.

## 3. Implement HA

0). The import part of HA Set up
  a. share edits

  b. NameNode(Active, StandBy)

  c. Client (Proxy)

  d. Fence, There is only one NameNode which provides service for others.

1). How to set up

This is reference by the Hadoop Offical Website.

2). Modify hdfs-site.xml

	<property>
	  <name>dfs.nameservices</name>
	  <value>ns1</value>
	</property>

	<property>
	  <name>dfs.ha.namenodes.ns1</name>
	  <value>nn1,nn2</value>
	</property>

	<property>
	  <name>dfs.namenode.rpc-address.ns1.nn1</name>
	  <value>machine1.example.com:8020</value>
	</property>

	<property>
	  <name>dfs.namenode.rpc-address.ns1.nn2</name>
	  <value>machine2.example.com:8020</value>
	</property>

	##Namenode HTTP WEB ADDRESS
	<property>
	  <name>dfs.namenode.http-address.ns1.nn1</name>
	  <value>machine1.example.com:50070</value>
	</property>
	<property>
	  <name>dfs.namenode.http-address.ns1.nn2</name>
	  <value>machine2.example.com:50070</value>
	</property>

	##shared edits ADDRESS
	<property>
	  <name>dfs.namenode.shared.edits.dir</name>
	  <value>qjournal://node1.example.com:8485;node2.example.com:8485;node3.example.com:8485/ns1</value>
	</property>

	###CLIENT PROXY
	<property>
	  <name>dfs.client.failover.proxy.provider.ns1</name>  
	  <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
	</property>

	<property>
  	  <name>dfs.ha.fencing.methods</name>
      <value>shell(/bin/true)</value>
    </property>

	<property>
	  <name>dfs.journalnode.edits.dir</name>
	  <value>/path/to/journal/node/local/data</value>
	</property>

3). Modify core-site.xml

	<property>
	  <name>fs.defaultFS</name>
	  <value>hdfs://ns1</value>
	</property>

Distributed configuration files to all machines.

4). How to trigger the set up

step1: For every journalnode, input below command.
	sbin/hadoop-daemon.sh start journalnode

step2: In nn1, format the namenode, and then start
	bin/hdfs namenode -format
	sbin/hadoop-daemon.sh start namenode

step3: In machine nn2, Synchronize the meta data information
	bin/hdfs namenode –bootstrapStandby

step4:Start machine nn2
	sbin/hadoop-daemon.sh start namenode

step5：Start all DataNode

step6: Choose one machine to be the active machine (manually start up)

	bin/hdfs haadmin -transitionToActive nn1

5). Automatic Failover

0. After Start up, every machine is in standby status, and then choose one be the active by election.

Monitor by using ZKFC failover.

1. Modify the hdfs-site.xml

	<property>
	   <name>dfs.ha.automatic-failover.enabled</name>
	   <value>true</value>
	</property>

2. Modify the core-site.xml

	<property>
	   <name>ha.zookeeper.quorum</name>
	   <value>zk1.example.com:2181,zk2.example.com:2181,zk3.example.com:2181</value>
	</property>

3. How to start the set up

	sbin/stop-dfs.sh

	under zookeeper
	bin/zkServer.sh start

	under hadoop
	bin/hdfs zkfc –formatZK
	sbin/start-dfs.sh




