> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某公司架构以二叉树形式记录，请返回该公司的层级数。

### 示例 1：

![](https://pic.leetcode.cn/1695101942-FSrxqu-image.png)

- 输入：root = [1, 2, 2, 3, null, null, 5, 4, null, null, 4]
- 输出: 4
- 解释: 上面示例中的二叉树的最大深度是 4，沿着路径 1 -> 2 -> 3 -> 4 或 1 -> 2 -> 5 -> 4 到达叶节点的最长路径上有 4 个节点。

### 提示：

- 节点总数 <= 10000

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
    def calculateDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            # 递归出口, 空节点的情况
            return 0
        # 当前节点深度是左右子树的最大深度+1
        return 1 + max(self.calculateDepth(root.left), self.calculateDepth(root.right))
        # 也可以进一步简化为只需要一行代码..
        # return 0 if not root else 1 + max(self.calculateDepth(root.left), self.calculateDepth(root.right))
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
    def calculateDepth(self, root: Optional[TreeNode]) -> int:
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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
