> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

> 今天继续更新剑指 offer 系列, 这道题挺经典的, 这里提供递归和迭代两种方案供大家参考

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。

- 节点总数 <= 10000

## 题目样例

### 示例

#### 输入

给定如下二叉树，以及目标和 sum = 22，

```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
```

#### 输出

```
[
   [5,4,11,2],
   [5,8,4,5]
]
```

## 题目思考

1. 如何用递归思路来解决? 需要保存哪些信息?
2. 可以用前几天的 BFS 解决吗?

## 解决方案

### 方案 1

#### 思路

- 首先考虑递归思路
- 这道题很适合用 DFS 来做, 因为在遍历的过程中可以保存路径中的节点, 以及当前和之类的信息
- 最后到叶子节点时只需要判断当前和是否等于目标, 是的话将路径加入结果中即可
- 递归出口自然就是当前节点是叶子节点了
- 如果不是叶子节点的话, 就递归调用非空的子节点, 将其值加入和中, 节点本身加入路径中即可
- 特别注意的是, 由于题目没有说节点值非负, 所以必须老老实实走到叶子节点才知道和是否等于目标. 不然如果节点值都是非负数的话, 就可以在当前和大于目标时直接返回了, 之后的和只可能更大, 肯定不满足条件

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N^2)`
  - 递归的栈的消耗是 N, 然后每个递归调用里面又要分配 N 个节点的路径

#### 代码

```python
class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        if not root:
            return []
        res = []

        def dfs(node, path, cur):
            if cur == sum and not node.left and not node.right:
                # 当前是叶子节点, 且和正好等于目标, 将路径加入结果中
                res.append(path)
            if node.left:
                # 递归遍历非空左子节点
                dfs(node.left, path + [node.left.val], cur + node.left.val)
            if node.right:
                # 递归遍历非空右子节点
                dfs(node.right, path + [node.right.val], cur + node.right.val)

        # 注意初始的路径以及和
        dfs(root, [root.val], root.val)
        return res
```

### 方案 2

#### 思路

- 如果限制只能用迭代的话, 就不能用 DFS 了
- BFS 就是迭代, 但回忆之前的几种 BFS 方案 (`剑指 Offer 32`), 都只是存了节点本身, 我们如何把路径以及路径和关联起来呢?
- 做法也很简单, 既然需要存额外信息, 我们就把队列的内容改造一下, 存成`(节点, 路径, 当前和)`三元组, 然后添加子节点的时候附带上对应路径以及当前和
- 这样如果当前节点是叶子节点的话, 只需要比较当前和与目标即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N^2)`
  - 队列需要存 N 个三元组, 每个元组中的路径又包含 N 个节点

#### 代码

```python
class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        if not root:
            return []
        # 初始化根节点/路径/当前和
        q = [(root, [root.val], root.val)]
        res = []
        for t in q:
            node, path, sm = t
            if not node.left and not node.right and sm == sum:
                # 当前是叶子节点, 且和正好等于目标, 将路径加入结果中
                res.append(path)
            if node.left:
                # 将非空左子节点/路径/当前和加入队列
                q.append(
                    (node.left, path + [node.left.val], sm + node.left.val))
            if node.right:
                # 将非空右子节点/路径/当前和加入队列
                q.append(
                    (node.right, path + [node.right.val], sm + node.right.val))
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
