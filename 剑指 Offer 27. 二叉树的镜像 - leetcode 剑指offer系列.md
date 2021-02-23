> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

> 今天继续更新剑指 offer 系列, 这道题的递归做法很容易想到, 这里额外提供一种迭代方法帮助大家拓展思路

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入：

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

镜像输出：

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

0 <= 节点个数 <= 1000

## 题目样例

### 示例

#### 输入

root = [4,2,7,1,3,6,9]

#### 输出

[4,7,2,9,6,3,1]

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
    def mirrorTree(self, root: TreeNode) -> TreeNode:
        # 方法1: 递归交换左右子节点
        if not root:
            return root
        left, right = self.mirrorTree(root.left), self.mirrorTree(root.right)
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
    def mirrorTree(self, root: TreeNode) -> TreeNode:
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
        # 最终镜像的根节点仍为roott
        return root
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
