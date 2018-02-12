---
layout: post
title: Decommission nodes from Hortonworks HDP
category: Hortonworks
tags: Hortonworks Hadoop
description: This page is going to talk about how to add volume on existing node in AWS and decommission nodes from Hortonworks HDP
---

## Why we need to do this?

The reason is because Hortonworks charged you based on node number. So if you have 10 data nodes(each has 3TB), It will be far expenisive than 1 data nodes(holds 10 3TB volume).

## 1.AWS Part
How to add volume to existing node?

1) Login to your AWS Account

2) Click Volumes under "ELASTIC BLOCK STORE"

3) Click Create Volume (Choose the type and size good for you. below just one example)

	Volume Type: General Purpose SSD(GP2)
	Size(GIB): 3000 (3TB for example)
	Availability Zone: eu-west-1a

4) After Volume created, Attached to existing Data node(If you don't have ec2 machine yet, create one).

## 2. Hortonworks Part
5) Initialize the volume

	Assume the new Volume is /dev/xvdi (This will change, check your volumn name)
	Login to the machine which has new volumn attached.
	sudo parted /dev/xvdi
		mklabel gpt
		mkpart primary ext4 0GB 4295GB
		quit
	sudo mkfs.ext4 /dev/xvdi -E lazy_itable_init
	sudo vi /etc/fstab (add below change)
		/dev/xvdi /mnt/data2 ext4 defaults 0 2
	sudo mkdir /mnt/data2
	sudo mount /mnt/data2

6) Update HDFS Config if the Data directory changed.

	Such as /mnt/data1/hadoop/hdfs/data,/mnt/data2/hadoop/hdfs/data

7) Repeat process to add more volumns

8) Decommission data nodes (Make sure there is space on existing data nodes)

9) Rebalance HDFS
