---
layout: post
title:  hadoop profiler 想法
categories: [hadoop]
tags: [hadoop, profiler, thoughts ]
fullview: false
shortinfo: 配置eclipse c++ unit test. 
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

## Distributed request profiler

An idea to implement request-based profiler for distributed system is to annotated top-level request functions and decorated communication data. JVM Tool interface and agent API is useful for implemenation. 

### Output format 

One possible format of profiler output would be a set of request record entry. Request record may like: 

request ID | start time | end time | IP | ID | source request ID   
--- | --- | --- | --- | --- | --------|
writeBlock | xxx-xxx | xxx--xxx | xxx--xxxx | xxxxx | xxxxxx |
 
Source request ID is the ID of source request, which is a request of other node and triggers the current request through network communication.  


It can be converted to the output format similar to lprof paper. [^lprof]

![](http://i.imgur.com/5knHM2R.png)



### JVM Tool Interface and Agent 

The JVMTM Tool Interface (JVM TI) is a programming interface used by development and monitoring tools. It provides both a way to inspect the state and to control the execution of applications running in the JavaTM virtual machine (VM). [^jvmtool] 

JVMTI is helpful not only to record executation state but also to re-define the content of class that is loaded at run-time. 

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

User should indicate the top-level method (the start point of a request), for example, developer should indicate function **write_block** (line 14) as the top-level function for *write request*, and function **readBlock** (line 22) as the top-level function for *read request*. User can also optionally indicate the request IDs, in **write_block**, the **ExtendedBlock block**'s fields (e.g., block ID and pool ID) can be marked as the request IDs.

### request recording for current JVM

Java agent should record information inside the current JVM of a given request with ease. For each execution of request top-level function (e.g. **write_block**), java agent will generate an *ID*, *record request identifiers* (if marked by the user), *start time*, and *end time*. The end time is the time when the function and all threads spawned by the function terminate. 

### request recording between multiple JVMs

In distributed systems, requests may traverse different nodes.  Recording information among different nodes  (JVMS) would be more challenging.  If request A triggers request B, both request A and B should be recorded, and request A should be marked as the **source request** of request B. 

One possible solution is to use decorated data structures. Let's assume nodes in the distributes system use IO (such as netowrk sockets) to coomunication. More specifically, the object serialization/deserialization is adopted as the basic communication mechanism. When the node attempts to send data, java agent intercepts and checks this communication is caused by a request which is currently being traced. If it is, the decoration information is added into the orginal object serialization data. The decoration includes the ID, the request identifiers, start time and source IP of source request. 

When another node retrives the data, the java agent of that node intercepts and checks if it contains decoration. If it is,  it will mark the **source request field** of requests which accesses data to be the request indicated in the decoration.  


[^jvmtool]: [http://docs.oracle.com/javase/7/docs/platform/jvmti/jvmti.html](http://docs.oracle.com/javase/7/docs/platform/jvmti/jvmti.html)
[^lprof]: [http://www.eecg.toronto.edu/~yuan/papers/lprof_osdi14.pdf](http://www.eecg.toronto.edu/~yuan/papers/lprof_osdi14.pdf)