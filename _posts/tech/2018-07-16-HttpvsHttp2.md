---
layout: post
title: Http vs Http2
category: Web
tags: Web
description: Learning http2 recently and want to leave some notes for myself to revisit. 
---

## What's wrong with HTTP?

I think this is the question that I ask myself, What's wrong with HTTP? Why I need to learn and check Http2? What is the better thing that Http2 provides for us. The answer for the first question is Latency.

Everything runs on Transport. Which for http is TCP. As we know, the TCP is using three handshake connection. Looks like below

	Machine1 --> SYNC      Machine2
	Machine1 <-- ack, sync Machine2
	Machine1 --> ACK       Machine2
	Machine1 --> Request   Machine2
	Machine1 <-- Response  Machine2

You can see there a lot of time wasted establishing the connection, and not mention about the three handshake closed.

Http 1.0 --> A request would open a new TCP connection per request/response.

Http 1.1 introduce one feature which is add one "Connection: keep-alive" in the header, which use a single connection to make multiple request/response

As you can see. The change of Http1.1 resolve the three handshake issue. But there are some other issues as well for TCP. That is the price we pay for get reliable protocol compares to UDP. Don't worry about package lost or out of order.

Disadvantage(prices we are paying): not only increase the overhead. but introduce another issue which is known as head-of-line blocking.

Flow Control which deals with congestion.

TCP use a TCP Slow start.

So Actually Http2 is more about solving the issues with TCP than it is about fixing problems with HTTP 1.x.

## The Comparation of Http1.X and Http2

	HTTP 1.x                             ||     HTTP 2
	Text Protocol                        ||     Binary protocol
	Verb and Resource Semantics          ||     Verb and Resource Semantics
	Headers and Body                     ||     Headers and Body
	header Repeated                      ||     Header optimised
	SSL optional                         ||     SSL optional but mostly used
	Single Connection Single Request     ||     Single Connection Multiple Requests

## Why HTTP2 is better

1.) Transport by using Binary.

HTTP2 , to begin with, the exchange is no longer based on plain text but on binary, the binary message contains much the same as the http1.1 protocol does, which is verb, resource and any headers.

2.) Make Header more efficient

Because most of the response header are same except ETag,Expires and size etc. So it uses HPACK to do the compress. This specification defines HPACK, a compression format for efficiently representing HTTP header fields, to be used in HTTP/2
use hpack for header compression. There are tow types of tables introduced in here.

static and dynamic tables

	（The dynamic table is initially empty. Entries are added as each header block is decompressed.The dynamic table can contain duplicate entries (i.e., entries with the same name and same value). Therefore, duplicate entries MUST NOT be treated as an error by a decoder.）The size is setting in the SETTING Frame

	The Static table has a list of frequently known and used headers such as method,user agent, referrer

3.) How stream works? 

Http2 only has one single connection per host

Multiplexing -> is a technique in which multiple signals are combined into a single signal.

The weight in stream works for priority.

TO BE CONTINUED
