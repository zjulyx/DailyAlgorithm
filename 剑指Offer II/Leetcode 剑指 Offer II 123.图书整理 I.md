> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

书店店员有一张链表形式的书单，每个节点代表一本书，节点中的值表示书的编号。为更方便整理书架，店员需要将书单倒过来排列，就可以从最后一本书开始整理，逐一将书放回到书架上。请倒序返回这个书单链表。

### 示例 1：

- 输入：head = [3,6,4,1]
- 输出：[1,4,6,3]

### 提示：

0 <= 链表长度 <= 10000
0 <= path.length <= 10000

## 题目思考

1. 如果限制只能用递归来做, 或者只能用迭代来做, 你能想到哪些方案?

## 解决方案

### 方案 1

#### 思路

- 倒序返回书单链表, 一个很自然的思路就是先按照头到尾的顺序遍历并保存到数组中, 最后将数组翻转即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 链表中每个节点只需要遍历一遍
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def reverseBookList(self, head: Optional[ListNode]) -> List[int]:
        # 方法1: 正向遍历, 然后数组翻转
        res = []
        while head:
            res.append(head.val)
            head = head.next
        return res[::-1]
```

### 方案 2

#### 思路

- 如果此时题目多了个限制, 只能用递归来做, 我们就得利用递归的特性了

1. 先设计递归参数, 这里只需要传入节点本身即可, 因为只需要知道它自己的值
2. 然后定义递归出口, 很显然就是节点不存在时直接返回
3. 然后我们先尽可能向后调用递归函数, 直到到达递归出口
4. 最后再将当前节点加入结果中. 这样就保证尾部节点第一个加入结果中, 按照递归栈的顺序, 接下来是倒数第二个节点, 以此类推, 最终所有的节点都会按照从尾到头的顺序加入结果中

#### 复杂度

- 时间复杂度 `O(N)`
  - 链表中每个节点只需要遍历一遍
- 空间复杂度 `O(N)`
  - 递归的栈的消耗

#### 代码

```python
class Solution:
    def reverseBookList(self, head: Optional[ListNode]) -> List[int]:
        # 方法2: 递归
        res = []

        def addNode(node):
            if not node:
                # 递归出口, 当前node是None
                return
            # 先递归调用下一个节点
            addNode(node.next)
            # 此时后面的节点都已经按照从尾到头的顺序, 依次加入了res, 加入当前节点即可
            # 如果这一句和上一句交换位置, 那就变成了从头到尾的顺序了, 因为先遍历到的节点先加入res中
            res.append(node.val)
        # 初始传入头节点, 开始递归
        addNode(head)
        return res
```

### 方案 3

#### 思路

- 如果此时面试官再来个限制, 只能用迭代, 而且不允许数组翻转, 又该怎么做呢 (内心 OS: 面试官真难伺候 🤣)
- 回顾方案 2, 我们使用了递归来实现, 而递归本质上就是一个递归栈来保存整个调用链, 所以我们可以尝试引入一个栈来解决 (其实很多递归问题都可以通过改成栈来变成等价的迭代)
- 思路也很简单, 就是利用栈先进后出的性质

1. 从头遍历链表, 并将节点值加入栈里
2. 然后再循环 pop 栈里的所有值, 并按顺序加入结果中, 这样第一个加入的就是尾节点的值, 最后才是头节点, 保证从尾到头的顺序

#### 复杂度

- 时间复杂度 `O(N)`
  - 链表中每个节点只需要遍历两遍
- 空间复杂度 `O(N)`
  - 使用了一个长度为 N 的栈

#### 代码

```python
class Solution:
    def reverseBookList(self, head: Optional[ListNode]) -> List[int]:
        # 方法3: 使用辅助栈
        stack = []
        while head:
            # 先按顺序加入栈中
            stack.append(head)
            head = head.next
        res = []
        while stack:
            # 按照栈的pop顺序加入结果中
            res.append(stack.pop().val)
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
