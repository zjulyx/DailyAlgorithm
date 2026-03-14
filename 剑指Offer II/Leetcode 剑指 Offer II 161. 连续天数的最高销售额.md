> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某公司每日销售额记于整数数组 sales，请返回所有 连续 一或多天销售额总和的最大值。

要求实现时间复杂度为 O(n) 的算法。

### 示例 1：

- 输入：sales = [-2,1,-3,4,-1,2,1,-5,4]
- 输出：6
- 解释：[4,-1,2,1] 此连续四天的销售总额最高，为 6。

### 示例 2：

- 输入：sales = [5,4,-1,7,8]
- 输出：23
- 解释：[5,4,-1,7,8] 此连续五天的销售总额最高，为 23。

### 提示：

- 1 <= arr.length <= 10^5
- -100 <= arr[i] <= 100

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
    def maxSales(self, sales: List[int]) -> int:
        # 初始化最终结果为负无穷, 因为可能数组全部都是负数
        res = -float('inf')
        # 初始化和为0
        sm = 0
        for x in sales:
            # 计算当前结尾的最大值
            sm = max(sm + x, x)
            # 更新最终结果为当前最大的最大值
            res = max(res, sm)
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
