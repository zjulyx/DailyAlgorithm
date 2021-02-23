> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个整型数组，数组里有正数也有负数。数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为 O(n)。

- 1 <= arr.length <= 10^5
- -100 <= arr[i] <= 100

## 题目样例

### 示例

- 输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
- 输出: 6
- 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

## 题目思考

1. 如何记录最大和?
2. 可以不使用额外空间吗?

## 解决方案

### 思路

- 题目要求复杂度 O(N), 那么我们就不能使用暴力两重循环求当前前缀和的方法了, 那样的复杂度是 O(N^2)
- 那如何做到一次遍历就计算出结果呢?
- 假设当前以 i 结尾的最大和是 sm, 那么到 i+1 的时候, 以它结尾的最大和可以有两种选择:
  - 在 sm 的基础上加上 i+1 的值
  - 也可以另起炉灶, 从 i+1 开始计算 (对应的是 `sm < 0` 的情况)
- 也即 i+1 结尾的最大和就是 `max(sm+arr[i+1], arr[i+1])`
- 它就是新的 sm 值, 这样就不需要额外的空间
- 而最终的结果自然就是`max(以各个下标结尾的最大和)`, 可以在遍历的时候顺带一起判断
- 以上就是典型的动态规划的思想, 利用前面的计算结果来推导出当前的结果
- 下面的代码对必要步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历整个数组一遍
- 空间复杂度 `O(1)`
  - 不需要额外空间

### 代码

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        # 初始化最终结果为负无穷, 因为可能数组全部都是负数
        res = -float('inf')
        # 初始化和为0
        sm = 0
        for x in nums:
            # 计算当前结尾的最大值
            sm = max(sm + x, x)
            # 更新最终结果为当前最大的最大值
            res = max(res, sm)
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
