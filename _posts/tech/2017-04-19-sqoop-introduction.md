---
layout: post
title: Sqoop Introduction
category: Sqoop
tags: Sqoop
description: Big Data Learning -- Sqoop
---

- Sqoop Introduction

## 1. What is Sqoop

Sqoop is a tool designed for efficiently transferring bulk data between Apache Hadoop and structured datastores such as relational databases.

Here is the Link of Sqoop Official website. http://sqoop.apache.org/

There are two parts of the Sqoop : Import and Export

## 2. Import
	
RDBMS ->hadoop, hive

1). First Try

	bin/sqoop import \
	--connect jdbc:mysql://<hostname>:3306/<databaseName> \
	--username username \
	--password password \
	--table tableName
	--target-dir /use/root/sqoop/....
	--num-mappers 1 (assign map-reduce number, the default number is 4.

2). Saving the file in different format

	--as-sequencefile (saving the data in Hadoop SequenceFile)
	--as-avrodatafile (saving the data in Avro binary format)
	--as-parquetfile (saving the data in parquet file)

3). Import assign column to hdfs

	bin/sqoop import \
	--connect jdbc:mysql://<hostname>:3306/<databaseName> \
	--username username \
	--password password \
	--table tableName
	--target-dir /user/root/sqoop/imp_my_user_column \
	--num-mappers 1 \
	--columns id,account

It also can use --query 'select id, account from my_user where $CONDITIONS' \ to clean the data.

4). Compress the data

	bin/sqoop import \
	--connect jdbc:mysql://<hostname>:3306/<databaseName> \
	--username username \
	--password password \
	--table tableName
	--target-dir /user/root/sqoop/imp_my_Snappy \
	--delete-target-dir \
	--num-mappers 1 \
	--compress \
	--compression-codec org.apache.hadoop.io.compress.SnappyCodec \
	--fields-terminated-by '\t'

Before run above command, It can use "bin/hadoop checknative" under hadoop to check which compress is supported.

5). Direct mode

--direct (is only supported in mysql and postgresql.) Bottom line: "direct mode" means different things for different databases.

6). hive import

	bin/sqoop import \
	--connect jdbc:mysql://<hostname>:3306/<databaseName> \
	--username username \
	--password password \
	--table tableName \
	--fields-terminated-by '\t' \
	--delete-target-dir \
	--num-mappers 1 \
	--hive-import \
	--hive-database default \
	--hive-table user_hive

## 3. Export

hadoop, hive -> RDBMS

1). Export mysql table 

	bin/sqoop import \
	--connect jdbc:mysql://<hostname>:3306/<databaseName> \
	--username username \
	--password password \
	--table tableName \
	--export-dir /user/root/sqoop/exp/user/ \
	--num-mappers 1
	--input-fields-terminated-by '\t'

## 4. Scripting

Sqoop also supports script for all the command

bin/sqoop --options-file /opt/datas/sqoop-import-hdfs.txt(script name)