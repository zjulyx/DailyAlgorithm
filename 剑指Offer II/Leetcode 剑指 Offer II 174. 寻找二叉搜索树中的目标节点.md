> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某公司组织架构以二叉搜索树形式记录，节点值为处于该职位的员工编号。请返回第 cnt 大的员工编号。

### 示例 1：

![](https://pic.leetcode.cn/1695101634-kzHKZW-image.png)

- 输入：root = [7, 3, 9, 1, 5], cnt = 2

```
       7
      / \
     3   9
    / \
   1   5
```

- 输出：7

### 示例 2：

![](https://pic.leetcode.cn/1695101636-ESZtLa-image.png)

- 输入: root = [10, 5, 15, 2, 7, null, 20, 1, null, 6, 8], cnt = 4

```
       10
      / \
     5   15
    / \    \
   2   7    20
  /   / \
 1   6   8
```

- 输出: 8

### 提示：

- 1 ≤ cnt ≤ 二叉搜索树元素个数

## 题目思考

1. 如何利用二叉搜索树的性质?
2. 可以做到 O(1) 空间复杂度吗?

## 解决方案

### 方案 1

#### 思路

- 二叉搜索树的一个很重要的性质是其中序遍历是升序数组
- 所以我们可以直接利用这一性质, 采用中序遍历
- 但注意这个题目是求第 cnt 大, 所以我们需要对原始的中序遍历顺序进行翻转: **先访问右子树, 再访问当前节点, 最后访问左子树**, 这样保证了遍历序列是降序
- 这样每次遍历一个节点就把 cnt 减 1, 减为 0 的时候对应的节点自然就是第 cnt 大的节点了~

#### 复杂度

- 时间复杂度 O(CNT): 需要遍历 CNT 个节点
- 空间复杂度 O(H): H 表示树的高度, 也即递归的栈的消耗

#### 代码

```python
class Solution:
    def findTargetNode(self, root: Optional[TreeNode], cnt: int) -> int:
        # 反向中序遍历, 直接记录当前下标, 无需额外空间
        res = None

        def revInorder(node):
            nonlocal cnt
            nonlocal res
            if not node or res is not None:
                # 当前节点为空或者已经找到目标节点了, 直接退出
                return
            revInorder(node.right)
            cnt -= 1
            if cnt == 0:
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

- 时间复杂度 O(CNT): 需要遍历 CNT 个节点
- 空间复杂度 **O(1)**: 由于没有使用递归, 且只使用了 1 个变量, 所以空间复杂度是常数

#### 代码

```python
class Solution:
    def findTargetNode(self, root: Optional[TreeNode], cnt: int) -> int:
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
                cnt -= 1
                if cnt == 0:
                    return cur.val
                cur = cur.left
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
