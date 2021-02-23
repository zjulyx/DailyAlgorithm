> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

> 今天继续更新剑指 offer 系列, 这道题有一些难度, 特别需要理解清楚题意

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入两棵二叉树 A 和 B，判断 B 是不是 A 的子结构。(约定空树不是任意一个树的子结构)

B 是 A 的子结构， 即 A 中有出现和 B 相同的结构和节点值。

例如:
给定的树 A:

```
     3
    / \
   4   5
  / \
 1   2
```

给定的树 B：

```
   4 
  /
 1
```

返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

0 <= 节点个数 <= 10000

## 题目样例

### 示例 1

#### 输入

A = [1,2,3], B = [3,1]

#### 输出

false

### 示例 2

#### 输入

A = [3,4,5,1,2], B = [4,1]

#### 输出

true

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

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
