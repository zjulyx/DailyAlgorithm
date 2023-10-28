> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/P5rCT8/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一棵二叉搜索树和其中的一个节点 p ，找到该节点在树中的中序后继。如果节点没有中序后继，请返回 null 。

节点 p 的后继是值比 p.val 大的节点中键值最小的节点，即按中序遍历的顺序节点 p 的下一个节点。

### 示例 1：

![](https://assets.leetcode.com/uploads/2019/01/23/285_example_1.PNG)

- 输入：root = [2,1,3], p = 1
- 输出：2
- 解释：这里 1 的中序后继是 2。请注意 p 和返回值都应是 TreeNode 类型。

### 示例 2：

![](https://assets.leetcode.com/uploads/2019/01/23/285_example_2.PNG)

- 输入：root = [5,3,6,2,4,null,null,1], p = 6
- 输出：null
- 解释：因为给出的节点没有中序后继，所以答案就返回 null 了。

### 提示：

- 树中节点的数目在范围 [1, 10^4] 内。
- -10^5 <= Node.val <= 10^5
- 树中各节点的值均保证唯一。

## 题目思考

1. 如何尽可能降低时间和空间复杂度?
2. 如何利用二叉搜索树的性质?

## 解决方案

#### 思路

- 根据题目描述, 一个很容易想到的思路就是中序遍历, 同时存储当前遍历到的节点的前一节点 pre, 这样当 pre 是 p 时, 也就意味着当前节点就是 p 的中序后继, 将它保存为最终结果并返回即可
- 不过这样我们就得从头开始遍历所有节点, 而且会有递归栈的额外空间消耗, 另外还没有用到二叉搜索树这个条件, 有没有更好的方法呢?
- 回顾二叉搜索树的性质, 其中序遍历的节点是有序的, 那么某个节点的中序后继, 一定是对应有序数组右侧紧挨着它的元素, 也即大于 p 的最小节点
- 我们可以利用这一特点, 采用二分查找, 具体过程如下:
  - 从根节点开始, 如果当前节点大于 p 节点时, 向左子树方向继续查找, 否则就向右子树方向查找
  - 在查找的同时, 维护一个变量 res 保存最终结果, 并初始化为空
  - 如果当前节点大于 p 节点, 且 res 为空或者 res 小于当前节点, 就更新 res 为当前节点
- 这样最终二分查找结束时, res 对应的节点就是 p 的中序后继
- 下面代码中包含了上述两种做法, 并有详细的注释, 方便大家理解

#### 复杂度

- 中序遍历
  - 时间复杂度 O(N): 从头开始遍历每个节点, O(N)
  - 空间复杂度 O(H): 递归调用最多使用 O(H) 栈空间, H 是树的高度
- 二分查找
  - 时间复杂度 O(logN): 每次二分都只需要朝某个方向继续查找, 所以是 O(logN)
  - 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def inorderSuccessor(self, root: "TreeNode", p: "TreeNode") -> "TreeNode":
        # 方法1: 中序遍历
        res = None
        pre = None

        def inorder(cur):
            nonlocal res, pre
            if not cur:
                return
            inorder(cur.left)
            if pre == p:
                # pre是p, 自然cur就是p的中序后继
                res = cur
            # 更新pre为当前节点, 用于下个节点的判断
            pre = cur
            inorder(cur.right)

        inorder(root)
        return res
        # 方法2: 二分查找
        cur = root
        res = None
        while cur:
            if cur.val > p.val:
                if res is None or cur.val < res.val:
                    # 当前节点是第一个大于p的, 或者它小于res, 更新它为最终结果
                    res = cur
                # 向右子树方向继续查找
                cur = cur.left
            else:
                # 向左子树方向继续查找
                cur = cur.right
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
