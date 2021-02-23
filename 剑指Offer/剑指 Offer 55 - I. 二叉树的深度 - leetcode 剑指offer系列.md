> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

- 节点总数 <= 10000

## 题目样例

### 示例

```
给定二叉树 [3,9,20,null,null,15,7]，

    3
   / \
  9  20
    /  \
   15   7
返回它的最大深度 3 。
```

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 先考虑递归做法, 尝试 DFS
- 我们可以这样构造递归方法: 传入节点, 返回当前节点的深度, 该深度是左右子树的最大深度+1
- 假设叶子节点的深度为 1, 显然根节点的深度就是整个树的最大深度了
- 递归出口即节点为空的情况, 此时深度为 0

#### 复杂度

- 时间复杂度 O(N): 需要遍历整个树
- 空间复杂度 O(H): H 表示树的高度, 也即递归的栈的消耗

#### 代码

```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            # 递归出口, 空节点的情况
            return 0
        # 当前节点深度是左右子树的最大深度+1
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
        # 也可以进一步简化为只需要一行代码..
        # return 0 if not root else 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

### 方案 2

#### 思路

- 如果要求必须用迭代方式实现, 那方案 1 就不行了
- 迭代一般可以先尝试 BFS, 这道题也不例外
- 通过分析题目, 显然这里的深度就是指 BFS 的层数, 所以完全可以利用[剑指 Offer 32 - II. 从上到下打印二叉树 II - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484055&idx=1&sn=eb46c80176a468ea276ea3746978b501&chksm=9002859aa7750c8c0a010b5df67f411b538d791d5600946773fae1dcecefe3a96e46c115c4f4&token=44591176&lang=zh_CN#rd)的做法得出层数, 只是不需要打印出每一层的节点, 只需要统计层数即可. 不清楚的同学可以先看看那道题的思路~
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 需要遍历整个树
- 空间复杂度 O(N): 队列的空间消耗

#### 代码

```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        q = [root]
        res = 0
        while q:
            # 当前层节点数目
            curlen = len(q)
            for node in q[:curlen]:
                # 只追加非空子节点
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            # 队列切片, 开始处理下一层
            q = q[curlen:]
            # 当前层遍历完毕, 深度+1
            res += 1
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
