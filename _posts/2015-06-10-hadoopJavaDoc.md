---
layout: post
title:  hadoop JavaDoc 
categories: [Hadoop]
tags:  [环境配置, Java]
fullview: false

---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

网上hadoop 的API link 不靠谱，自己用eclipse 生成了一个版本：



虽然hadoop 的maven 支持直接生成 javadoc：

	mvn javadoc: javadoc 

但是生成的 doc 貌似很不靠谱，对我来说一个重要的类：**org.apache.hadoop.ipc.Client**.甚至连**org.apache.hadoop.ipc** 这个包都不显示！！！！什么鬼！！！

只好自己动手，用eclipse 生成了一个javadoc，放在github上面。

[Hadoop Common JavaDoc](http://wiilliamzou.github.io/hadoopCommonDoc/)

[Hadoop HDFS JavaDoc](http://wiilliamzou.github.io/hadoopHDFSJavadoc/)

github 静态建站真是好用。