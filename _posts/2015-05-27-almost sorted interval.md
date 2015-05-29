---
layout: post
title:  almost sorted interval 
categories: [hackerrank]
tags: [sorting, binary index tree, expert ]
fullview: false
shortinfo: almost sorted interval 
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>


原题在这里：[almost sorted interval](https://www.hackerrank.com/challenges/almost-sorted-interval)，

{% highlight c++ linenos %}
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
using namespace std;

#define FOR(it, c) for(__typeof((c).begin()) it = (c).begin(); it != (c).end(); it++)
#define SZ(c) ((int)(c).size())
typedef long long LL;

const int N = 1000005;
int stack[N], top=0;
int left[N], right[N];
int a[N], n;
vector<int> r[N];
int bit[N];

void add(int x, int v) {
  while(x<=n) { bit[x] += v; x += (x&-x); }
}
int ask(int x) {
  int ret=0;
  while(x) { ret += bit[x]; x -= (x&-x); }
  return ret;
}

int main(void) {
  scanf("%d", &n);
  for(int i=1;i<=n;i++) scanf("%d", &a[i]);
  for(int i=1;i<=n;i++) {
    while(top>0 && a[i] > a[stack[top-1]]) --top;
    left[i] = top? stack[top-1] : 0;
    stack[top++] = i;
  }
  top = 0;
  for(int i=n;i>=1;i--) {
    while(top>0 && a[i] < a[stack[top-1]]) --top;
    right[i] = top? stack[top-1] : n+1;
    stack[top++] = i;
  }
  LL ans = 0;
  for(int L=n;L>=1;L--) {
    FOR(it, r[L]) add(*it, -1);
    add(L, 1);
    r[left[L]].push_back(L);
    ans += ask(right[L]-1);
  }
  printf("%lld\n", ans);
  return 0;
}
{% endhighlight %}

代码相当的短小精悍。

## 数据结构  

>理解一个程序，首先要理解这个程序的核心数据结构。

关键的，比较tricky的数据结构主要是

{% highlight c++ %}

vector<int> r[N];
int bit[N];

{% endhighlight %}

对应程序的 line 16, 17. 我们一个一个来看：

###  vector<int> r[N]

实际上，这个相当于反函数。

> 定义 \\(f(x) = left(x)\\)

那么： 

$$ r[x] = \{ x| x = f^{-1}(x) \} $$


算法基本上分两步：  
1. 计算**left**数组和**right**数组。  
2. 根据**left**,**right**数组算出ans.

## 计算*left*数组和*right*数组  


## 根据**left**,**right**数组算出ans
line 61-64对应*left[R]<L*; line 65对应*right[L]>R*。

