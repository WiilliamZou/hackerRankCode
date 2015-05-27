---
layout: post
title:  Eclipse 配置 google test c++ 单元测试环境 
categories: [环境配置]
tags: [eclipse, c plus plus, unit test]
fullview: false

---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
### 什么是单元测试？

根据[wiki](http://zh.wikipedia.org/wiki/单元测试)的解释，

>单元测试（又称为模块测试, Unit Testing）是针对程序模块（软件设计的最小单位）来进行正确性检验的测试工作。程序单元是应用的最小可测试部件。在过程化编程中，一个单元就是单个程序、函数、过程等；对于面向对象编程，最小单元就是方法，包括基类（超类）、抽象类、或者派生类（子类）中的方法。

### 配置eclipse C++ 单元测试

Cpp unit test 似乎没有 junit 成熟，配置起来也麻烦一些。 

按照[这片帖子](http://linmingren.me/blog/2013/07/eclipse中使用goolge-test来写c单元测试/)，花了一晚上的时间，终于配置好了。  


对我来说，cpp unit test 的最大价值是不需要编写 main 函数也能完成代码测试。  

截个图吧。 

![](http://i.imgur.com/E5zldVY.png)


明天计划学习这个帖子：[介绍全新单元测试框架组合： googletest 与 googlemock](http://www.ibm.com/developerworks/cn/linux/l-cn-cppunittest/index.html)。
