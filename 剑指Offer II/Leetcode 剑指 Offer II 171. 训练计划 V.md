> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某教练同时带教两位学员，分别以链表 l1、l2 记录了两套核心肌群训练计划，节点值为训练项目编号。两套计划仅有前半部分热身项目不同，后续正式训练项目相同。请设计一个程序找出并返回第一个正式训练项目编号。如果两个链表不存在相交节点，返回 null 。

如下面的两个链表：

![](https://assets.leetcode.cn/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

在节点 c1 开始相交。

### 输入说明：

- intersectVal - 相交的起始节点的值。如果不存在相交节点，这一值为 0

- l1 - 第一个训练计划链表

- l2 - 第二个训练计划链表

- skip1 - 在 l1 中（从头节点开始）跳到交叉节点的节点数

- skip2 - 在 l2 中（从头节点开始）跳到交叉节点的节点数

- 程序将根据这些输入创建链式数据结构，并将两个头节点 head1 和 head2 传递给你的程序。如果程序能够正确返回相交节点，那么你的解决方案将被视作正确答案 。

### 示例 1：

![](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

- 输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
- 输出：Reference of the node with value = 8
- 解释：第一个正式训练项目编号为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

### 示例 2：

![](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

- 输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
- 输出：Reference of the node with value = 2
- 解释：第一个正式训练项目编号为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。

### 示例 3：

![](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

- 输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
- 输出：null
- 解释：两套计划完全不同，返回 null。从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。

### 注意：

- 如果两个链表没有交点，返回 null.
- 在返回结果后，两个链表仍须保持原有的结构。
- 可假定整个链表结构中没有循环。
- 程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
