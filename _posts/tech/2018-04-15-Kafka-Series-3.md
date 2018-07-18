---
layout: post
title: Kafka Series 3 -- Kafka Manager(Monitoring)
category: Kafka
tags: Kafka
description: This series going to talk about Kafka and record what I learned about Kafka.
---

> Reference:[https://github.com/yahoo/kafka-manager]

## What is Kafka Manager?

Kafka Manager is A tool for managing Apache Kafka.

It supports the following :

#### Manage multiple clusters
#### Easy inspection of cluster state (topics, consumers, offsets, brokers, replica distribution, partition distribution)
#### Run preferred replica election
#### Generate partition assignments with option to select brokers to use
#### Run reassignment of partition (based on generated assignments)
#### Create a topic with optional topic configs (0.8.1.1 has different configs than 0.8.2+)
#### Delete topic (only supported on 0.8.2+ and remember set delete.topic.enable=true in broker config)
#### Topic list now indicates topics marked for deletion (only supported on 0.8.2+)
#### Batch generate partition assignments for multiple topics with option to select brokers to use
#### Batch run reassignment of partition for multiple topics
#### Add partitions to existing topic
#### Update config for existing topic
#### Optionally enable JMX polling for broker level and topic level metrics.
#### Optionally filter out consumers that do not have ids/ owners/ & offsets/ directories in zookeeper.

## How to install

I am using the machine which installed Red Hat, require java 1.8 for kafka manager

	sudo yum update
	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm"
	sudo rpm -ivh jdk-8u161-linux-x64.rpm 
	java -version
	(download kafka-manager.zip)
	unzip kafka-manager-master.zip
	cd kafka-manager-master/conf
	vi application.conf (check https://github.com/yahoo/kafka-manager for Configuration)
	./sbt clean dist
	cd kafka-manager-master/target/universal/kafka-manager-1.3.3.17/bin
    ./kafka-manager &

## Usage

#### 1). Export JMX port from Kafka to get Metrics information.

	Description	Mbean name	Normal value
	Message in rate	kafka.server:type=BrokerTopicMetrics,name=MessagesInPerSec	
	Byte in rate	kafka.server:type=BrokerTopicMetrics,name=BytesInPerSec	
	Request rate	kafka.network:type=RequestMetrics,name=RequestsPerSec,request={Produce|FetchConsumer|FetchFollower}	 
	Byte out rate	kafka.server:type=BrokerTopicMetrics,name=BytesOutPerSec	
	Log flush rate and time	kafka.log:type=LogFlushStats,name=LogFlushRateAndTimeMs	
	# of under replicated partitions (|ISR| < |all replicas|)	kafka.server:type=ReplicaManager,name=UnderReplicatedPartitions	0
	Is controller active on broker	kafka.controller:type=KafkaController,name=ActiveControllerCount	only one broker in the cluster should have 1
	Leader election rate	kafka.controller:type=ControllerStats,name=LeaderElectionRateAndTimeMs	non-zero when there are broker failures
	Unclean leader election rate	kafka.controller:type=ControllerStats,name=UncleanLeaderElectionsPerSec	0
	Partition counts	kafka.server:type=ReplicaManager,name=PartitionCount	mostly even across brokers
	Leader replica counts	kafka.server:type=ReplicaManager,name=LeaderCount	mostly even across brokers
	ISR shrink rate	kafka.server:type=ReplicaManager,name=IsrShrinksPerSec	If a broker goes down, ISR for some of the partitions will shrink. When that broker is up again, ISR will be expanded once the replicas are fully caught up. Other than that, the expected value for both ISR shrink rate and expansion rate is 0.
	ISR expansion rate	kafka.server:type=ReplicaManager,name=IsrExpandsPerSec	See above
	Max lag in messages btw follower and leader replicas	kafka.server:type=ReplicaFetcherManager,name=MaxLag,clientId=Replica	< replica.lag.max.messages
	Lag in messages per follower replica	kafka.server:type=FetcherLagMetrics,name=ConsumerLag,clientId=([-.\w]+),topic=([-.\w]+),partition=([0-9]+)	< replica.lag.max.messages
	Requests waiting in the producer purgatory	kafka.server:type=ProducerRequestPurgatory,name=PurgatorySize	non-zero if ack=-1 is used
	Requests waiting in the fetch purgatory	kafka.server:type=FetchRequestPurgatory,name=PurgatorySize	size depends on fetch.wait.max.ms in the consumer
	Request total time	kafka.network:type=RequestMetrics,name=TotalTimeMs,request={Produce|FetchConsumer|FetchFollower}	broken into queue, local, remote and response send time
	Time the request waiting in the request queue	kafka.network:type=RequestMetrics,name=QueueTimeMs,request={Produce|FetchConsumer|FetchFollower}	
	Time the request being processed at the leader	kafka.network:type=RequestMetrics,name=LocalTimeMs,request={Produce|FetchConsumer|FetchFollower}	
	Time the request waits for the follower	kafka.network:type=RequestMetrics,name=RemoteTimeMs,request={Produce|FetchConsumer|FetchFollower}	non-zero for produce requests when ack=-1
	Time to send the response	kafka.network:type=RequestMetrics,name=ResponseSendTimeMs,request={Produce|FetchConsumer|FetchFollower}	
	Number of messages the consumer lags behind the producer by	kafka.consumer:type=ConsumerFetcherManager,name=MaxLag,clientId=([-.\w]+)	
	The average fraction of time the network processors are idle	kafka.network:type=SocketServer,name=NetworkProcessorAvgIdlePercent	between 0 and 1, ideally > 0.3
	The average fraction of time the request handler threads are idle	kafka.server:type=KafkaRequestHandlerPool,name=RequestHandlerAvgIdlePercent	between 0 and 1, ideally > 0.3