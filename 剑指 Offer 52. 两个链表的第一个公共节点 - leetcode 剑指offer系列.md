> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入两个链表，找出它们的第一个公共节点。

- 如果两个链表没有交点，返回 null.
- 在返回结果后，两个链表仍须保持原有的结构。
- 可假定整个链表结构中没有循环。
- 程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

## 题目样例

### 示例

![](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

- 输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
- 输出：Reference of the node with value = 8
- 输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

## 题目思考

1. 如何做到仅用 O(1) 内存得出结果?

## 解决方案

#### 思路

- 一个比较容易想到的思路是先遍历其中一个链表, 将节点存入集合中, 然后遍历另一个链表, 查看节点是否存在于集合中, 存在则说明找到. 这样虽然时间复杂度是 O(N), 但空间复杂度也是 O(N), 不满足题目要求
- 重新观察题目示例, 假设链表 A 和 B 的共享部分长度为 shared, 各自前面独占部分长度为 a 和 b, 那么 A 的总长度为`a+shared`, B 的总长度为`b+shared`
- 根据交换律, A+B 的长度等于 B+A 的长度, 也即`a+shared+b+shared == b+shared+a+shared`
- 根据上面这个式子, 我们可以定义两个指针, 分别从 A 和 B 的开头出发, 达到终点后换成另一个链表的起点继续走: 如果 A 和 B 有交点的话, 很显然两个指针会在交点处碰面, 共同走完剩余的 shared 部分; 而如果没交点的话, 两个指针只可能共同走到最后的空节点, 此时就返回 null
- **特别注意一点, 我们遍历到最后一个节点的时候, 不能直接跳到开头, 而是应该先到末尾后面的空节点, 然后再跳到另一个链表的开头.** 因为如果不进入空节点的话, 对于没有交点的情况就永远不可能跳出循环了(永远走不到末尾之后的空节点, 两个指针对应节点永远不可能相等)

#### 复杂度

- 时间复杂度 O(N): 只需要遍历两遍
- 空间复杂度 O(1): 只定义了几个变量

#### 代码

```python
class Solution:
    def getIntersectionNode(self, headA: ListNode,
                            headB: ListNode) -> ListNode:
        a, b = headA, headB
        while a != b:
            # a如果到达A的末尾之后的空节点, 就置为B的起点重新遍历
            a = headB if not a else a.next
            # b如果到达B的末尾之后的空节点, 就置为A的起点重新遍历
            b = headA if not b else b.next
        return a
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
