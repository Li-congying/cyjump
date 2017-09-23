---
title: K-edit-distance
date: 2017-09-13 10:42:51
tags: algorithm
---
最近做了一下k edit distance，通过一题，可以了解到trie数，动态规划和深搜三个算法，可以说是非常精妙了

### Edit Distance
先做word edit distance  
有：A='abc'，B='ac'  
可以做的操作有：  
1，remove一个字母，2，add一个字母，3，replace一个字母。  
求A到B需要几步edit
<!--more-->

这是个非常标准的类似于求解公共子串的动态规划问题  
令dp[i][j] 表示，从A[0:i] 到 B[0:j] 有几步edit  
从而递推公式为：
```
if A[i+1] == B[j+1]: dp[i+1][j+1] = dp[i][j]  
if A[i+1] != B[j+1]: dp[i+1][j+1] = min(dp[i-1][j]+1, dp[i][j-1]+1, dp[i-1][j-1]+1)
```
解释一下就是，如果当前要比较的两个字母相同，那么editdistance 就不变，若不同，则总可以通过增加一步操作来达到，replace 一个 or 删掉一个 or 新增一个，选择步数少的那一个。  
画个表格更清晰：（markdown还不太会用不会做表格并列，好烦）

|| 0 | a | b | c |
|---|---|---|---|---|
|0|0|1|2|3|
|a|1||||
|c|2||||

|| 0 | a | b | c
|---|---|---|---|---
|0|0|1|2|3
|a|1|**0**|**1**|**2**
|c|2|

|| 0 | a | b | c
|---|---|---|---|---
|0|0|1|2|3
|a|1|0|1|2
|c|2|**1**|**1**|**1**

以最外层的循环为例子：  
1，初始化当空字符串与另一个串比较时的结果  
2，从第二个word的第一个字母开始与第一个word的字母进行比较  
3，填充整个表格，得到最后dp[i][j] 就是最终结果
代码如下
```
def editDistance(word1, word2):
    n1 = len(word1)
    n2 = len(word2)
    dp = [[j+i for j in range(n1+1)] for i in range(n2+1)]
    for i in range(1,n2+1):
        for j in range(1, n1+1):
            if word1[j-1] == word2[i-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = min(dp[i-1][j] + 1, dp[i][j-1] +1, dp[i-1][j-1]+1)
    return dp[n2][n1]
```

### K eidt distance
给定一个字符串组与target字符串  
words = ['abc', 'abd', 'bc', 'adca']  
target = 'ac'  
求解 words 里与 target 的edit distance 在k以内的字符串们

刚做完两两比较的，就会想到用target和words里每个字符串都两两比较，（一说到两两比较我就害怕，好像大牛always能能找到比两两比较更好的办法，然后让你也说出来，我一个小白菜哪能回回都想出来）

当然这一题也可以，就要借助到trie树，trie树就不细说了吧。大概意思就是，abc abd 不用重复计算ab

以 abc abd 为例，构建的trie树就是
```
a
 \
  b
 / \
c   d
```
等同于我们要构建动态规划的表格就是如下的两个

|| 0 | a | b | **c**    || 0 | a | b | **d** 
|---|---|---|---|---|---|---|---|---|---
|0|0|1|2|3||0|1|2|3|
|a|1|||||a|1|||
|c|2|||||c|2||||

于是我们从a,b,c,d 以此开始比较，递归调用，每次传递前一列的结果  
从 0 与 ac开始，生成 a 列，a 列用于计算b列，b列用于计算 c列与d列，如此递归下去

```
def getKdistanceWord( word_list, target, k):
    #生成trie树
    nodes = {}
    for word in word_list:
        node = nodes
        for i in range(len(word)):
            if word[i] not in node:
                node[word[i]] = {}
            node = node[word[i]]
            if i == len(word)-1:
                node['#'] = 1
     
    # 构建递归式
    prev_dp 为前一列dp结果, w为当前的字母，node 当前字典树中的节点
    def helper(prev_dp, w, node, target, prev_str, result):
        curr_dp = [prev_dp[0] + 1]
        for i in range(len(target)):
            if target[i] == w:
                curr_dp.append(prev_dp[i])
            else:
                min_dis = min(prev_dp[i]+1, curr_dp[i]+1, prev_dp[i+1]+1)
                curr_dp.append(min_dis)
        if curr_dp[-1] <= k and '#' in node:
            result.append(prev_str+w)
        for nxt in node:
            if nxt != '#':
                helper(curr_dp, nxt, node[nxt], target, prev_str+w, result)


    result = []
    for key in nodes:
        # 初始化第一列
        prev_dp = [i for i in range(len(target) + 1)]
        helper(prev_dp, key, nodes[key], target, "", result)
        
    return result
```
