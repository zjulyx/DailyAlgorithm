> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数 max_value、push_back 和 pop_front 的均摊时间复杂度都是 O(1)。

若队列为空，pop_front 和 max_value  需要返回 -1

- 1 <= push_back,pop_front,max_value 的总操作数 <= 10000
- 1 <= value <= 10^5

## 题目样例

### 示例

- 输入:
- ["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
- [[],[1],[2],[],[],[]]
- 输出: [null,null,null,2,1,2]

- 输入:
- ["MaxQueue","pop_front","max_value"]
- [[],[],[]]
- 输出: [null,-1,-1]

## 题目思考

1. 要做到均摊时间复杂度为 O(1), 需要哪些数据结构?

## 解决方案

#### 思路

- 一个比较容易想到的思路是使用一个双端队列模拟, 然后每次利用 max 函数求最大值, 但这样求最大值的时间复杂度为 O(N), 不满足题目要求
- 如果我们能够动态维护当前队列的最大值, 那么求最大值的时候只需要用 O(1)时间返回这个值即可
- 但只使用一个变量来保存最大值并不够用, 因为当它被 pop 的时候必须重新计算新的最大值, 时间复杂度仍为 O(N)
- 所以需要把当前队列的第二大值, 第三大值...也都给保存下来
- 上面的分析是不是和昨天的题目[剑指 Offer 59 - I. 滑动窗口的最大值 - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484103&idx=1&sn=32e232b428b34d9d6eb7eb10011281a9&chksm=900285caa7750cdc1c95fe09f03dc3d127eafe435e793e8203a97ff155eb91d6d26296097989&token=948739292&lang=zh_CN#rd)很类似?
- 没错, 我们仍然需要把这些值保存在一个单调双端队列中, 一边是最小值, 一边是最大值, 大家可以结合昨天的文章一起阅读
- 这里采用单调队列左侧最小右侧最大的方案, 各个操作具体细节如下:

  - **max_value**: 直接返回单调队列右侧最大值即可, 不存在则返回-1
  - **push_back**: 将新元素加到正常队列左侧, 且从单调队列左侧不断弹出比新元素更小的值, 最后将新元素插入单调队列左侧作为新的最小值, 因为更小的值绝不可能是最大值候选项了(更小且更旧)
  - **pop_front**: 弹出并返回正常队列右侧元素, 不存在则返回-1; 且如果单调队列最大值恰好等于弹出的元素时, 也需要将其从单调队列中弹出

- 下面代码对必要的步骤有详细的解释, 特别是对 push_back 和 pop_front 的一些关键点的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(1): 显然 max_value 和 pop_front 操作的复杂度都是 O(1). 而对于 push_back 操作, 虽然它使用了 while 循环, 可能弹出多个元素, 但是每个元素只会进入和弹出单调队列各一次, 所以整个操作序列下来的均摊时间复杂度为 O(1)
- 空间复杂度 O(N): 队列需要存所有值

#### 代码

```python
class MaxQueue:
    def __init__(self):
        # 使用两个deque, 一个存正常的队列, 另一个存单调队列
        # 正常的队列
        self.q = collections.deque()
        # 单调队列, 左边小, 右边大
        self.monoq = collections.deque()

    def max_value(self) -> int:
        if not self.monoq:
            return -1
        # 直接返回最大值
        return self.monoq[-1]

    def push_back(self, value: int) -> None:
        # 将新元素加到正常队列左侧
        self.q.appendleft(value)
        # 保证新的值是最小值, 因为更小的值绝不可能是最大值候选项了(更小且更旧)
        # 注意这里不能是<=, 那样的话如果当前新值也恰好是最大值, <=的话就会把原来已经有的的最大值错误的弹出, 这样会导致后面的最大值计算出现错误, 只会统计1个最大值, 而不是原来的若干个
        while self.monoq and self.monoq[0] < value:
            self.monoq.popleft()
        self.monoq.appendleft(value)

    def pop_front(self) -> int:
        if not self.q:
            return -1
        # 弹出正常队列最右侧元素
        res = self.q.pop()
        # 如果最右侧元素恰好也是最大值的话, 也从单调队列中弹出它
        # 注意这里最右侧元素只能有两种情况: 是最大值或者不在单调队列中
        # 因为如果最右侧元素存在于单调队列且不是最大值时, 那么说明它在正常队列左侧的元素中存在比它大的, 那根据上面的push_back操作, 左侧的那个最大值一定会把这个最右侧元素给淘汰掉, 而不会留它在单调队列中, 与假设矛盾, 所以这种情况不成立
        if res == self.monoq[-1]:
            self.monoq.pop()
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
