---
layout: post
title:  library query
categories: [hackerrank]
tags: [algorithm, fenwick tree, advanced, binary index tree]
fullview: false
shortinfo: 一道关于fenwick tree 的题目
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

原题在这里：[Library Query](https://www.hackerrank.com/challenges/library-query)

懒得抄题了。

开始以为这个题目很简单，不就是 query 和 update 两种操作吗，直接做了一个 naive 的实现，代码也是一读就懂：

{% highlight java%}
package libraryQuery;

import java.io.File;
import java.util.Arrays;
import java.util.Scanner;

public class Solution {
	
	
	public static void main(String[] args) {
		Scanner consoleInput = new Scanner(System.in);
		try {
			consoleInput = new Scanner(new File("libraryQuerySampleInput.txt"));
		}
		catch (Exception e){
			
		}
		int numOfTestCase;
		int numOfShelf;
		int numOfOperation;
		numOfTestCase = consoleInput.nextInt();
		for (int i = 0; i < numOfTestCase; i++) {
			numOfShelf = consoleInput.nextInt();
			int[] shelfArray = new int[numOfShelf];
			for(int j = 0; j < numOfShelf; j++) 
				shelfArray[j] = consoleInput.nextInt();
			
			Shelf shelf = new Shelf(shelfArray);
			
			numOfOperation = consoleInput.nextInt();
			for (int j  = 0; j < numOfOperation; j++) {
				int opCode = consoleInput.nextInt();
				if (opCode == 1) {
					// update
					shelf.update(consoleInput.nextInt(), consoleInput.nextInt());
				}
				else {
					// query
					System.out.println(shelf.query(consoleInput.nextInt(), consoleInput.nextInt(), consoleInput.nextInt()));
				}
			}
		}
		consoleInput.close();
	}
	
	
	
}

class Shelf {
	private int[] shelf;
	public Shelf(int[] shelf) {
		this.shelf = shelf;
	}
	public void update (int loc, int newValue) {
		this.shelf[loc - 1] = newValue;
	}
	public int query(int start, int end, int rank) {
		assert(end >= start);
		assert(rank <= end - start + 1);
		int[] range = Arrays.copyOfRange(this.shelf, start - 1, end);
		Arrays.sort(range);
		return range[rank - 1];
	}
}
{% endhighlight%} 

然后运行，实现应该是没有错，但是问题是：超时。

![time out :(](http://i.imgur.com/3aFz6df.png)

两个主要方法的时间复杂度：

* update: \\(O(1)\\)
* query: \\(O(nlog(n))\\)

理想的时间复杂度应该是：

* update: \\(O(log(n))\\)
* query: \\(O(log(n))\\)

应该是需要用 [fenwick tree](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-trees/)，先看看算法材料再来做吧。

似乎应该用 2-D fenwick tree.  但是问题来了，如何应用2-d binary index tree。 

可以这样用：tree[N][maxValue] 这样的2-D array。

操作的定义就非常重要。 

* query: the number of points which is less than (index, value). 

>If we need to find the kth ranked element between indices l and r (inclusive), we use binary search to guess which element (out of 0 to 1000) could be the kth element. Let our current guess be x. We then calculate val(x) = cumulative(r,x) - cumulative(l-1,x) where cumulative(a,b) gives us the number of elements less than or equal to b uptil index a.

看张图吧： 

![](http://i.imgur.com/lGa2rPD.png)

下面贴代码： 

{% highlight c++ %}
/*
Author : Vivek Hamirwasia

A programmer started to cuss,
Because getting to sleep was a fuss.
As he lay there in bed,
Looping ???round in his head,
Was: while(!asleep()) sheep++;

 */
#include<cstdio>
#include<iostream>
#include<vector>
#include<map>
#include<set>
#include<stack>
#include<queue>
#include<algorithm>
#include<cmath>
#include<string>
#include<cstdlib>
#include<climits>
#include<cstring>
using namespace std;

#define CLR(a,x) memset(a,x,sizeof(a))
#define PB push_back
#define INF 1000000000
#define MOD 1000000007
#define MP make_pair
#define tr(container , it) for(typeof(container.begin()) it=container.begin() ; it!=container.end() ; it++)
#define FOR(i,a,b) for(i=a;i<b;i++)
#define REP(i,a) FOR(i,0,a)
#define LLD long long
#define VI vector < int >
#define PII pair < int , int >
#define MAX 1000000000
#define max_x 10000
#define max_y 1000
int tree[10005][1005];
int A[10004];
void update(int x , int y , int val){
    int y1;
    while (x <= max_x){
    y1 = y;
    while (y1 <= max_y){
        tree[x][y1] += val;
        y1 += (y1 & -y1); 
    }
    x += (x & -x); 
    }
}

int read(int x, int y){
    int sum = 0, y1;
    while (x > 0){
    y1 = y;
    while(y1 > 0)
    {
        sum += tree[x][y1];
        y1 -= (y1 & -y1);
    }
    x -= (x & -x);
    }
    return sum;
}

int solve(int l, int r, int k)
{
    int lo = 1;
    int hi = max_y;
    int mid;
    int ans = -1;

    while(lo<=hi)
    {
    mid = (lo+hi)/2;
    int v2 = read(r, mid);
    int v1 = read(l-1, mid);
    if( v2 - v1 >=k )
    {
        ans = mid;
        hi = mid-1;
    }
    else
        lo = mid+1;
    }
    return ans;
}

int main()
{
    int T, n, q;
    scanf("%d",&T);
    memset(tree, 0, sizeof tree);
    while(T--)
    {
    scanf("%d",&n);
    for(int i=1;i<=n;i++)
    {
        scanf("%d",&A[i]);
        update(i, A[i], 1);
    }

    scanf("%d",&q);
    while(q--)
    {
        int b,x,y,k;
        scanf("%d", &b);
        if(b==0)
        {
        scanf("%d%d%d",&x,&y,&k);
            printf("%d\n", solve(x,y,k));
        }
        else
        {
        scanf("%d%d",&x,&k);
        update(x, A[x], -1);
        A[x] = k;
        update(x, A[x], 1);
        }
    }

    for(int i=1;i<=n;i++)
        update(i, A[i], -1);
    }
    return 0;
}
{% endhighlight %}