---
layout: post
title: CDH Set up Hadoop, Hive and Sqoop1
category: Hadoop
tags: Hadoop
description: Big Data Learning(Basic)
---

- Set up Hadoop, Hive and Sqoop by using CDH

## 1. Download files

Download below files from Link of http://archive.cloudera.com/cdh5/cdh/5/
(These are the file I am using in this set up)

	hadoop-2.5.0-cdh5.3.6.tar.gz
	hive-0.13.1-cdh5.3.6.tar.gz
	sqoop-1.4.5-cdh5.3.6.tar.gz

## 2. Unzip files
	
	tar -zxvf hadoop-2.5.0-cdh5.3.6.tar.gz -C /opt/cdh-5.3.6
	tar -zxvf hive-0.13.1-cdh5.3.6.tar.gz -C /opt/cdh-5.3.6
	tar -zxvf sqoop-1.4.5-cdh5.3.6.tar.gz -C /opt/cdh-5.3.6

## 3. Config Hadoop

Go to hadoop directory which is /opt/cdh-5.3.6/hadoop-2.5.0-cdh5.3.6

1). Modify etc/hadoop/hadoop-env.sh

	export JAVA_HOME=/usr/java/jdk1.7.0_65

2). Modify etc/hadoop/yarn-env.sh

	export JAVA_HOME=/usr/java/jdk1.7.0_65

3). Modify etc/hadoop/mapred-env.sh

	export JAVA_HOME=/usr/java/jdk1.7.0_65

4). Modify core-site.xml

create folder data/tmp first and then add below property under Configuration

	<property>
	        <name>fs.defaultFS</name>
	        <value>hdfs://<hostname>:8020</value>
	</property>
	<property>
	        <name>hadoop.tmp.dir</name>
	        <value>/opt/cdh-5.3.6/hadoop-2.5.0-cdh5.3.6/data/tmp</value>
	</property>

5). Modify hdfs-site.xml

add below property under Configuration

	<property>
	    <name>dfs.replication</name>
	    <value>1</value>
	</property>
	<property>
	    <name>dfs.permission.enabled</name>
	    <value>false</value>
	</property>
	<property>
	    <name>dfs.namenode.secondary.http-address</name>
	    <value><hostname>:50090</value>
	</property>
	<property>
	    <name>dfs.namenode.secondary.https-address</name>
	    <value><hostname>:50091</value>
	</property>

6). Modify slaves.xml

	<hostname>

7). Format
	
	bin/hdfs namenode -format

8). Modify yarn-site.xml

add below property under Configuration

	<property>
	    <name>yarn.nodemanager.aux-services</name>
	    <value>mapreduce_shuffle</value>
	</property>
	<property>
	    <name>yarn.resourcemanager.hostname</name>
	    <value>spark1</value>
	</property>
	<property>
	    <name>yarn.log-aggregation-enable</name>
	    <value>true</value>
	</property>
	<property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>106800</value>
    </property>

9). Modify mapred-site.xml.template

Rename mapred-site.xml.template to mapred-site.xml first and then add below property under Configuration

	<property>
	    <name>mapreduce.framework.name</name>
	    <value>yarn</value>
	</property>
	<property>
	    <name>mapreduce.jobhistory.address</name>
	    <value>spark1:10020</value>
	</property>
	<property>
	    <name>mapreduce.jobhistory.webapp.address</name>
	    <value>spark1:19888</value>
	</property>

10). Start hadoop
	
Run below command to start namenode, datanode, resourcemanager, nodemanager and historyserver

	sbin/hadoop-daemon.sh start namenode
	sbin/hadoop-daemon.sh start datanode
	sbin/yarn-daemon.sh start resourcemanager
	sbin/yarn-daemon.sh start nodemanager
	sbin/mr-jobhistory-daemon.sh start historyserver

## 4. Config Hive

Go to hive directory which is /opt/cdh-5.3.6/hive-0.13.1-cdh5.3.6


1). Modify hive-env.sh

Go to conf folder, rename hive-env.sh.template to hive-env.sh
and then modify the file

	HADOOP_HOME= /opt/cdh-5.3.6/hadoop-2.5.0-cdh5.3.6
	export HIVE_CONF_DIR= /opt/cdh-5.3.6/hive-0.13.1-cdh5.3.6/conf

2). Modify hive-log4j.properties.template

rename hive-log4j.properties.template to hive-log4j.properties

modify hive-log4j.properties

	hive.log.dir=/opt/cdh-5.3.6/hive-0.13.1-cdh5.3.6/logs

3). Create hive-site.xml

Add below informaiton to hive-site.xml

	<?xml version="1.0"?>
	<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
	<configuration>
		<property>
		  <name>javax.jdo.option.ConnectionURL</name>
		  <value>jdbc:mysql://<hostname>:3306/metastore?createDatabaseIfNotExist=true</value>
		  <description>JDBC connect string for a JDBC metastore</description>
		</property>
		<property>
		  <name>javax.jdo.option.ConnectionDriverName</name>
		  <value>com.mysql.jdbc.Driver</value>
		  <description>Driver class name for a JDBC metastore</description>
		</property>
		<property>
		  <name>javax.jdo.option.ConnectionUserName</name>
		  <value>username</value>
		  <description>username to use against metastore database</description>
		</property>
		<property>
		  <name>javax.jdo.option.ConnectionPassword</name>
		  <value>password</value>
		  <description>password to use against metastore database</description>
		</property>
		<property>
		  <name>hive.cli.print.header</name>
		  <value>true</value>
		  <description>Whether to print the names of the columns in query output</description>
		</property>
		<property>
		  <name>hive.cli.print.current.db</name>
		  <value>true</value>
		  <description>Whether to include the current database in the Hive prompt</description>
		</property>
	</configuration>

4). Add mysql-connector-java-5.1.41-bin.jar under lib

## 5. Config Sqoop1

1). Modify conf/sqoop-env-template.sh

Rename conf/sqoop-env-template.sh - > conf/sqoop-env.sh

	export HADOOP_COMMON_HOME=/opt/cdh-5.3.6/hadoop-2.5.0-cdh5.3.6
	export HADOOP_MAPRED_HOME=/opt/cdh-5.3.6/hadoop-2.5.0-cdh5.3.6
	export HIVE_HOME=/opt/cdh-5.3.6/hive-0.13.1-cdh5.3.6

2). Add mysql-connector-java-5.1.41-bin.jar under lib

3). Try sqoop command

	bin/sqoop list-databases \
	--connect jdbc:mysql://<hostname>:3306 \
	--username username \
	--password password