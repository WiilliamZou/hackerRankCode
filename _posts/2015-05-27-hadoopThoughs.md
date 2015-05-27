---
layout: post
title:  hadoop profiler 想法
categories: [hadoop]
tags: [hadoop, profiler, thoughts ]
fullview: false
shortinfo: 配置eclipse c++ unit test. 
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

最近准备做一个hadoop request profiler. 基于分布式。

基本的输出格式： 

![](http://i.imgur.com/5knHM2R.png)

###基本想法：

1. 让 developer 标记	top level methods
2. 让 developer 标记 request identifier.

###来自分布式主要挑战

最主要的挑战是多个JVM之间的交互。这是主要的挑战。 
解决的方式可能是sends stats. 

这个要相关的文章。

###java agent 介绍

one possible solution: tracing 

adding a source part in the data structure 

想到一个有意思的想法。

profiler 可以作为一个java agent. 

### example code 

{% highlight java linenos=table %}
class DataXceiver implements Runnable {
  public void run() {
   do { //handle one request per iteration
	switch (readOpCode()) {
	 case WRITE_BLOCK: // a write request
	  writeBlock(proto.getBlock(), ..); 
	  break;
     case READ_BLOCK: // a read request
      readBlock(proto.getBlock(), ..); 
      break;
	 } //proto.getBlock: deserialize the request 
	}while (!socket.isClosed());
  }
  void writeBlock(ExtendedBlock block..) {
  	LOG.info("Receiving block " + block);
  	send.writeBlock(block,..); //send to next DN
  	responder.start();
  }
}

/* PacketResponder handles the ack responses */
class PacketResponder implements Runnable {
  public void run() {
    ack.readField(downstream); //read ack
    LOG.info("Received block" + block);
    replyAck(upstream);
    LOG.info(myString + " terminating");
	}
}
{% endhighlight %}


### Request top-level function and request identifiers

user should indicate the top-level method (the start point of a request), for example, developer should indicate function **write_block** (line 14) as the top-level function for *write request*, and function **readBlock** (line 22) as the top-level function for *read request*. User can also optionally indicate the request IDs, in **write_block**, the **ExtendedBlock block**'s fields (e.g., block ID and pool ID) can be marked as the request IDs.

### request recording for current JVM

Java agent should record information inside the current JVM of a given request with ease. For each execution of request top-level function (e.g. **write_block**), java agent will generate an *ID*, *record request identifiers* (if marked by the user), *start time*, and *end time*. The end time is the time when the function and all threads spawned by the function terminate. 

### request recording between multiple JVMs

In distributed systems, requests may traverse different nodes.  Recording information among different nodes  (JVMS) would be more challenging.  

One possible solution is to use decorated data structures. Let's assume nodes in the distributes system use IO (such as netowrk sockets) to coomunication. More specifically, the object serialization/deserialization is adopted as the basic communication mechanism.  