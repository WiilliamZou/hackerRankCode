---
layout: post
title:  hackerRank 题目 - Roy and alpha-beta trees
categories: [hackerrank]
tags: [algorithm, binary search tree, dynamic programming]
fullview: true
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

原题在这里：[Roy and alpha-beta trees](https://www.hackerrank.com/challenges/roy-and-alpha-beta-trees)。

## Problem Statement 

Roy对[二叉搜索树(BST)](https://en.wikipedia.org/wiki/Binary_search_tree)产生了兴趣。 他有兴趣知道有多少种方法可以把一个包含有N个整数的数组重排为一棵二叉搜索树。 于是，他尝试了几种组合，并且记录了在奇数层和在偶数层的数字。 给你两个值 alpha 和 beta. 你能计算从一个包含有N个整数的数组可以转变的所有可能的二叉搜索树的兴趣度总和么? 二叉搜索树的*兴趣度*如下定义:  
	
	(偶数层的数字总和 * alpha) - (奇数层的数字总和 * beta)


###注意：

* 根节点的元素在第0层 ( E偶数 )
* 比根节点元素值小或者和根节点元素值相等的元素在左子树, 严格大于根节点元素的值在右子树。
* 如果答案不小于\\(10^9 + 9\\), 输出 answer % \\(10^9 + 9\\).

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

###输入样例   

	4
	1
	1 1
	1
	2
	1 1
	1 2
	3
	1 1
	1 2 3
	5
	1 1
	1 2 3 4 5
	
	
###输出样例  
	
	1
	0
	6
	54
	
###解释

一共有4组测试数据。  

* 对于第一组测试数据, 用数字’1’只能构造一棵二叉搜索树，即1作为根节点。因此兴趣度之和为1。
* 对于第二组测试数据, 我们有两棵二叉搜索树, 第一棵树和第二棵树的兴趣度分别为 1 * 1 - 2 * 1 = -1 和 2 * 1 - 1 * 1 = 1, 它们的和为0，因此答案是0。

	
		1                  2 
		\               /
      		2            1
  
* 对于第二组测试数据, 我们有4棵二叉搜索树, 从左到右的兴趣度分别为 2, -2, 4, 2, 总和为6，因此答案是6.

		1            2                 3          3  
	 \          / \               /          /
 	  2        1   3             1          2       
  		\                          \        /
   		 3                          2      1
* 类似地，我们可以计算出第4组测试数据的答案为54。


好了，题目抄完了，该怎么做呢？

实际上是一个典型的dynamic programming 的题目。  这里有一个明显的递归。这个应该是解体的线索：

{% highlight python %}
ans[i][j] = 0
for each_item in [i..j]:
	ans[i][j] += ans[i][j] = ans[0][i - 1] * (number of right subtrees) 
	ans[i][j] += ans[j + 1][n - 1] * (number of left subtrees) 
	ans[i][j] += (contribution from root)
{% endhighlight %}



