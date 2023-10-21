> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/NYBBNL/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给你一棵二叉搜索树，请 按中序遍历 将其重新排列为一棵递增顺序搜索树，使树中最左边的节点成为树的根节点，并且每个节点没有左子节点，只有一个右子节点。

### 示例 1：

![](https://assets.leetcode.com/uploads/2020/11/17/ex1.jpg)

- 输入：root = [5,3,6,2,4,null,8,1,null,null,null,7,9]
- 输出：[1,null,2,null,3,null,4,null,5,null,6,null,7,null,8,null,9]

### 示例 2：

![](https://assets.leetcode.com/uploads/2020/11/17/ex2.jpg)

- 输入：root = [5,1,7]
- 输出：[1,null,5,null,7]

### 提示：

- 树中节点数的取值范围是 [1, 100]
- 0 <= Node.val <= 1000

## 题目思考

1. 如何降低时间和空间复杂度?

## 解决方案

#### 思路

- 根据题目描述, 一个很容易想到的思路就是中序遍历, 然后将遍历到的节点用列表存下来, 最后遍历列表, 并依次将每个节点的右子节点指向列表里的下一个节点, 当然也要把左子节点置为空
- 不过这样我们就得遍历每个节点两遍, 而且需要额外的列表来存储节点, 有没有更好的方法呢?
- 回顾中序遍历的性质, 遍历到的节点本身就是有序的, 所以如果我们保存了前一个节点, 那么在遍历当前节点时, 只需要将前一个节点的右子节点指向它即可, 然后将前一节点更新为当前节点, 以此类推, 这样最终中序遍历完成时, 就得到了题目要求的递增顺序搜索树
- 当然, 我们在遍历到当前节点时, 也需要将其左子节点置为空, 这里之所以将当前节点而不是前一节点的左子节点置为空, 是因为这样才能保证最终所有节点的左子节点都是空, 否则最后一个节点的左子节点就可能不是空, 会导致出现循环
- 利用上述做法, 我们就无需引入额外的列表存储, 也不需要再次遍历了
- 另外在遍历第一个节点时, 由于它没有前一节点, 所以我们可以额外引入一个哨兵节点, 并将最开始的前一节点初始化为哨兵, 这样哨兵的右子节点就是最终形成的树的根, 返回它即可
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 每个节点只会被遍历一次
- 空间复杂度 O(H): 递归调用最多使用 O(H) 栈空间, H 是树的高度

#### 代码

```python
class Solution:
    def increasingBST(self, root: TreeNode) -> TreeNode:
        # 使用哨兵节点, 作为最开始的前一节点
        dummy = TreeNode()
        pre = dummy

        def inorder(node):
            # 中序遍历
            nonlocal pre
            if not node:
                return
            print(node.val)
            inorder(node.left)
            # 将前一节点的右子节点指向当前节点
            pre.right = node
            # 更新前一节点为当前节点
            pre = node
            # 将当前节点的左子节点置为空
            node.left = None
            inorder(node.right)

        inorder(root)
        return dummy.right
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
