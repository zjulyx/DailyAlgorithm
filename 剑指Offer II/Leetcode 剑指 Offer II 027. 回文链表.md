> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/aMhZSa)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个链表的 头节点 head ，请判断其是否为回文链表。

如果一个链表是回文，那么链表节点序列从前往后看和从后往前看是相同的。

### 示例 1：

![](https://pic.leetcode-cn.com/1626421737-LjXceN-image.png)

- 输入: head = [1,2,3,3,2,1]
- 输出: true

### 示例 2：

![](https://pic.leetcode-cn.com/1626422231-wgvnWh-image.png)

- 输入: head = [1,2]
- 输出: false

### 提示：

- 链表 L 的长度范围为 [1, 10^5]
- 0 <= node.val <= 9

## 题目思考

1. 能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

## 解决方案

#### 思路

- 相比传统的数组或者字符串求回文, 链表不能直接取下标, 也无法像双向链表那样用双指针往中间靠拢比较的方法, 如何做到 O(n) 时间复杂度和 O(1) 空间复杂度呢?
- 分析双指针往中间靠拢比较的原理, 它是第一个和最后一个比较, 第二个和倒数第二个比较, 以此类推...
- 所以我们可以人为将链表分成左右两半部分, 然后将右半部分进行反转, 再用双指针从两部分的头节点依次往后比较即可
- 这样就把问题分解成了 3 个经典的小问题, 它们都可以利用双指针来解决, 大家可以结合下面的代码和注释进行理解
- 如果题目要求恢复原来链表结构的话, 还可以将链表翻转部分提取出来, 然后比较完之后再将右半部分转回去, 下面代码也实现了这一步

#### 复杂度

- 时间复杂度 O(N): 只需要遍历每个节点三遍(划分/翻转/比较)
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        # 把右半部分翻转后再双指针依次比较, 最后将输入链表结构恢复成原样
        # 第一步: 使用快慢指针找中点
        fast = head
        slow = head
        while fast:
            fast = fast.next
            if fast:
                fast = fast.next
            slow = slow.next
        # 此时slow就是右半部分的头节点(左半部分长度可能相等或多1, 对应总长度是偶数和奇数的情况)
        # 第二步: 定义翻转函数, 将右半部分进行翻转
        def reverse(cur):
            # 翻转并返回新的头
            pre = None
            while cur:
                nex = cur.next
                cur.next = pre
                pre, cur = cur, nex
            return pre

        # 第三步: 双指针依次比较左右两部分对应节点, 并记录右半部分末尾节点用于恢复原始结构
        left, right = head, reverse(slow)
        rightHead = right
        res = True
        # 注意右半部分长度<=左半部分, 所以一定是right先没
        while right:
            if left.val != right.val:
                res = False
                break
            left = left.next
            right = right.next
        # 翻转右半部分开头恢复原始链表结构(注意它可能不存在, 如果当原始链表长度小于等于1时)
        reverse(rightHead)
        return res
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
