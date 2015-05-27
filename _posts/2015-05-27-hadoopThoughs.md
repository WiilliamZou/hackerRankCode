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