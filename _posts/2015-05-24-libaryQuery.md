---
layout: post
title:  hackerRank 题目 - library query
categories: [hackerrank]
tags: [algorithm, fenwick tree, advanced]
fullview: true
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

* update: \\(O(log(n)\\)
* query: \\(O(log(n)\\)

应该是需要用 [fenwick tree](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-trees/)，先看看算法材料再来做吧。