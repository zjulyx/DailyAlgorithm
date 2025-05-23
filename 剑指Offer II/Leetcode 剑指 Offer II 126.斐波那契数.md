> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

斐波那契数 （通常用 F(n) 表示）形成的序列称为 斐波那契数列 。该数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：

F(0) = 0，F(1) = 1
F(n) = F(n - 1) + F(n - 2)，其中 n > 1
给定 n ，请计算 F(n) 。

答案需要取模 1e9+7(1000000007) ，如计算初始结果为：1000000008，请返回 1。

### 示例 1：

- 输入：n = 2
- 输出：1
- 解释：F(2) = F(1) + F(0) = 1 + 0 = 1

### 示例 2：

- 输入：n = 3
- 输出：2
- 解释：F(3) = F(2) + F(1) = 1 + 1 = 2

### 示例 3：

- 输入：n = 4
- 输出：3
- 解释：F(4) = F(3) + F(2) = 2 + 1 = 3

### 提示：

0 <= n <= 100

## 题目思考

1. 如何利用递推条件?

## 解决方案

### 思路

#### 分析

- 初始化条件和递推式(转移方程)都给了, 这就是最基础的动态规划思想了: 保存已有信息, 新的信息通过已有信息计算得到(就是转移过程), 避免重复计算
- 这里引申一点, 动态规划的难点在于如何将具体问题进行转换, 以及如何找到转移方程
- 我的建议是遇到问题都可以先尝试分析相邻的值(数组的相邻元素, 图的相邻点), 看看它们是否存在一些关系; 另外还可以尝试增加维度/状态找相邻点, 以及找当前值与之前多个值的关系等等
- 感兴趣的小伙伴可以在我的公众号"每日精选算法题"中的聊天框中回复 **股票**, 那个系列里有几个比较进阶的动态规划题目

#### 实现

- 定义 dp 数组, 初始化`dp[0] = 0, dp[1] = 1`
- 然后从 `2` 开始循环直到 `n`, 每次套用`dp[n] = dp[n-1] + dp[n-2]` 即可
- 特别注意根据题目要求, 每次相加得到新的 dp 值时需要取模 (如果最后结果再取模的话, 对于 python 而言由于涉及大数操作, 速度会慢一些, 而对于 C/Java 等语言的话则可能会导致溢出)
- **优化**
  - 注意到当前 dp 值只和前面两个 dp 值有关, 所以我们完全可以只使用两个变量 `pre` 和 `prepre`, 分别代表当前下标的前一个和再上一个下标的 dp 值 `dp[n-1]` 和 `dp[n-2]`.
  - 然后当前的 dp 值就更新为它们两个的和
  - 最后更新当前 dp 值为新的 `pre`, 原来的 `pre` 就是新的 `prepre` 了
  - 而最终结果就是 `pre` 了, 因为遍历完 n 之后, 当前 dp 值已经更新为 `pre` 了

### 复杂度

- 时间复杂度 `O(N)`
  - 需要遍历整个数组一遍
- 空间复杂度 `O(1)`
  - 优化后只需要几个变量即可

### 代码

```python
class Solution:
    def fib(self, n: int) -> int:
        if n < 2:
            # n小于2的情况, 根据初始化, 直接返回n即可
            return n
        prepre, pre = 0, 1
        for i in range(2, n + 1):
            # 同时更新新的prepre和pre, 这里利用了python的语言特性, 同时赋值的时候不会相互影响, 其他语言可能需要额外一个tmp变量来先保存原来的pre了
            prepre, pre = pre, (prepre + pre) % 1000000007
        return pre
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
