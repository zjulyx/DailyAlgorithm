> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/er-cha-shu-de-jing-xiang-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一棵二叉树的根节点 root，请左右翻转这棵二叉树，并返回其根节点。

### 示例 1：

![](https://pic.leetcode.cn/1694686821-qlvjod-%E7%BF%BB%E8%BD%AC%E4%BA%8C%E5%8F%89%E6%A0%91.png)

- 输入：root = [5,7,9,8,3,2,4]
- 输出：[5,9,7,4,2,3,8]

### 提示：

- 树中节点数目范围在 [0, 100] 内
- -100 <= Node.val <= 100

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 要得到镜像, 如果我们已经得到了当前节点两个子节点各自的镜像, 那么只需要交换子节点位置即可, 这就自然引出了递归的思路, 将大问题化为小问题来解决
- 首先定义递归出口
  - 如果节点为空, 其镜像也为空
- 然后写递归内部调用逻辑
  - 先得到当前节点的两个子节点递归调用后的结果 left 和 right, 此时 left 就是左子树的镜像, 而 right 就是右子树的镜像
  - 然后将当前节点的 left 指向 right, right 指向 left 即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 所有节点只需要遍历一遍
- 空间复杂度 `O(N)`
  - 递归栈的消耗

#### 代码

```python
class Solution:
    def flipTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # 方法1: 递归交换左右子节点
        if not root:
            return root
        left, right = self.flipTree(root.left), self.flipTree(root.right)
        root.left = right
        root.right = left
        return root
```

### 方案 2

#### 思路

- 接下来尝试用迭代的思路来解决
- 重新分析题目并观察例子, 我们可以发现其实镜像说白了就是每个节点都交换其直接左右子节点
- 所以我们如果能保证每个节点都恰好交换其子节点一次, 那么操作结束后得到的树就是镜像
- 一个很自然的想法就是类似 BFS 的思路, 使用一个队列存储当前访问到的节点
- 然后对于当前节点, 交换其左右子节点, 然后把非空子节点加入队列等待后面的交换即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 所有节点只需要遍历一遍
- 空间复杂度 `O(N)`
  - 辅助队列需要存 N 个节点

#### 代码

```python
class Solution:
    def flipTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # 方法2: 迭代, 使用一个队列存储当前的节点
        if not root:
            return None
        q = [root]
        for node in q:
            # 交换当前节点的左右子节点
            node.left, node.right = node.right, node.left
            # 如果子节点不为空的话, 将其加入队列, 等待后续处理
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        # 最终镜像的根节点仍为root
        return root
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
