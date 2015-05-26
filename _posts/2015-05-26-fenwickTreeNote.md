---
layout: post
title:  Binary Index Tree (fenwick tree) 学习笔记
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


下面是关于 BIT 的基本的思想：

>Basic idea   >Each integer can be represented as sum of powers of two. In the same way, cumulative frequency can be represented as sum of sets of subfrequencies. In our case, each set contains some successive number of non-overlapping frequencies.
>


文章中描述的基本概念：

>Notation  >BIT – Binary Indexed Tree  >MaxVal – maximum value which will have non-zero frequency  >f[i] – frequency of value with index i, i = 1 .. MaxVal  >c[i] – cumulative frequency for index i (f[1] + f[2] + ... + f[i])  >tree[i] – sum of frequencies stored in BIT with index i (latter will be described what index means);sometimes we will write tree frequency instead sum of frequencies stored in BIT
>

tree[i] 和 f[i] 的对应关系：

>idx is some index of BIT. r is a position in idx of the last digit 1 (from left to right) in binary notation. tree[idx] is sum of frequencies from index \\(idx – 2^r + 1\\) to index idx (look at the Table 1.1 for clarification). We also write that idx is responsible for indexes from \\(idx - 2^r + 1\\) to idx.


看一个例子应该清楚许多：



### Table 1.1 BST 和对应的数组  

 i | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16  
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---  
f[i] | 1 | 0 | 2 | 1 | 1 | 3 | 0 | 4 | 2 | 5 | 2 | 2 | 3 | 1 | 0 | 2   
c[i] | 1 | 1 | 3 | 4 | 5 | 8 | 8 | 12 | 14 | 19 | 21 | 23 | 26 | 27 | 27 | 29  
tree[i] | 1 | 1 | 2 | 4 | 1 | 4 | 0 | 12 | 2 | 7 | 2 | 11 | 3 | 4 | 0 | 29    

比如说， tree[16] 就是 f[1－16] 的和，而 tree[10] 是 f[9 － 10] 的和。  

### Imagee 1.1 -- tree of responsibility for indexes (bar shows range of frequencies accumulated in top element)

![tree of responsibility for indexes (bar shows range of frequencies accumulated in top element)
](http://community.topcoder.com/i/education/binaryIndexedTrees/BITimg.gif)

>Suppose we are looking for cumulative frequency of index 13 (for the first 13 elements). In binary notation, 13 is equal to 1101. Accordingly, we will calculate c[1101] = tree[1101] + tree[1100] + tree[1000].


query 函数可以这样实现：

{% highlight java %]
int read(int idx){
    int sum = 0;
    while (idx > 0){
        sum += tree[idx];
        idx -= (idx & -idx);
    }
    return sum;
}  
{% endhighlight %}