> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/4ueAj6/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定循环单调非递减列表中的一个点，写一个函数向这个列表中插入一个新元素  insertVal ，使这个列表仍然是循环升序的。

给定的可以是这个列表中任意一个顶点的指针，并不一定是这个列表中最小元素的指针。

如果有多个满足条件的插入位置，可以选择任意一个位置插入新的值，插入后整个列表仍然保持有序。

如果列表为空（给定的节点是 null），需要创建一个循环有序列表并返回这个节点。否则。请返回原先给定的节点。

### 示例 1：

![](https://assets.leetcode.com/uploads/2019/01/19/example_1_before_65p.jpg)

- 输入：head = [3,4,1], insertVal = 2
- 输出：[3,4,1,2]
- 解释：在上图中，有一个包含三个元素的循环有序列表，你获得值为 3 的节点的指针，我们需要向表中插入元素 2 。新插入的节点应该在 1 和 3 之间，插入之后，整个列表如上图所示，最后返回节点 3 。
  ![](https://assets.leetcode.com/uploads/2019/01/19/example_1_after_65p.jpg)

### 示例 2：

- 输入：head = [], insertVal = 1
- 输出：[1]
- 解释：列表为空（给定的节点是 null），创建一个循环有序列表并返回这个节点。

### 示例 3：

- 输入：head = [1], insertVal = 0
- 输出：[1,0]

### 提示：

- 0 <= Number of Nodes <= 5 \* 10^4
- -10^6 <= Node.val <= 10^6
- -10^6 <= insertVal <= 10^6

## 题目思考

1. 新元素有哪些可能的情况？

## 解决方案

#### 思路

- 分析题目, 待插入的新元素有以下几种情况:
  1. 原链表为空, 则插入新节点并指向自身
  2. 新元素的大小介于原链表某两个节点之间, 则插入到这两个节点之间
  3. 新元素的大小比原链表的最大节点还要大, 或者比最小节点还要小, 则插入到原链表最后一个最大节点之后
- 综上所述, 我们先判断原始链表是否为空, 是的话处理情况 1
- 否则的话遍历链表节点, 并额外维护一个指针, 指向最后一个最大节点
- 如果遍历到某个节点后满足了情况 2, 则直接插入新节点, 并返回原来给定的节点
- 如果遍历完了所有节点仍未返回, 则说明是情况 3, 将新节点插入到最后一个最大节点之后, 并返回原来给定的节点
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 只需要遍历每个节点一次
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def insert(self, head: "Node", insertVal: int) -> "Node":
        if not head:
            # 情况1: 原始链表为空, 新建指向自身的节点并返回
            head = Node(insertVal)
            head.next = head
            return head
        # 维护当前节点和最后最大节点
        cur = head
        mx = head
        while True:
            if cur.val <= insertVal <= cur.next.val:
                # 情况2: 找到插入位置了, 直接插入~
                cur.next = Node(insertVal, cur.next)
                return head
            if cur.val >= mx.val:
                # 更新最大节点, 特别注意需要插入到最后一个最大值之后 (例如1,2,3,3插入4, 如果存的是第一个3, 则错误插入成1,2,3,4,3)
                # 所以更新时相等也要更新最大值, 保证mx指向最后一个最大节点!!!
                mx = cur
            cur = cur.next
            if cur == head:
                # 所有节点都遍历过了, 退出循环
                break
        # 情况3: 此时说明插入数字要么大于所有, 要么小于所有, 统一插入到最后一个最大节点之后~
        mx.next = Node(insertVal, mx.next)
        return head
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
