> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

> 今天继续更新剑指 offer 系列, 这道题很经典, 也有多种方法, 这里提供三种做法帮助大家拓展思路

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个链表，输出该链表中倒数第 k 个节点。为了符合大多数人的习惯，本题从 1 开始计数，即链表的尾节点是倒数第 1 个节点。例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。

## 题目样例

### 示例

#### 输入

给定一个链表: 1->2->3->4->5, 和 k = 2.

#### 输出

4 -> 5

## 题目思考

1. 可以一次遍历搞定吗?
2. 可以不使用额外空间吗?

## 解决方案

### 方案 1

#### 思路

- 最直接的思路, 直接保存每个节点到数组中, 然后下标 `n-k` 的节点即为所求

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍链表
- 空间复杂度 `O(N)`
  - 额外需要一个数组存 N 个节点

#### 代码

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        # 方法1: 数组存储每个节点
        arr = []
        while head:
            arr.append(head)
            head= head.next
        return arr[len(arr)-k]
```

### 方案 2

#### 思路

- 如果我们能得到链表节点数目 `n`, 那么倒数第 `k` 个节点就是正数第 `n-k+1` 个节点 (从 1 开始计数)
- 利用这个性质, 我们先遍历一遍链表, 得到数目 `n`
- 然后再遍历前 `n-k+1` 个节点即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历两遍链表
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        # 方法2: 计数然后正向遍历
        n = 0
        cur = head
        while cur:
            n += 1
            cur = cur.next
        cur = head
        for i in range(n - k):
            cur = cur.next
        return cur
```

### 方案 3

#### 思路

- 回顾前两种方案, 要么使用了额外空间, 要么遍历了多次, 有没有一次遍历又不需要使用额外空间的方法呢?
- 答案也是有的, 需要求倒数第 k 个节点, 我们完全可以定义两个指针, 保证两个指针距离为 k, 这样当后面的指针到达结尾时, 前面的指针正好就在倒数第 k 个节点处
- 要保证两个指针距离为 k, 我们只需要让后面的指针先走 k 步 (快指针), 然后前面的指针再从开头出发 (慢指针), 两个指针按照每次 1 步的节奏继续同时往下走即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个数字只需要被遍历一遍
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        # 双指针快慢节点
        # 快指针先走k步, 然后慢指针再开始走, 这样能保证两者距离一直为k, 那么当快指针走到结尾时满指针就恰好停在倒数第k个节点
        fast = head
        for i in range(k):
            if fast:
                fast = fast.next
        slow = head
        while fast:
            slow = slow.next
            fast = fast.next
        return slow
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
