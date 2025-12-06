> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给你二叉树的根节点 root 和一个整数目标和 targetSum ，找出所有 从根节点到叶子节点 路径总和等于给定目标和的路径。

叶子节点 是指没有子节点的节点。

示例 1：

- 输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
- 输出：[[5,4,11,2],[5,8,4,5]]

### 示例 2：

- 输入：root = [1,2,3], targetSum = 5
- 输出：[]

### 示例 3：

- 输入：root = [1,2], targetSum = 0
- 输出：[]

### 提示：

- 树中节点总数在范围 [0, 5000] 内
- -1000 <= Node.val <= 1000
- -1000 <= targetSum <= 1000

## 题目思考

1. 如何用递归思路来解决? 需要保存哪些信息?
2. 可以用前几期的 BFS 解决吗?

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
    def pathTarget(self, root: Optional[TreeNode], target: int) -> List[List[int]]:
        if not root:
            return []
        res = []

        def dfs(node, path, cur):
            if cur == target and not node.left and not node.right:
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
- BFS 就是迭代, 但回忆前面几期的几种 BFS 方案 (`剑指 Offer II 149~151`), 都只是存了节点本身, 我们如何把路径以及路径和关联起来呢?
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
    def pathTarget(self, root: Optional[TreeNode], target: int) -> List[List[int]]:
        if not root:
            return []
        # 初始化根节点/路径/当前和
        q = [(root, [root.val], root.val)]
        res = []
        for t in q:
            node, path, sm = t
            if not node.left and not node.right and sm == target:
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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
