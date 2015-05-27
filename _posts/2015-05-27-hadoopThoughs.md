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

### java profiler inside the current JVM

### java profiler inside 