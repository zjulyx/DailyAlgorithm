> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/LGjMqU)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个单链表 L 的头节点 head ，单链表 L 表示为：

L0 → L1 → … → Ln-1 → Ln 
请将其重新排列后变为：

L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → …

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

### 示例 1:

![](https://pic.leetcode-cn.com/1626420311-PkUiGI-image.png)

- 输入: head = [1,2,3,4]
- 输出: [1,4,2,3]

### 示例 2:

![](https://pic.leetcode-cn.com/1626420320-YUiulT-image.png)

- 输入: head = [1,2,3,4,5]
- 输出: [1,5,2,4,3]

### 提示：

- 链表的长度范围为 [1, 5 * 10^4]
- 1 <= node.val <= 1000

## 题目思考

1. 如何做到 O(1) 空间复杂度？

## 解决方案

#### 思路

- 分析题目, 最容易想到的做法就是把链表节点用数组存储, 然后使用头尾双指针交替指向对方
- 不过这种做法的空间复杂度是 O(N), 如何优化呢?
- 注意题目重排的方式是 1->N, 2->N-1, 以此类推, 相当于左半部分和翻转的右半部分进行合并
- 所以我们可以分为以下三步实现:

1. 将链表分为左右两半部分, 并断开之间的连接, 这里可以通过快慢指针的方式找到中点:
   1. 快指针每次移动两步, 慢指针每次移动一步
   2. 这样快指针变成空的时候, 慢指针就到了中点, 即左半部分末尾
   3. 慢指针的下个节点就是右半部分开头, 保存它, 并断开慢指针和它的连接
2. 然后对右半部分进行翻转, 这里可以使用双指针做到, 具体可以参考之前的文章: [024 TODO]
3. 最后再将两部分交替合并即可, 这里同样使用双指针来进行合并:
   1. 保存左右指针的下个节点
   2. 左指针指向右指针
   3. 右指针指向左指针下个节点
   4. 左右指针移动到先前保存的各自下个节点的位置

- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 每步操作都只需要遍历每个节点一次
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def reorderList(self, head: ListNode) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        # 快慢指针找中点+右半部分翻转+链表合并
        if not head:
            return None
        # 第一步: 快慢指针找中点
        # 注意长度是偶数的话中点使用左半部分最后一个节点
        fast = head.next
        slow = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        # 第二步: 右半部分翻转
        # 此时slow是左半部分的尾节点
        # 先断开左右部分的连接
        cur = slow.next
        slow.next = None
        # 然后进行经典链表翻转
        pre = None
        while cur:
            nex = cur.next
            cur.next = pre
            pre, cur = cur, nex
        # 第三步: 链表合并
        # 此时pre是右半部分翻转后的头节点
        # 使用双指针依次指向对方下一个节点即可
        l, r = head, pre
        while l and r:
            nl = l.next
            nr = r.next
            l.next = r
            r.next = nl
            l = nl
            r = nr
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
