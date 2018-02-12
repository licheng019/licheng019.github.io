---
layout: post
title: Kafka Series 1
category: Kafka
tags: Kafka
description: This series going to talk about Kafka and record what I learned about Kafka.
---

> Reference:[https://kafka.apache.org/documentation]

## What is Kafka?

1) It lets you publish and subscribe to steams or records. Similar to message queue or messaging system.

2) It lets you store streams of records in a fault-tolerant way.

3) It lets you process streams of records as they occur.

## Kafka Terms

1) Message：Messages consist of a variable-length header, a variable length opaque key byte array and a variable length opaque value byte array. There are four parts of message(offset, key, value, timestamp).

2) Broker：Normally, One server has one broker. but it can hold more brokers(don't recommend).

3) Topic：Kafka topics are divided into a number of partitions. Partitions allow you to parallelize a topic by splitting the data in a particular topic across multiple brokers. Produce write messages to Topic，Consumer read messages from topic.

4) Partition：One topic includes multiple partition, when producer send messages, it will send to different partitions based on keys. There are two characters for one partition

      	order: based on timeline of kafka message in有序：按照进入kafka的时间排序
     	immute: The data can't be change

5) Producer：data producer

6) Consumer：data consumer

7) ConsumerGroup：Multiple Consumer consume data in the same time. If the number of consumers equal to the number of partition in one topic. then each consumer consume one partition. if more than partition, then there are some consumers are idle.

## Why kafka is fast?

1) Message Set: when producer send data, combine multiple message in one messgae set, reduce IO times.

2) Binary transport

3) read disk in order: according offset to read disk and read multiple messages once.

4) zero-copy

	Log->Read Buffer -> NIC Buffer （zero-copy) directly DMA Copy
	File->Read Buffer ->Applicaiton Buffer -> Socket Buffer ->NIC Buffer(Traditional)

5) compress on producer and consumer end. Transfer compressed message.
