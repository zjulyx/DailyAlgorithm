> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

给定一棵二叉搜索树，请找出其中第 k 大的节点。

- 1 ≤ k ≤ 二叉搜索树元素个数

## 题目样例

### 示例

```
输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
输出: 4
```

## 题目思考

1. 如何利用二叉搜索树的性质?
2. 可以做到 O(1) 空间复杂度吗?

## 解决方案

### 方案 1

#### 思路

- 二叉搜索树的一个很重要的性质是其中序遍历是升序数组
- 所以我们可以直接利用这一性质, 采用中序遍历
- 但注意这个题目是求第 k 大, 所以我们需要对原始的中序遍历顺序进行翻转: **先访问右子树, 再访问当前节点, 最后访问左子树**, 这样保证了遍历序列是降序
- 这样每次遍历一个节点就把 k 减 1, 减为 0 的时候对应的节点自然就是第 k 大的节点了~

#### 复杂度

- 时间复杂度 O(K): 需要遍历 K 个节点
- 空间复杂度 O(H): H 表示树的高度, 也即递归的栈的消耗

#### 代码

```python
class Solution:
    def kthLargest(self, root: TreeNode, k: int) -> int:
        # 反向中序遍历, 直接记录当前下标, 无需额外空间
        res = None

        def revInorder(node):
            nonlocal k
            nonlocal res
            if not node or res is not None:
                # 当前节点为空或者已经找到目标节点了, 直接退出
                return
            revInorder(node.right)
            k -= 1
            if k == 0:
                # k减到了0, 说明当前节点就是第k大节点, 保存下来并退出
                res = node.val
                return
            revInorder(node.left)

        revInorder(root)
        return res
```

### 方案 2

#### 思路

- 思路同方案 1, 将传统的递归中序遍历转为 morris 中序遍历, 即可以只使用常数空间
- 同样的, 这里需要改为先访问右子树, 找右边的先序节点指向当前节点
- 注意该方案属于进阶内容, 下面代码给出了必要的注释, 感兴趣的同学可以了解一下, 自己画个图可能更好理解算法的过程
  > 不熟悉 morris 遍历的同学可以参考这篇文章: [What is Morris traversal?](https://www.educative.io/edpresso/what-is-morris-traversal), 解释的蛮清楚的, 也可以举一反三试试 morris 前序和后序遍历

#### 复杂度

- 时间复杂度 O(K): 需要遍历 K 个节点
- 空间复杂度 **O(1)**: 由于没有使用递归, 且只使用了 1 个变量, 所以空间复杂度是常数

#### 代码

```python
class Solution:
    def kthLargest(self, root: TreeNode, k: int) -> int:
        cur = root
        while cur:
            # 此处要先访问右子树, 所以和原始morris遍历的条件刚好相反
            shouldHandleCurNode = True
            if cur.right:
                # 右子树还有内容, 先找先序节点last
                # 先序节点last是指当前节点前一个遍历的节点
                last = cur.right
                while last.left and last.left != cur:
                    last = last.left
                if not last.left:
                    # 需要将先序指向cur, 此时右子树还没遍历完, 不能直接处理cur节点
                    # 接下来需要往右子节点继续遍历
                    shouldHandleCurNode = False
                    last.left = cur
                    cur = cur.right
                else:
                    # 右子树遍历完了, 断开先序和cur的连接, 恢复原来树的结构
                    # 此时意味着可以处理当前节点了, 所以不改变shouldHandleCurNode, 从而走到下面的逻辑
                    last.left = None
            if shouldHandleCurNode:
                # 可以处理当前节点了 (即遍历到了当前节点)
                # 对应两种情况: 1. 右子树本身就不存在 (cur.right is None); 2. 右子树遍历完了 (上面的else部分)
                # 接下来应该往左子树遍历
                k -= 1
                if k == 0:
                    return cur.val
                cur = cur.left
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
