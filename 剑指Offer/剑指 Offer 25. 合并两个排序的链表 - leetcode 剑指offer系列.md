> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

> 今天继续更新剑指 offer 系列, 这道题同样很经典, 这里提供两种做法帮助大家拓展思路

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

- 0 <= 链表长度 <= 1000

## 题目样例

### 示例

#### 输入

1->2->4, 1->3->4

#### 输出

1->1->2->3->4->4

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 一个很自然的思路就是采用类似归并排序的做法, 只是把原算法中数组下标转换成这里的链表指针即可
- 循环结束条件是两个指针都指向空, 只要有一个不为空, 那就继续循环, 追加到当前节点中即可
- 这里额外引入一个哨兵节点 prehead, 用于统一各种边界条件, 例如 l1 和 l2 都不存在的情况, 从而简化代码 (哨兵节点思路有点类似之前的文章 `剑指 Offer 18. 删除链表的节点 - leetcode 剑指offer系列`, 感兴趣的同学可以在历史记录里看下那篇)
- 下面代码中对各个步骤有更为详细的解释, 这个也是归并排序的一个比较通用的模板了, 希望大家都能掌握它, 很多问题都可以基于它进行一些改动来解决

#### 复杂度

- 时间复杂度 `O(M+N)`
  - 两个链表的每个节点都只需要访问一次
- 空间复杂度 `O(1)`
  - 只使用了常数个变量

#### 代码

```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        # 方法1: 迭代, 哨兵节点, 归并排序思路
        prehead = ListNode(0)
        cur = prehead
        while l1 or l2:
            if not l1 or (l2 and l2.val <= l1.val):
                # 该情况要么是只有l2节点, 要么l2节点值更小, 当前的下个节点只能是l2
                cur.next = l2
                l2 = l2.next
            else:
                # 否则的话当前的下个节点只能是l1
                cur.next = l1
                l1 = l1.next
            cur = cur.next
        # 哨兵节点的next就是合并后的链表头
        return prehead.next
```

### 方案 2

#### 思路

- 接下来我们考虑如何用递归来做这道题
- 我们先尝试能否直接使用题目给的方法作为递归方法本身, 也即传入两个节点, 返回它们合并后的链表的头
- 首先定义递归出口
  - 自然就是 l1 或者 l2 不存在的情况, 这时候就返回另一个指针即可, 因为任何链表和空链表合并都是它自身
- 然后写递归内部调用逻辑
  - 当 l1 和 l2 都存在的时候, 就要判断其头节点的大小关系了
  - 小的那个节点(这里记为 small)可以进行递归调用, 传入其下个节点 (small.next) 和另一个节点(这里记为 big), 从而得到两者合并后的链表头
  - 根据递归方法本身定义, 这里合并后的链表就是有序的, 那么 small 只需要指向该链表头即可, 这样整个链表仍然有序
  - 最终 small 也正好就是递归的返回值, 也即 small 和 big 合并后的链表头
- 以上两步就完成了整个递归算法的设计
- 下面代码中对各个步骤有更为详细的解释

#### 复杂度

- 时间复杂度 `O(M+N)`
  - 两个链表的每个节点都只需要访问一次
- 空间复杂度 `O(M+N)`
  - 递归的栈的消耗

#### 代码

```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        # 方法2: 递归返回当前两个节点合并后的链表的头节点
        if not l1:
            # 没有l1, 直接返回l2即可
            return l2
        if not l2:
            # 没有l2, 直接返回l1即可
            return l1
        # 此时说明l1和l2都存在, 判断它们的值
        if l1.val <= l2.val:
            # l1更小, 说明合并链表的头是l1, 头的下一个节点自然就是将l1后移一位递归调用后的结果
            l1.next = self.mergeTwoLists(l1.next, l2)
            return l1
        else:
            # 同理此时l2是头, 将l2后移一位递归调用
            l2.next = self.mergeTwoLists(l1, l2.next)
            return l2
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
