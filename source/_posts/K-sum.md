---
title: K-sum解题报告
date: 2017-09-23 18:41:42
tags: algorithm
---
前前后后折腾了好久，终于把博客搭起来了，感谢哥哥帮忙，最近的日子过的真是一言难尽，但所幸有些成长和进步虽说离最终目标还有很远，但是不要放弃，继续努力！

牢骚完了，就更新一下最近一个觉得又点绕的题，Ksum
题目如下
```
Given n distinct positive integers, integer k (k <= n) and a number target.

Find k numbers where sum is target. Calculate how many solutions there are?

example: Given [1,2,3,4], k=2, target=5. There are 2 solutions:
[1,4] and [2,3], return 2.
```
<!--more-->

这一题我一开始用深搜 暴力解的但是超时了，网上找了一些题解看发现要用动态规划解，当时理解了，后面过几天又忘记了，于是打算记下来，我这脑子，忘性太大，另外感慨一下，动态规划真是个神奇的东西。

假设我们有list=[1,2,3,4], k = 2, target = 5;   
那么需要被缓存的内容，是这样的一组值：由k_k个元素可以组成的小于target的组合的个数。    

比如我们求 两个可以组成5的，那我们在从 1 到1，2到1，2，3... 的过程中可以得到的是： 由1个元素可以组合得到1，2，3...的个数，那如果需要两个元素组合得到5，只需要根据当前元素找到 dp[k-1][target-current] 的个数就可以了。

例如如下的计算过程
```
list = [1,2,3,4]
k = 2, target = 5
[1] ==> dp[1][1] = 1, target-1 = 4, dp[1][4] == 0 => dp[2][5] = 0
[1,2] ==> dp[1][2] = 1, target-2 = 3, dp[1][3] == 0 => dp[2][5] = 0
[1,2,3] ==> dp[1][3] = 1, target-3 =2, dp[1][2] == 1=> dp[2][5] = 1
[1,2,3,4] ==> dp[1][4] = 1, target-4 =1, dp[1][1] == 1=> dp[2][5] = 2
```
上代码
所以我们首先建立一个dp[k][target] 的数组，默认初始值为0
```
def k_sum(list, k, target):
    dp = {key:{t:0 for t in range(0, target+1)} for key in range(1, k+1)}
    for i in range(len(list)):
        num = list[i]
        if num > target:
            continue
        max_k = min(k, i+1)  # 得到可以最多组合多少个数字   
        for kk in range(2, max_k+1): 
            for t in range(target+1): # 从0到target计算可以得到的组合个数
                if num > t:
                    continue
                #print 'kk', kk, t, num, dp[kk-1][t-num], dp[kk][t]
                dp[kk][t] += dp[kk-1][t-num]

        dp[1][num] = 1 #  放在最后是为了避免2+2=4的重复选取情况

    return dp[k][target]
```
