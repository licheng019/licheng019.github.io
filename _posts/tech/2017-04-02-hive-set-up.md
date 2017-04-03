---
layout: post
title: Hive Set up
category: Hive
tags: Hive
description: Hive Learning(Basic)
---

- Set up Hive

## 1. Install and Set up Hive

1)Download Hive from <a href="https://hive.apache.org/downloads.html">Hive Apache Org Website</a>.

2)Config hive-env.sh

	Set Up Hadoop_home
	HADOOP_HOME =...
	Set up HIVE_CONF_DIR
	HIVE_CONF_DIR=/.../hive/conf

3)Config HDFS Folder
    fs -mkdir /tmp   -> bin/hdfs dfs -mkdir /tmp
    fs -mkdir /user/hive/warehouse -> bin/hdfs dfs -mkdir -p /user/hive/warehouse
    fs -chmod g+w /tmp -> bin/hdfs dfs -chmod g+x /tmp
    fs -chmod g+w /usr/hive/warehouse ->  bin/hdfs dfs -chmod g+x /usr/hive/warehouse

## 2.Config MySQL Metastore

1)Install MySQL

2)Install the MySQL Java Connector

3) Create hive-site.xml ( If not already present) in $HIVE_HOME/conf folder with the configuration below

	<configuration>
	   <property>
	      <name>javax.jdo.option.ConnectionURL</name>
	      <value>jdbc:mysql://localhost/metastore?createDatabaseIfNotExist=true</value>
	      <description>metadata is stored in a MySQL server</description>
	   </property>
	   <property>
	      <name>javax.jdo.option.ConnectionDriverName</name>
	      <value>com.mysql.jdbc.Driver</value>
	      <description>MySQL JDBC driver class</description>
	   </property>
	   <property>
	      <name>javax.jdo.option.ConnectionUserName</name>
	      <value>hiveuser</value>
	      <description>user name for connecting to mysql server</description>
	   </property>
	   <property>
	      <name>javax.jdo.option.ConnectionPassword</name>
	      <value>hivepassword</value>
	      <description>password for connecting to mysql server</description>
	   </property>
	</configuration>

4)Move mysql-connector under hive/lib

5)Open hive under /bin/hive
There is one database metastore in mysql database.
