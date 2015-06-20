---
layout: post
title:  LeetCode Dungeon Game 
categories: [leetcode]
tags:  [Dynamic Programming, Java]
fullview: false

---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

似乎好久没有做Leetcode了，今天是这道题：

* [Dungeon Game](https://leetcode.com/problems/dungeon-game/).

一道不错的DP 题目，讲的是一个勇士能够走完地牢的最小生命值。 

基本的道理是：

	dp[i][j] 代表从(i, j)到终点的最小生命值

	dp[i][j] ＝ Min(dp[i + 1][j], dp[i][j + 1]) + (在dungeon[i][j] 最小的生命值)
	
code 如下：

{% highlight java %}
package dungeonGame;
 
public class Solution {
	public int calculateMinimumHP(int[][] dungeon) {
		int width = dungeon.length;
		int height = dungeon[0].length;
		for (int i = width - 1; i >= 0; i--) {
			for (int j = height - 1; j >= 0; j--) {
				if (i == width - 1 && j == height - 1) {
					dungeon[i][j] = dungeon[i][j] > 0 ? 1 : 1 - dungeon[i][j];
				} else if (j == height - 1) {
					int result = dungeon[i + 1][j] - dungeon[i][j];
					dungeon[i][j] = result > 0 ? result : 1;
				} else if (i == width - 1) {
					int result = dungeon[i][j + 1] - dungeon[i][j];
					dungeon[i][j] = result > 0 ? result : 1;
				} else {
					int result1 = dungeon[i][j + 1] - dungeon[i][j];
					int result2 = dungeon[i + 1][j] - dungeon[i][j];
					int result = Math.min(result1, result2);
					dungeon[i][j] = result > 0 ? result : 1;
				}
			}
		}
		return dungeon[0][0];
	}
}
{% endhighlight %}