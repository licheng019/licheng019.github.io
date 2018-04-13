---
layout: post
title: Kafka Series 2 -- Kafka Operations
category: Kafka
tags: Kafka
description: This series going to talk about Kafka and record what I learned about Kafka.
---

> Reference:[https://svn.apache.org/repos/asf/kafka/site/082/ops.html]

## Topic Operation

1). Create Topic

	$ bin/kafka-topics.sh --create --topic topicExample --zookeeper zkserver1:2181/kafka --config max.message.bytes=12800000 --config flush.messages=1 --partitions 5 --replication-factor 3

	$ bin/kafka-topics.sh --create --topic topicExample --zookeeper zkserver1:2181/kafka --replication-factor 1 --partitions 1 ## minimum variable to create topic

	$ bin/kafka-topics.sh --create --topic topicExample --zookeeper zkserver1:2181/kafka --replication-factor 1 --partitions 1 --config segment.bytes=10240
	The number of partitions can't above kafka broker number

2). Check all Topic

	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --list

3). Check Topic detail inforamiton

	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --describe 

	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --describe --topic topicExample

4). Alter Topic information
	
	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --alter --topic topicExample --config max.message.bytes=128000

	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --alter --topic topicExample --delete-config max.message.bytes

	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --alter --topic topicExample --partitions 10 

	$ bin/kafka-topics.sh --zookeeper zkserver:2181/kafka --alter --topic topicExample --partitions 3                     Only allow increase partition number, can't be reduce

5). Delete Topic

	$bin/kafka-topics.sh --delete --topic topicExample  --zookeeper zkserver:2181/kafka

	## Note: This will have no impact if delete.topic.enable is not set to true.                                             ## Default, It only shows delete, but it not actually delete those topics. There are two ways to actually delete
	Method 1: run delete command, and then manually delete topic inforamtion and related information on zk
	Method 2: Config "server.properties" file，set up "delete.topic.enable=true".

## Graceful shutdown

The Kafka cluster will automatically detect any broker shutdown or failure and elect new leaders for the partitions on that machine. This will occur whether a server fails or it is brought down intentionally for maintenance or configuration changes. For the later cases Kafka supports a more graceful mechanism for stoping a server then just killing it. When a server is stopped gracefully it has two optimizations it will take advantage of:

1) It will sync all its logs to disk to avoid needing to do any log recovery when it restarts (i.e. validating the checksum for all messages in the tail of the log). Log recovery takes time so this speeds up intentional restarts.

2) It will migrate any partitions the server is the leader for to other replicas prior to shutting down. This will make the leadership transfer faster and minimize the time each partition is unavailable to a few milliseconds.


## Balancing leadership

Whenever a broker stops or crashes leadership for that broker's partitions transfers to other replicas. This means that by default when the broker is restarted it will only be a follower for all its partitions, meaning it will not be used for client reads and writes.To avoid this imbalance, Kafka has a notion of preferred replicas.

1) Manually: bin/kafka-preferred-replica-election.sh -zookeeper zkserver1:2181,zkserver2:2181

2) Automatically: Set up auto.leader.rebalance.enable=true
