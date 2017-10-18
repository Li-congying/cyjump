---
title: 二叉树相关内容整理（1）
date: 2017-10-18 14:37:14
tags: algorithm
---
总结一下二叉树相关的高频题目
### 前，中，后序遍历
前序遍历（中－左－右）：
```
递归：
    print root.val,
    if root.left:
        self.pre_order(root.left)
    if root.right:
        self.pre_order(root.right)
    
非递归：
    node_stack = [root]
    while node_stack:
        node = node_stack.pop()
        if node:
            print node.val,
        if node.right:
            node_stack.append(node.right)
        if node.left:
            node_stack.append(node.left)
```
<!--more-->
中序遍历（左－中－右）
```
递归
if root.left:
    self.inner_order(root.left)
if root:
    print root.val,
if root.right:
    self.inner_order(root.right)

非递归
node_stack = []
node = root
while node or node_stack:
    if node:
        node_stack.append(node)
        node = node.left    //每次压最左节点入栈
    else:
        node = node_stack.pop() 
        print node.val, //依次弹出左
        node = node.right //压入右
```
后序遍历（左－右－中）
```
递归：
if root.left:
    self.post_order(root.left)
if root.right:
    self.post_order(root.right)
if root:
    print root.val,

非递归：
node_stack = []
status_stack = [] //多维护一个栈存存储某节点的右节点是否访问过
node = root
//类似于中序，当一个节点的右节点未被访问时，重新入栈，先访问右节点
while node or node_stack:
    if node:
        node_stack.append(node)
        status_stack.append(0)
        node = node.left
    else:
        node = node_stack.pop()
        status = status_stack.pop()
        if status:
            print node.val,
            node = None //访问中间节点后，置判断条件为0，继续pop下一个节点
        else:
            node_stack.append(node)
            status_stack.append(1)
            node = node.right

```
### 求解搜索二叉树某节点X后继节点
    利用中序遍历，左中右
    1，若X有右子树，查找右子树的最左节点
    2，若X无右子树，则查找X的父节点P
        a，若X为P的坐子树，P为后继
        b，若X为P的右子树，则查找P的后继
```
public static TreeNode inorderSucc(TreeNode e) {    
    if(e!=null){
        TreeNode p;
        // Found right children -> return 1st inorder node on right 
        if (e.parent == null || e.right != null) {
            p = leftMostChild(e.right); } 
        else {
        // Go up until we’re on left instead of right (case 2b) 
            while ((p = e.parent) != null) {
                if (p.left == e) { 
                    break;
                }
                e = p; 
            }
        }
        return p; 
    }
    return null; 
}
```

### 检查平衡二叉树是否合法
    查找mindepth and maxdepth
    递归查找左右子树
    eg：return 1 + min(getDepth(root.left), getDepth(root.right))
    确认 maxdepth－mindepth <= 1

### 有向图查找两点间是否存在路径
    DFS/BFS
    eg BFS
    用queue 一层一层查找，记录visited 列表
    
### 为二叉树每一层构建数据链表
    level traverse
    将node与高度一起推入queue，根据高度构建linked list
    
### LCA 查找最近公共祖先
    递归思路：
        1，若root为空，返回false
        2，若root＝p or root＝q，返回root
        3，递归左子树，右子树
        4，若左右均能找到，说明root一左一右为两个节点，返回root，否则返回不为空的那一方
        

### 检查搜索二叉树是否合法
    中序遍历：
        1，中序遍历数，返回列表
        2，检查列表是否严格递增
        3，注意空树的边界条件
        
### 在一个大的二叉树A中查找另一个较小的二叉树B
    递归：
        1，B==NULL return True
        2，A==NULL return False
        3，如果当前A_node.val == B_node.val 检查左右子树是否match 
        4，检查A_node.right ／A_node.left 与B
    
    当树较小时，可以用如下解法：
        1，求A，B的 前序中序遍历
        2，若B的前序中序都是A的字串，则B为A的子树
        
### Tree Sum
    to be solved
    

    
