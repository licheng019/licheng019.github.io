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


4.) Server Push

HTTP1.x is doing for asking a lot of resources does impact load time, even sharing the same TCP connection

How to avoid additional request?

One solution is using inline resources, it is not the best solution. It prevents potential caching of resources, as well as making reuse a bit harder.

Http2 is using Server Push

	Ex: The server knows every time when the client ask for index.html, it will require style.css and script.js as well. So when the client send request for index.html. The server will push style.css and script.js to the client

How this really work?

	When client send a request such as index.html. The server sends a PUSH_PROMISE Frame back.This frame sent on the same stream as the original request, in this case the one that carries index.html. The frame carries a stream Id, the stream id is originated from the server, have even number

Requirement for this Push

	1. Must be safe, can't be impact on the resource itself
	2. Cachable
	3. No include a request body
	4. SETTINGS_ENABLE_PUSH (If this sets to 0, then stop pushing)

## Security

1.) Is SSL or TLS the same thing?

	SSL is Secure Sockets Layer
	TLS is Transport Layer Security
	TLS is the successor to SSL 3
	TLS has overhead

TLS provides three things -- encryption, which allows communication to be transmitted securely without eavesdropping, and it doesn't so by using symmetrical encryption and an initial asymmetric encryption

authentication: which means I can now identify myself, knowing who is talking to who
integrity: It validates the data against this being tampered with or modified during the connection


2.) Caching head in HTTP

	1. Expires
	2. Etag
	3. Cache-Control

## Ways to improve performance

# 1.) Multiple TCP Connections

Open more request trying to improve load time and kind of parallelize these requests
The browsers usually restrict the number of simultaneous to 6. So domain sharding will work for this.

Negative part: A socket requires resources, TCP Overhead and DNS Lookups for each server

### 2.) Concatenation

Negative: Complexity to build process, Impacts caching, Impacts rendering.

Http2 gives proper multiplexing and multiple stream support.

3.) Spriting 

Replace all images to one image

Negative: Make development harder. Impacts caching. Impacts rendering

4.) Minifying

Make files smaller, Such as removing unnecessary white space from js, css
Negative: Complexity to build process.

This solution still works for HTTP2.

5.) Inline resources

Negative: Impacts developement, Impacts caching

HTTP2 drop Inline, try to use server push instead.

6.) Working with CDN

This solution still works for HTTP2.