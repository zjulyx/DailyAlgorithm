> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

> 今天继续更新剑指 offer 系列, 这道题很经典, 出现频率也非常高, 这里提供**一种迭代和两种递归**方法帮助大家拓展思路

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

0 <= 节点个数 <= 5000

## 题目样例

### 示例

#### 输入

1->2->3->4->5->NULL

#### 输出

5->4->3->2->1->NULL

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 首先考虑迭代做法, 要反转链表, 我们至少需要两个指针才能做到, 否则反转的时候不知道应该指向哪里
- 所以保存前一个节点 pre 和当前节点 cur, 并记录 cur 的下一个节点 nex, 每次都将当前节点指向前一个节点, 然后 pre 和 cur 都往后移动一位即可 (即 pre = cur, cur = nex)
- 注意边界条件: 没有节点的情况, 以及对 head 的 next 的处理

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍链表
- 空间复杂度 `O(1)`
  - 只需要常数个变量

#### 代码

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        # 方法1: 迭代, 双指针
        if not head:
            # 链表为空的话直接返回空
            return head
        pre, cur = head, head.next
        # 首先head变成了翻转后的末尾, 所以其next要置为空
        head.next = None
        while cur:
            # 先存下cur的下一个节点
            nex = cur.next
            # cur的下一个节点指向pre, 完成当前节点指向的反转
            cur.next = pre
            # 更新pre和cur, 分别按照原链表顺序往后移动一位
            pre, cur = cur, nex
        # 最终cur就是空, 而pre则是反转后的开头节点
        return pre
```

### 方案 2

#### 思路

- 接下来我们考虑如何用递归来做这道题
- 如果我们能够得到当前节点之后的节点反转后的链表, 那么只需要将新链表的尾指向当前节点, 同时将当前节点指向空, 就完成了对当前节点的反转
- 所以额外定义一个方法, 传入当前节点, 返回一个二元组: (反转后的链表头, 反转后的链表尾), 然后按照上述操作即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要访问一次
- 空间复杂度 `O(N)`
  - 递归的栈的消耗

#### 代码

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        # 方法2: 递归版本1 - 返回反转后的头和尾
        def reverse(head):
            if not head:
                # 空节点, 反转后的头和尾仍为空
                return (None, None)
            if not head.next:
                # 只有单独一个节点, 反转后的头和尾仍为自身
                return (head, head)
            nextHead, nextTail = reverse(head.next)
            # 将nextTail指向head, head指向空即可
            head.next = None
            nextTail.next = head
            # 新的链表头仍为原来的nextHead, 尾则变了, 变成head
            return (nextHead, head)

        # 最后返回反转链表的头即可
        return reverse(head)[0]
```

### 方案 3

#### 思路

- 回顾递归方案 2, 我们不仅需要返回反转后的链表头, 也需要返回链表尾. 如果可以只返回头, 就不需要额外定义新方法了
- 注意反转的过程, 当前节点的 next 在之后节点反转结束后对应的就是反转链表的结尾, 也即方案 2 的 nextTail, 这样我们就可以利用这一性质去掉链表尾这一返回值了
- 该方案是基于方案 2 优化推导得来的, 没有方案 2 直观, 理解起来也有一些难度, 但胜在代码更简洁

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要访问一次
- 空间复杂度 `O(N)`
  - 递归的栈的消耗

#### 代码

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        # 方法3: 递归版本2 - 只需要返回反转后的链表头
        if not head or not head.next:
            # 空节点或者只有自身一个节点, 直接返回自身即可
            return head
        nextHead = self.reverseList(head.next)
        # head.next就是方法2的nextTail
        head.next.next = head
        # 注意此处必须先反转指向, 再将head.next置为空
        head.next = None
        # 新的链表头仍为原来的nextHead
        return nextHead
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
