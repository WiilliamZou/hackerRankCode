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


## Binary Indexed Tree

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

### Image 1.1 -- tree of responsibility for indexes (bar shows range of frequencies accumulated in top element)

![tree of responsibility for indexes (bar shows range of frequencies accumulated in top element)
](http://community.topcoder.com/i/education/binaryIndexedTrees/BITimg.gif)

>Suppose we are looking for cumulative frequency of index 13 (for the first 13 elements). In binary notation, 13 is equal to 1101. Accordingly, we will calculate c[1101] = tree[1101] + tree[1100] + tree[1000].


### query 函数可以这样实现：

{% highlight java %}
int query(int idx){
    int sum = 0;
    while (idx > 0){
        sum += tree[idx];
        idx -= (idx & -idx);
    }
    return sum;
}  
{% endhighlight %}

### update 的实现：

{% highlight java %}
void update(int idx ,int val){    while (idx <= MaxVal){        tree[idx] += val;        idx += (idx & -idx);    }}
{% endhighlight %}


具体实现一般包含两个数组，*tree* 和原始的 *array*。

### scale 的实现

{% highlight java linenos %}
void scale (int c) {
	for (int i = 1; i <= MaxVal; i++)
		tree[i] = tree[i] / c;
}
{% endhighlight %}

###  Find index with given cumulative frequency

如果元素是非负的，那么 cumulative frequency 是一个 ascending sorted array. 可以考虑用 binary search 强化查找。确定 binary index 的每一个bit。

其实也不能叫 binary search. 因为没有 binary 的部分，实际上是确定idx 的每一个bit. 

{% highlight java %}
// if in tree exists more than one index with a same
// cumulative frequency, this procedure will return 
// some of them (we do not know which one)

// bitMask - initialy, it is the greatest bit of MaxVal
// bitMask store interval which should be searched
int find(int cumFre){
    int idx = 0; // this var is result of function
    
    while ((bitMask != 0) && (idx < MaxVal)){ // nobody likes overflow :)
        int tIdx = idx + bitMask; // we make midpoint of interval
        if (cumFre == tree[tIdx]) // if it is equal, we just return idx
            return tIdx;
        else if (cumFre > tree[tIdx]){ 
                // if tree frequency "can fit" into cumFre,
                // then include it
            idx = tIdx; // update index 
            cumFre -= tree[tIdx]; // set frequency for next loop 
        }
        bitMask >>= 1; // half current interval
    }
    if (cumFre != 0) // maybe given cumulative frequency doesn't exist
        return -1;
    else
        return idx;
}



// if in tree exists more than one index with a same
// cumulative frequency, this procedure will return 
// the greatest one
int findG(int cumFre){
    int idx = 0;
    
    while ((bitMask != 0) && (idx < MaxVal)){
        int tIdx = idx + bitMask;
        if (cumFre >= tree[tIdx]){ 
                // if current cumulative frequency is equal to cumFre, 
                // we are still looking for higher index (if exists)
            idx = tIdx;
            cumFre -= tree[tIdx];
        }
        bitMask >>= 1;
    }
    if (cumFre != 0)
        return -1;
    else
        return idx;
}
{% endhighlight %} 

### Example for cumulative frequency 21 and function *find*:

 Iteration | description 
--- | --- 
first iteration | tIdx is 16; tree[16] is greater than 21; half bitMask and continue  
second iteration | tIdx is 8; tree[8] is less than 21, so we should include first 8 indexes in result, remember idx because we surely know it is part of result; subtract tree[8] of cumFre (we do not want to look for the same cumulative frequency again – we are looking for another cumulative frequency in the rest/another part of tree); half bitMask and contiue  
Third iteration | tIdx is 12; tree[12] is greater than 9 (there is no way to overlap interval 1-8, in this example, with some further intervals, because only interval 1-16 can overlap); half bitMask and continue   
Forth iteration	| tIdx is 10; tree[10] is less than 9, so we should update values; half bitMask and continue  
Fifth iteration	| tIdx is 11; tree[11] is equal to 2; return index (tIdx)


## 2-D Binary Indexed Tree

这是一个非常 fancy 的数据结构，非常的巧妙和优雅。
一个例题如下：

>BIT can be used as a multi-dimensional data structure. Suppose you have a plane with dots (with non-negative coordinates). You make three queries:   1. setdotat(x,y)   2. remove dot from (x , y)   3. count number of dots in rectangle (0 , 0), (x , y) – where (0 , 0) if down-left corner, (x , y) is up-right corner and sides are parallel to x-axis and y-axis.   

If m is the number of queries, \\(max\_x\\) is maximum x coordinate, and \\(max\_y\\) is maximum y coordinate, then the problem should be solved in \\(O(m \times log (max\_x) \times log (max\_y))\\). In this case, each element of the tree will contain array – (tree[\\(max\_x\\)][\\(max\_y\\)]). Updating indexes of x-coordinate is the same as before. For example, suppose we are setting/removing dot (a , b). We will call _update(a , b , 1)_ / _update(a , b , -1)_. 

###这个非常漂亮的想法

update 的 code: 

{% highlight java %}
void update(int x , int y , int val){
    while (x <= max_x){
        updatey(x , y , val); 
        // this function should update array tree[x] 
        x += (x & -x); 
    }
}

void updatey(int x , int y , int val){
    while (y <= max_y){
        tree[x][y] += val;
        y += (y & -y); 
    }
}


{% endhighlight %}

query 的 code：
{% highlight java %}
int query(int x, int y){    int sum = 0, ;    while (x > 0){    	sum += queryy(x, y);    	x -= (x & -x);    }    return sum;}

int queryy(int y) {
	int sum  = 0;
	while (y > 0) {
		sum += tree[x][y];
		y -= (y & -y);
	}
	return sum;
}
{% endhighlight %}

其实用的是类似于 计算几何中 [sweep line](https://www.topcoder.com/community/data-science/data-science-tutorials/line-sweep-algorithms/) 算法。

粗略的看，这是一个两层的 binary indexed tree. 看张图可能更加清晰：


### Image 1.2 BIT is array of arrays, so this is two-dimensional BIT (size 16 x 8).
Blue fields are fields which we should update when we are updating index (5 , 3).
![](http://community.topcoder.com/i/education/binaryIndexedTrees/bit2d.gif)

