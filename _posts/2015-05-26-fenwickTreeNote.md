---
layout: post
title:  fenwick 学习笔记
categories: [topcoder]
tags: [algorithm, fenwick tree, 学习笔记]
fullview: false
shortinfo: 关于 topcoder 的一片blog 的学习笔记
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

这个帖子是关于Binary Indexed Trees 的学习笔记。Binary Index Tree 的别名是 fenwick tree. 原文章来自于 [top coder](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-trees/).

根据 [wiki](http://en.wikipedia.org/wiki/Fenwick_tree) 的解释，fenwick tree 是对数组 *A[0..n]* 操作的优化，核心操作如下：

* *update(k, val)*: 将 *A[k]* 的值改成val。
* *query(k)*: 返回 *A[0..k]* 的数值的总和。

当然，直接在数组 *A[0..n]* 可以非常容易的实现这些操作，时间复杂度是：

* *update(k, val) -- 数组版本：* 时间复杂度：\\(O(1)\\)
* *query(k) -- 数组版本：* 时间复杂度：\\(O(n)\\)

如果用 *fenwick tree* 的实现：

* *update(k, val) -- fenwick tree 版本：* 时间复杂度：\\(O(log(n))\\)
* *query(k) -- fenwick tree 版本* 时间复杂度： \\(O(log(n))\\)

在要求时间复杂度的题目中， fenwick tree 还是有相当的用武之地的。 

当然在题目中 fenwick tree 是有技巧的，比如这道题 [library query](/hackerRankCode/hackerrank/2015/05/24/libaryQuery.html)  

按照 [top coder 文章](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-trees/) 的介绍：

>Fenwick Tree is often used for storing frequencies and manipulating cumulative frequency tables.
	

一个例子程序如下： 

>例题1-1   
>
>设想有n个箱子，相关的操作如下：      
> 1. 在第i个箱子里放入水晶球   
> 2. 计算第l个箱子到第r个箱子中水晶球的个数总和  
> 
>  

如果我们用数组实现，假设有m次操作，naive implementation 的时间复杂度是 \\(O(m \times n)\\)，但是如果采用 binary index tree, 时间复杂度是 \\(O(m \times log(n))\\)。还是不小的提高。 

>例题1-1 实现  
>假设箱子的个数是n, 操作的个数是m。    
>naive 实现时间复杂度：\\(O(m * n)\\)   
>binary tree index 实现时间复杂度： \\(O(m \times log(n))\\)   
>
