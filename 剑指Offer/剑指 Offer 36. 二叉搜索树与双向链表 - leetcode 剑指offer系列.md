> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

> 今天继续更新剑指 offer 系列, 这道题同时考察了树和链表, 是一道不错的题目, 这里提供两种思路供大家参考

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

## 题目样例

### 示例

以下面的二叉搜索树为例：

![](https://assets.leetcode.com/uploads/2018/10/12/bstdlloriginalbst.png)

我们希望将这个二叉搜索树转化为双向循环链表。链表中的每个节点都有一个前驱和后继指针。对于双向循环链表，第一个节点的前驱是最后一个节点，最后一个节点的后继是第一个节点。

下图展示了上面的二叉搜索树转化成的链表。“head” 表示指向链表中有最小元素的节点。

![](https://assets.leetcode.com/uploads/2018/10/12/bstdllreturndll.png)

特别地，我们希望可以就地完成转换操作。当转化完成以后，树中节点的左指针需要指向前驱，树中节点的右指针需要指向后继。还需要返回链表中的第一个节点的指针。

## 题目思考

1. 如何确定当前节点在链表中的左右节点?

## 解决方案

### 方案 1

#### 思路

- 根据题目描述, 一个比较容易想到的思路是分治法, 就是先将左右子树转换成双向链表, 然后再调节当前根节点的指向
- 双向链表需要知道其头和尾, 所以递归的返回值就是转换后的链表头和尾, 这样根节点左边就指向左子树对应链表的尾 ltail, 右边就指向右子树对应链表的头 rhead, 然后返回左边的头 lhead 和右边的尾 rtail, 自然就是当前的树转换后的链表头和尾了
- 注意如果当前根节点某一个子树不存在, 比如根节点只有左子树, 那么根节点右指针就不需要额外指向了, 且根节点就是对应的双向链表的尾. 而如果左右子树都不存在的话, 根节点本身就是对应的双向链表的头和尾. 这里可以将左右子树的头尾都初始化为 root 来实现 (具体参考下面的代码部分)
- 而递归出口自然就是节点为空的情况, 此时对应的链表头和尾都是空
- 这样递归调用, 最后返回的就是整个树转换成的链表的头和尾, 又因为题目要求是循环链表, 所以最后只需要把头尾相连即可
- 下面的代码对关键步骤都有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N)`
  - 递归栈的空间消耗

#### 代码

```python
class Solution:
    def treeToDoublyList(self, root: 'Node') -> 'Node':
        if not root:
            return None

        def getDoublyList(root):
            if not root:
                # 空节点对应的链表头尾也是空
                return (root, root)
            # 初始化左右子树对应链表的头和尾
            lhead, ltail, rhead, rtail = root, root, root, root
            if root.left:
                # 左子树存在时, 需要将根节点与左子树链表结尾相连
                lhead, ltail = getDoublyList(root.left)
                root.left = ltail
                ltail.right = root
            if root.right:
                # 右子树存在时, 需要将根节点与右子树链表开头相连
                rhead, rtail = getDoublyList(root.right)
                root.right = rhead
                rhead.left = root
            # 最后左链表开头和右链表结尾就是当前树转换的链表的头和尾
            return (lhead, rtail)

        # 将整个树转换的链表的头和尾相连, 形成循环链表
        head, tail = getDoublyList(root)
        head.left = tail
        tail.right = head
        return head
```

### 方案 2

#### 思路

- 这次我们从另外一个角度分析这个问题
- 二叉搜索树的性质是中序遍历有序, 而最终形成的双向链表也是排好序的
- 所以我们可以直接利用中序遍历, 只需要额外存储一个 pre 节点, 然后遍历到当前节点时, 就将其和 pre 节点相连即可
- 另外遍历过程中要找到转换的链表的头和尾, 最后也要把它们也连起来从而形成循环链表
- 下面的代码对关键步骤都有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N)`
  - 递归栈的空间消耗

#### 代码

```python
class Solution:
    def treeToDoublyList(self, root: 'Node') -> 'Node':
        if not root:
            return None
        self.pre = None

        def inorder(node):
            if not node:
                return
            inorder(node.left)
            if not self.pre:
                # 说明当前遍历到第一个节点, 也是最小的节点, 就是链表头
                self.head = node
            else:
                # 将pre和node连起来
                self.pre.right = node
                node.left = self.pre
            # 更新pre为当前node
            self.pre = node
            inorder(node.right)

        inorder(root)
        # 注意遍历结束的时候的pre就是最后一个节点, 即链表尾, 需要和之前保存的链表头连起来, 形成循环链表
        self.head.left, self.pre.right = self.pre, self.head
        return self.head
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
