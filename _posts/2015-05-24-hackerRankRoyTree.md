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
  	      \                          \      /
   	3                          2   1
* 类似地，我们可以计算出第4组测试数据的答案为54。


## 解题

好了，题目抄完了，该怎么做呢？

实际上是一个典型的dynamic programming 的题目。  这里有一个明显的递归。这个应该是解体的线索。

定义*ans[i][j]*是a[i..j] 的兴趣度。在构建树结构的时候，假设root 是a[k]，那么ans[i[j] 可以这么算：

{% highlight python %}
ans[i][j] = 0
for k in [i..j]:
	ans[i][j] += ans[i][k - 1] * (number of right subtrees) 
	ans[i][j] += ans[k + 1][j] * (number of left subtrees) 
	ans[i][j] += (contribution from root)
{% endhighlight %}

ans[i][k-1] 和 ans[k+1][j-1] 都可以通过dynamic programming 的数组中读出来，但是 *contribution from root* 这个项需要分情况讨论，如果root 所处的位置是odd level，那么   

	contribution from root = (total number of trees) * alpha * a[k]

如果 root 所处的位置是 even level, 那么：  

	contribution from root = (-1) * (total number of trees) * beta * a[k]

那么最后一个问题就是计算 tree count. 这是 [Catalan number](http://en.wikipedia.org/wiki/Catalan_number). 

dynamic programming 的相关数组是dp[lo][hi][parity], 其中\\(lo \in [1..n]\\)，\\(hi \in [1..n]\\), \\(parity \in [-1,1]\\). 空间复杂度是\\(O(n^2)\\)，时间复杂度是\\(O(n^3)\\)，因为 n < 150，所以可以接受。

Sample Solution (Java): 

{% highlight java %}

import java.io.*;
import java.util.Arrays;
import java.util.StringTokenizer;

class ANKBST02_solver {
    long go(int lo, int hi, int odd) {
        if (lo > hi) {
            return 0;
        }
        if (dp[lo][hi][odd] == -1) {
            long ans = 0;
            for (int root = lo; root <= hi; root++) {
                //consider all BST in left subtree of root
                ans += (go(lo, root - 1, 1 -odd) * cnt[hi - root - 1 + 1]) % MOD;
                if (ans >= MOD) ans -= MOD;
                if (ans < 0) ans += MOD;
                //consider all BST in right subtree
                ans += (go(root + 1, hi, 1 -odd) * cnt[root - 1 - lo + 1]) % MOD;
                if (ans >= MOD) ans -= MOD;
                if (ans < 0) ans += MOD;
                //totTrees is total number of trees considered
                long totTrees = (cnt[hi - root] * cnt[root - lo]) % MOD;
                //remember to add the root as many times for each tree
                ans += (totTrees * ((mul[odd] * a[root]) % MOD)) % MOD;
                if(ans >= MOD) ans -= MOD;
                if (ans < 0) ans += MOD;
            }
            dp[lo][hi][odd] = ans;
        }
        return dp[lo][hi][odd];
    }

    public void solve() throws IOException {
        cnt = generateCatalan(205, MOD);
        cnt[0] = 1;
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter pw = new PrintWriter(System.out);
        int t = Integer.parseInt(br.readLine());

        while (t --> 0) {
            int n = Integer.parseInt(br.readLine());
            assert(n <= 200);
            a = new long[n] ;
            mul = new long[2];
            int i = 0;
            for (StringTokenizer tokenizer = new StringTokenizer(br.readLine()); tokenizer.hasMoreTokens(); ) {
                String s = tokenizer.nextToken();
                mul[i ++] = Integer.parseInt(s);
            }
            mul[1] = -mul[1];
            i = 0;
            for (StringTokenizer tokenizer = new StringTokenizer(br.readLine()); tokenizer.hasMoreTokens(); ) {
                String s = tokenizer.nextToken();
                a[i ++] = Integer.parseInt(s);
            }
            assert(i == n);
            Arrays.sort(a);
            dp = new long[n][n][2];
            for (int j = 0; j < n; j++) {
                for (int k = 0; k < n; k++) {
                    for (int l = 0; l < 2; l++) {
                        dp[j][k][l] = -1;
                    }
                }
            }
            pw.println(go(0, n - 1, 0));
        }
        pw.close();
    }

    long[] a;
    long[][][] dp;
    long[] cnt;
    long MOD = (int) (1e9 + 9);
    long[] mul;

    public static long[] generateCatalan(int n, long module) {
        long[] inv = generateReverse(n + 2, module);
        long[] Catalan = new long[n];
        Catalan[1] = 1;
        for (int i = 1; i < n - 1; i++) {
            Catalan[i + 1] = (((2 * (2 * i + 1) * inv[i + 2]) % module) * Catalan[i]) % module;
        }
        return Catalan;
    }

    public static long[] generateReverse(int upTo, long module) {
        long[] result = new long[upTo];
        if (upTo > 1)
            result[1] = 1;
        for (int i = 2; i < upTo; i++)
            result[i] = (module - module / i * result[((int) (module % i))] % module) % module;
        return result;
    }
}

public class Solution {
    public static void main(String[] args) throws IOException {
            new ANKBST02_solver().solve();
    }
}

{% endhighlight %}

