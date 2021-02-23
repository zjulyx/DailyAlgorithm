> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

> 这道题是经典的股票问题, 以它为基础可以延伸很多扩展问题, 我之前也写过整个股票系列, 一共 6 道题目, 大家感兴趣的话在公众号里回复 **股票** 就能看到了~

## 题目描述

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

- 0 <= 数组长度 <= 10^5

## 题目样例

### 示例

- 输入: [7,1,5,3,6,4]
- 输出: 5
- 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。

- 输入: [7,6,4,3,1]
- 输出: 0
- 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

## 题目思考

1. 只能买卖一次, 要求最大利润, 有没有什么直观思路, 比如贪心法?

## 解决方案

### 思路

- 因为只能买卖一次, 所以我们必须要保证买卖的那次收益最大, 这才是最佳时机, 这就要用到贪心的思路
- 我们可以维护一个当前的最小值, 然后遍历整个数组, 每次都用当前值减去最小值, 如果收益比最终结果大的话, 就更新最终结果, 这样最终结果一定是最大的收益
- 注意如果当前值比最小值小的话, 也要更新最小值, 意味着之后的遍历要以更新后的最小值作为买入起点

### 复杂度

- 时间复杂度 O(N): 需要遍历整个数组一遍
- 空间复杂度 O(1): 只需要维护常数个变量

### 代码

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        res = 0
        mn = float('inf')
        for p in prices:
            mn = min(mn, p)
            res = max(res, p - mn)
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
