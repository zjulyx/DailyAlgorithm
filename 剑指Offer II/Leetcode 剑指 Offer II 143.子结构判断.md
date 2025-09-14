> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/shu-de-zi-jie-gou-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两棵二叉树 tree1 和 tree2，判断 tree2 是否以 tree1 的某个节点为根的子树具有 相同的结构和节点值 。
注意，空树 不会是以 tree1 的某个节点为根的子树具有 相同的结构和节点值 。

### 示例 1：

![](https://pic.leetcode.cn/1694684670-vwyIgY-two_tree.png)

- 输入：tree1 = [1,7,5], tree2 = [6,1]
- 输出：false
- 解释：tree2 与 tree1 的一个子树没有相同的结构和节点值。

### 示例 2：

![](https://pic.leetcode.cn/1694685602-myWXCv-two_tree_2.png)

- 输入：tree1 = [3,6,7,1,8], tree2 = [6,1]
- 输出：true
- 解释：tree2 与 tree1 的一个子树拥有相同的结构和节点值。即 6 - > 1。

### 提示：

0 <= 节点个数 <= 10000

## 题目思考

1. 子结构有哪些性质?

## 解决方案

### 思路

- 分析题目, B 是 A 的子结构的话, 那么 B 的节点结构需要完全是 A 的某一部分, 而且该部分中的一部分子树可以不在 B 中(例如题目中 A 的 2 号节点就不在 B 中)
- 所以我们可以额外定义一个方法, 来递归比较当前 A 和 B 节点是否满足子结构关系
- 然后从 A 和 B 的根节点开始调用该方法, 如果当前满足条件就直接返回 true, 否则就将 A 移动到其左右子节点位置, 重新与 B 的根节点比较即可
- 下面代码中有详细的注释, 方便大家理解

### 复杂度

- 时间复杂度 `O(MN)`
  - 假设 A 和 B 的节点数目分别是 M 和 N, 那么最差情况是对于每个 A 节点, 都要调用一次 match 方法遍历整个 B 树, 所以时间复杂度是 `O(MN)`
- 空间复杂度 `O(M)`
  - isSubStructure 递归调用则最多使用 `O(M)` 递归栈空间, match 递归调用使用 `O(min(M, N))` 递归栈空间, 所以整体空间复杂度为 `O(M)`

### 代码

```python
class Solution:
    def isSubStructure(self, A: TreeNode, B: TreeNode) -> bool:
        if not A or not B:
            # 根据题意, B如果是空树一定不满足条件, 而A是空树的话B更不可能是其子结构了
            return False

        def match(a, b):
            if not b:
                # 因为A的子树部分可以有部分节点是B没有的, 所以如果当前b节点是空的话是满足条件的情况, 直接返回true
                return True
            if not a:
                # 此时b节点还有值, 但a节点是空了, B不可能是A的子结构
                return False
            # 既要当前a和b的值相等, 同时各自左右子树部分也要匹配
            return a.val == b.val and match(a.left, b.left) and match(
                a.right, b.right)

        # B是A的子结构的充要条件: 要么当前A和B匹配, 要么A的左右子节点和B匹配
        return match(A, B) or self.isSubStructure(
            A.left, B) or self.isSubStructure(A.right, B)
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
