> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/qIsx9U/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个整数数据流和一个窗口大小，根据该滑动窗口的大小，计算滑动窗口里所有数字的平均值。

实现 MovingAverage 类：

- MovingAverage(int size) 用窗口大小 size 初始化对象。
- double next(int val) 成员函数 next 每次调用的时候都会往滑动窗口增加一个整数，请计算并返回数据流中最后 size 个值的移动平均值，即滑动窗口里所有数字的平均值。

### 示例：

- 输入：
  - inputs = ["MovingAverage", "next", "next", "next", "next"]
  - inputs = [[3], [1], [10], [3], [5]]
- 输出：
  - [null, 1.0, 5.5, 4.66667, 6.0]
- 解释：
  - MovingAverage movingAverage = new MovingAverage(3);
  - movingAverage.next(1); // 返回 1.0 = 1 / 1
  - movingAverage.next(10); // 返回 5.5 = (1 + 10) / 2
  - movingAverage.next(3); // 返回 4.66667 = (1 + 10 + 3) / 3
  - movingAverage.next(5); // 返回 6.0 = (10 + 3 + 5) / 3

### 提示：

- 1 <= size <= 1000
- -10^5 <= val <= 10^5
- 最多调用 next 方法 10^4 次

## 题目思考

1. 可以使用什么数据结构模拟整个过程?
2. 如何优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 要想动态维护最后 size 个元素, 需要支持一端添加新元素, 另一端移除老元素, 显然可以使用双端队列来模拟这个过程
- 每次调用 next 函数时, 先将新元素添加到队列中, 然后判断队列长度, 如果超过了 size, 则弹出另一端最老的元素
- 这样就保证了队列里存储的滑动窗口的有效元素, 将它们的和除以元素个数, 就是当前窗口平均值
- 我们还可以利用**后缀和**进一步优化, 添加或弹出元素时分别将后缀和加上或减去对应元素, 这样就把计算窗口元素和的时间复杂度从 O(size) 优化到了 O(1)
- 下面的代码就对应了上面的整个过程, 并且有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(1): next 函数只需要几次 O(1)的操作
- 空间复杂度 O(size): 需要额外双端队列存储 size 个元素

#### 代码

```python
class MovingAverage:
    def __init__(self, size: int):
        # 双端队列+后缀和
        self.size = size
        self.window = collections.deque()
        self.sum = 0

    def next(self, val: int) -> float:
        self.window.append(val)
        self.sum += val
        if len(self.window) > self.size:
            # 超出大小了, 移除最左侧元素并更新后缀和
            self.sum -= self.window.popleft()
        return self.sum / len(self.window)
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
