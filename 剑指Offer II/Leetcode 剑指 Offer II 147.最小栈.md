> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

请你设计一个 最小栈 。它提供 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

实现 MinStack 类:

- MinStack() 初始化堆栈对象。
- void push(int val) 将元素 val 推入堆栈。
- void pop() 删除堆栈顶部的元素。
- int top() 获取堆栈顶部的元素。
- int getMin() 获取堆栈中的最小元素。

### 示例 1：

- 输入：
  - ["MinStack","push","push","push","getMin","pop","top","getMin"]
  - [[],[-2],[0],[-3],[],[],[],[]]
- 输出：
  - [null,null,null,null,-3,null,0,-2]
- 解释：
  - MinStack minStack = new MinStack();
  - minStack.push(-2);
  - minStack.push(0);
  - minStack.push(-3);
  - minStack.getMin(); --> 返回 -3.
  - minStack.pop();
  - minStack.top(); --> 返回 0.
  - minStack.getMin(); --> 返回 -2.

### 提示：

- -2^31 <= val <= 2^31 - 1
- pop、top 和 getMin 操作总是在 非空栈 上调用
- push、pop、top 和 getMin 最多被调用 3 \* 10^4 次

## 题目思考

1. 内部需要什么数据结构来满足所有操作都是 O(1), 一个栈够吗?

## 解决方案

### 思路

- 要使得 push 和 pop 的复杂度为 O(1), 传统的栈就可以搞定, 难点在于如何使得 min 函数也为 O(1)
- 如果我们能一直维护当前所有元素的最小值, 那么 min 函数直接返回它就可以, 但问题是在 pop 的时候有可能会正好 pop 这个最小值, pop 之后的最小值(也即原来的次小值)如何得到呢?
- 要存储多个最小值, 显然一个变量不够用. 而根据上一步的分析, 这里我们可以考虑额外引入一个**单调递减栈**, 栈顶存当前最小值, 下面依次是次小, 第三小...
- 这样如果 pop 了最小值的话, 这个单调栈的栈顶仍会保存 pop 后的最小值, 每次 min 只需要取这个栈的栈顶即可
- 而 push 的时候也需要额外的操作, 由于是单调栈, 只需要在新的值**小于等于**栈顶的时候才 push 到单调栈中.特别注意在等于栈顶的时候也要 push 到单调栈中, 这是因为如果对于重复的最小值 x 不 push, 那么在后续的 pop 其中一个 x 之后, 栈顶(不再是 x)就和实际最小值(仍为 x)不一致了

### 复杂度

- 时间复杂度 `O(1)`
  - 各种操作都是常数复杂度
- 空间复杂度 `O(N)`
  - 使用了两个栈

### 代码

```python
class MinStack:
    def __init__(self):
        """
        initialize your data structure here.
        """
        # 一个普通栈和一个单调递减栈
        self.minstack = []
        self.stack = []

    def push(self, x: int) -> None:
        self.stack.append(x)
        if not self.minstack or x <= self.minstack[-1]:
            # 如果单调栈顶为空或者当前新值小于等于单调栈顶才push
            # 注意这里等于也需要push. 如果对于重复的最小值 x 不 push, 那么在后续的 pop 其中一个 x 之后, 栈顶(不再是 x)就和实际最小值(仍为 x)不一致了
            self.minstack.append(x)

    def pop(self) -> None:
        if not self.stack:
            return
        x = self.stack.pop()
        if x == self.minstack[-1]:
            # 如果单调栈顶恰好等于pop的值, 也要pop单调栈
            self.minstack.pop()

    def top(self) -> int:
        if not self.stack:
            return -1
        return self.stack[-1]

    def getMin(self) -> int:
        if not self.minstack:
            return -1
        return self.minstack[-1]
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
