---
layout: post
title:  fenwick 学习笔记
categories: [topcoder]
tags: [algorithm, fenwick tree, 学习笔记]
fullview: false
shortinfo: 关于 topcoder 的一片blog 的学习笔记
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

这个帖子是关于Binary Indexed Trees 的学习笔记。

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

当然在题目中 fenwick tree 是有技巧的，比如这道题 [library query](/2015/05/24/libaryQuery.html)