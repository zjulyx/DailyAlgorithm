> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/lMSNwu)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个 非空链表 l1 和 l2  来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

可以假设除了数字 0 之外，这两个数字都不会以零开头。

### 示例 1：

![](https://pic.leetcode-cn.com/1626420025-fZfzMX-image.png)

- 输入：l1 = [7,2,4,3], l2 = [5,6,4]
- 输出：[7,8,0,7]

### 示例 2：

- 输入：l1 = [2,4,3], l2 = [5,6,4]
- 输出：[8,0,7]

### 示例 3：

- 输入：l1 = [0], l2 = [0]
- 输出：[0]

### 提示：

- 链表的长度范围为 [1, 100]
- 0 <= node.val <= 9
- 输入数据保证链表代表的数字无前导 0

## 题目思考

1. 如果输入链表不能修改(不能对链表中的节点进行翻转)该如何处理？

## 解决方案

#### 思路

- 分析题目, 最容易想到的做法就是先翻转两个链表, 然后就转换成了这道题目: [程序员面试金典 - 面试题 02.05. 链表求和](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484189&idx=1&sn=98cd9f3b63e45e8c595b8b2de598b539&chksm=90028410a7750d06efd783a8a07db632ea9c765061fc7bf3e69f54ac201dd499918233c3b25e&token=1686780854&lang=zh_CN#rd)
- 我在那篇文章里也写了具体的方案和代码, 大家可以参考一下
- 不过这道题多了个进阶要求, 不能对链表中的节点进行翻转, 这该如何做呢?
- 回顾我们学过的数据结构, 不难发现"栈"可以满足这里的需求
- 我们可以先将两个链表的节点值压入两个栈中, 由于栈是先进后出的, 栈顶一定是两个数字的最低位
- 然后我们进行逐位相加:
  - 先维护当前进位(初始化为 0)和链表节点(初始化为空)
  - 然后不断弹出两个栈的栈顶, 如果某个栈已经空了则用 0 代替, 将它们加上当前进位进行求和
  - 将和除以 10, 商就是新的进位, 而余数则是当前位的值
  - 使用余数创建新的节点, 并指向上一个节点, 然后更新当前链表节点
- 最终直到两个栈都是空, 且进位为 0, 则结束循环

#### 复杂度

- 时间复杂度 O(M+N): M 和 N 是两个链表的长度, 每个节点都需要遍历一遍
- 空间复杂度 O(M+N): 两个栈需要存储所有节点的值

#### 代码

```python
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        # 先创建两个栈, 分别将两个链表的节点值存进去
        stacks = [[], []]
        for i, node in enumerate([l1, l2]):
            while node:
                stacks[i].append(node.val)
                node = node.next
        # 然后pop两个栈的栈顶, 相加并记录进位, 直到两个栈都为空, 且进位为0
        res = None
        # carry存储当前进位
        carry = 0
        # 将carry是否为0也作为循环条件, 如果两个链表都遍历完毕但仍有进位时, 也需要进行处理
        while stacks[0] or stacks[1] or carry:
            # 注意栈为空时将对应数字置为0
            left = 0 if not stacks[0] else stacks[0].pop()
            right = 0 if not stacks[1] else stacks[1].pop()
            # 当前位的和
            sm = left + right + carry
            # 计算进位和当前位数字
            carry, bit = divmod(sm, 10)
            # 创建新的节点, 并指向上一个创建的节点
            node = ListNode(bit, res)
            res = node
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
