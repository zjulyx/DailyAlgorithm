> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个头节点为 head 的单链表用于记录一系列核心肌群训练编号，请将该系列训练编号 倒序 记录于链表并返回。

### 示例 1：

- 输入：head = [1,2,3,4,5]
- 输出：[5,4,3,2,1]

### 示例 2：

- 输入：head = [1,2]
- 输出：[2,1]

### 示例 3：

- 输入：head = []
- 输出：[]

### 提示：

- 链表中节点的数目范围是 [0, 5000]
- -5000 <= Node.val <= 5000

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 首先考虑迭代做法, 要反转链表, 我们至少需要两个指针才能做到, 否则反转的时候不知道应该指向哪里
- 所以保存前一个节点 pre 和当前节点 cur, 并记录 cur 的下一个节点 nex, 每次都将当前节点指向前一个节点, 然后 pre 和 cur 都往后移动一位即可 (即 pre = cur, cur = nex)
- 注意初始时 pre 置为 None, 因为 head 没有前一个节点

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍链表
- 空间复杂度 `O(1)`
  - 只需要常数个变量

#### 代码

```python
class Solution:
    def trainningPlan(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 方法1: 迭代, 双指针
        # head的前一个节点是None
        pre, cur = None, head
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
    def trainningPlan(self, head: Optional[ListNode]) -> Optional[ListNode]:
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
    def trainningPlan(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 方法3: 递归版本2 - 只需要返回反转后的链表头
        if not head or not head.next:
            # 空节点或者只有自身一个节点, 直接返回自身即可
            return head
        nextHead = self.trainningPlan(head.next)
        # head.next就是方法2的nextTail
        head.next.next = head
        # 注意此处必须先反转指向, 再将head.next置为空
        head.next = None
        # 新的链表头仍为原来的nextHead
        return nextHead
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
