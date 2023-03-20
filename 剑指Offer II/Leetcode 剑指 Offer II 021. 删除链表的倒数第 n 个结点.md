> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/SLwz0R)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

![](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

### 示例 1：

输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]

### 示例 2：

输入：head = [1], n = 1
输出：[]

### 示例 3：

输入：head = [1,2], n = 1
输出：[1]

### 提示：

- 链表中结点的数目为 sz
- 1 <= sz <= 30
- 0 <= Node.val <= 100
- 1 <= n <= sz

- 进阶：能尝试使用一趟扫描实现吗？

## 题目思考

1. 如何使用一趟扫描实现?

## 解决方案

#### 思路

- 分析题目, 最容易想到的做法是先统计链表的节点总数, 然后将它减去 n 就得到了待删除的节点的正数下标, 最后遍历找到该节点的前一节点并将其指向待删除节点的后一节点即可
- 但这样会扫描两遍链表 (先统计个数, 再找待删除节点), 如何优化呢?
- 我们可以反其道而行之, 使用双指针且保证两者距离为 n, 那么右边的指针指向空时, 左边的指针就指向了倒数第 n 个节点
- 具体做法如下:
  1. 快指针先正向遍历到第 n 个节点
  2. 慢指针初始化为哨兵节点(指向原始头节点)
  3. 然后快慢指针同时向右遍历, 直到快指针变成空
- 此时显然慢指针指向的正是待删除节点的前一节点, 将它指向待删除节点的后一节点即可
- 通过上述过程, 我们只需要一趟扫描就删除了对应节点
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 只需要一趟扫描所有节点
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        # 快慢指针
        # 快指针先右移n步
        fast = head
        for _ in range(n):
            fast = fast.next
        # 慢指针初始化为哨兵节点
        dummy = ListNode(0, head)
        slow = dummy
        # 之后快慢指针以相同速度右移, 直到快指针为空
        while fast:
            slow = slow.next
            fast = fast.next
        # 此时慢指针指向的节点即为待删除节点的前一节点, 将其next更新成待删除节点的next即可
        slow.next = slow.next.next
        # 哨兵节点的next即为删除后的头节点, 因为原来的头节点可能会被删除
        return dummy.next
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
