> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

> 今天继续更新剑指 offer 系列, 这道题是比较基础的链表问题, 适合入门同学. 这里同样提供多种方案还有进阶问题供大家参考~

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

题目保证链表中节点的值互不相同

若使用 C 或 C++ 语言，你不需要 free 或 delete 被删除的节点

## 题目样例

### 示例

#### 输入

head = [4,5,1,9], val = 5

#### 输出

[4,1,9]

#### 解释

给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.

## 题目思考

> (思考题答案在最后)

1. 进阶问题: 如果节点的值存在重复需要怎么做?

## 解决方案

### 思路

#### 分析

- 要想删除链表中的节点, 我们必须知道它的前一个节点, 然后将其前一个节点的 next 值指向当前节点的 next 即可
- 所以直接使用两个指针, 一个保存前一个节点 pre, 一个保存当前节点 cur, 遇到当前节点值是输入值的时候按照上面做法删除即可. (大多数链表问题都可以先尝试使用多个指针的方案, 画图模拟整个过程更容易理解)
- 注意题目中节点没有重复, 所以删除之后直接跳出循环即可
- 这里提供两种实现方案, 一种更好理解, 一种代码更简短, 具体见下面的实现和代码部分

#### 实现

1. 方案 1:
   1. 特殊处理 `head`, 如果删除值是 `head` 直接返回 `head`;
   2. 否则把 `head` 作为第一个 `pre`, `head.next` 作为第一个 `cur`, 依次遍历找删除节点
2. 方案 2:
   1. 增加一个哨兵节点 `prehead`, 其 `next` 值是原始 `head`
   2. 然后把 `prehead` 作为 `pre`, `head` 作为第一个 `cur`, 这样就不需要特殊处理 `head` 了, 把它当成普通节点处理即可
   3. 最后返回 `prehead.next` 就是新的 `head`

### 复杂度

- 时间复杂度 `O(N)`
  - 最差情况需要遍历整个链表的所有节点
- 空间复杂度 `O(1)`
  - 只使用了几个变量

### 代码

#### 方案 1 - 特殊处理 head

```python
class Solution:
    def deleteNode(self, head: ListNode, val: int) -> ListNode:
        # 需要考虑head不存在或者删除节点就是head的情况
        if not head:
            return head
        if head.val == val:
            return head.next
        pre = head
        cur = head.next
        while cur:
            if cur.val == val:
                # 找到删除节点了, 直接将pre的next指向cur的next就完成删除操作
                pre.next = cur.next
                break
            # 更新pre和cur, 依次后移一个节点
            pre = cur
            cur = cur.next
        return head
```

#### 方案 2 - 增加哨兵节点

```python
class Solution:
    def deleteNode(self, head: ListNode, val: int) -> ListNode:
        prehead = ListNode(0)
        prehead.next = head
        cur, pre = head, prehead
        while cur:
            if cur.val == val:
                # 找到删除节点了, 直接将pre的next指向cur的next就完成删除操作
                pre.next = cur.next
                break
            # 更新pre和cur, 依次后移一个节点
            pre = cur
            cur = cur.next
        return prehead.next
```

## 思考题答案

1. 进阶问题: 如果节点的值存在重复需要怎么做?

   1. 对方案 2 稍加改造即可: 找到删除节点后不跳出循环, 而是在更新 `pre` 的 `next` 之后, **保持 pre 不变** (因为有可能存在连续多个待删除节点, 这样保持 `pre` 不变才能将其指向要一个不会被删除的节点), 而将 `cur` 继续后移; 而没找到时再移动 `pre` 和 `cur`
   2. 由于两个条件都需要后移`cur`, 所以可以将其提取到 if 条件之外, 简化代码
   3. 这道题也即[203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/), 具体代码如下:

```python
class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        prehead = ListNode(0)
        prehead.next = head
        cur, pre = head, prehead
        while cur:
            if cur.val == val:
                # 找到删除节点了, 直接将pre的next指向cur的next就完成删除操作
                pre.next = cur.next
            else:
                # 当前cur不是要删除节点, 更新pre, 后移一个节点
                pre = cur
            # 由于两个条件都需要后移cur, 这里可以将其提取出来
            cur = cur.next
        return prehead.next
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
