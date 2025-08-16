> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个头节点为 head 的链表用于记录一系列核心肌群训练项目编号，请查找并返回倒数第 cnt 个训练项目编号。

### 示例 1：

- 输入：head = [2,4,7,8], cnt = 1
- 输出：8

### 提示：

- 1 <= head.length <= 100
- 0 <= head[i] <= 100
- 1 <= cnt <= head.length

## 题目思考

1. 可以一次遍历搞定吗?
2. 可以不使用额外空间吗?

## 解决方案

### 方案 1

#### 思路

- 最直接的思路, 直接保存每个节点到数组中, 然后下标 `n-cnt` 的节点即为所求

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍链表
- 空间复杂度 `O(N)`
  - 额外需要一个数组存 N 个节点

#### 代码

```python
class Solution:
    def trainingPlan(self, head: Optional[ListNode], cnt: int) -> Optional[ListNode]:
        # 方法1: 数组存储每个节点
        arr = []
        while head:
            arr.append(head)
            head= head.next
        return arr[len(arr)-cnt]
```

### 方案 2

#### 思路

- 如果我们能得到链表节点数目 `n`, 那么倒数第 `cnt` 个节点就是正数第 `n-cnt+1` 个节点 (从 1 开始计数)
- 利用这个性质, 我们先遍历一遍链表, 得到数目 `n`
- 然后再遍历前 `n-cnt+1` 个节点即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历两遍链表
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def trainingPlan(self, head: Optional[ListNode], cnt: int) -> Optional[ListNode]:
        # 方法2: 计数然后正向遍历
        n = 0
        cur = head
        while cur:
            n += 1
            cur = cur.next
        cur = head
        for i in range(n - cnt):
            cur = cur.next
        return cur
```

### 方案 3

#### 思路

- 回顾前两种方案, 要么使用了额外空间, 要么遍历了多次, 有没有一次遍历又不需要使用额外空间的方法呢?
- 答案也是有的, 需要求倒数第 cnt 个节点, 我们完全可以定义两个指针, 保证两个指针距离为 cnt, 这样当后面的指针到达结尾时, 前面的指针正好就在倒数第 cnt 个节点处
- 要保证两个指针距离为 cnt, 我们只需要让后面的指针先走 cnt 步 (快指针), 然后前面的指针再从开头出发 (慢指针), 两个指针按照每次 1 步的节奏继续同时往下走即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个数字只需要被遍历一遍
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def trainingPlan(self, head: Optional[ListNode], cnt: int) -> Optional[ListNode]:
        # 双指针快慢节点
        # 快指针先走k步, 然后慢指针再开始走, 这样能保证两者距离一直为k, 那么当快指针走到结尾时满指针就恰好停在倒数第k个节点
        fast = head
        for i in range(cnt):
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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
