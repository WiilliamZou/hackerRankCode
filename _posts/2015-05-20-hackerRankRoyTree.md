---
layout: post
title:  hackerRank 题目 - Roy and alpha-beta trees
categories: [hackerrank]
tags: [algorithm, binary search tree, dynamic programming]
fullview: true
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

先把题目抄一遍吧：

## Problem Statement 

Roy对[二叉搜索树(BST)](https://en.wikipedia.org/wiki/Binary_search_tree)产生了兴趣。 他有兴趣知道有多少种方法可以把一个包含有N个整数的数组重排为一棵二叉搜索树。 于是，他尝试了几种组合，并且记录了在奇数层和在偶数层的数字。 给你两个值 alpha 和 beta. 你能计算从一个包含有N个整数的数组可以转变的所有可能的二叉搜索树的兴趣度总和么? 二叉搜索树的*兴趣度*如下定义:  
	
	(偶数层的数字总和 * alpha) - (奇数层的数字总和 * beta)


###注意：

* 根节点的元素在第0层 ( E偶数 )
* 比根节点元素值小或者和根节点元素值相等的元素在左子树, 严格大于根节点元素的值在右子树。
* 如果答案不小于\\(10^9 + 9\\), 输出 \\(answer \% (10^9 + 9)\\).

###输入格式

输入文件第一行包含一个整数, T, 表示后面测试数据的组数。 每组测试数据包含3行。 第一行包含 N, 整数的个数。 
第三行包含_N个空格分隔的整数，第ith个整数表示数组里第i个元素 A[i].

###输出格式

输出 T 行，每行表示对应测试数据的答案。

###约束条件

* 1 <= *T* <= 10 
* 1 <= *N* <= 150 
* 1 <= *A[i]* <= \\(10^9\\)
* 1 <= *alpha,beta* <= \\(10^9\\)

