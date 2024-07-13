> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/GzCJIP/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

数组的每个下标作为一个阶梯，第 i 个阶梯对应着一个非负数的体力花费值 cost[i]（下标从 0 开始）。

每当爬上一个阶梯都要花费对应的体力值，一旦支付了相应的体力值，就可以选择向上爬一个阶梯或者爬两个阶梯。

请找出达到楼层顶部的最低花费。在开始时，你可以选择从下标为 0 或 1 的元素作为初始阶梯。

### 示例 1：

- 输入：cost = [10, 15, 20]
- 输出：15
- 解释：最低花费是从 cost[1] 开始，然后走两步即可到阶梯顶，一共花费 15 。

### 示例 2：

- 输入：cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
- 输出：6
- 解释：最低花费方式是从 cost[0] 开始，逐个经过那些 1 ，跳过 cost[3] ，一共花费 6 。

### 提示：

- 2 <= cost.length <= 1000
- 0 <= cost[i] <= 999

## 题目思考

1. 第 n 个阶梯的最低花费可以根据什么得出?

## 解决方案

- 这个题目非常类似之前剑指 Offer 系列的([青蛙跳台阶问题](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247483889&idx=1&sn=61d6e8045d51afea0871e4bd8cbc8147&chksm=900286fca7750fea4078a9987e81c6642c29540582bf60a27ecbe8b8943b969b81bbcb3c1c55&scene=178&cur_album_id=1386231241346859009#rd)), 只是那道题目是求总方案数, 而这里是求最小花费, 感兴趣的同学可以先看看那篇文章, 然后举一反三, 尝试自己解答
- 分析题目, 每次要么跳 1 级, 要么跳 2 级, 也就是相邻台阶之间的关系非常明确, 可以尝试动态规划的思路
- 对于第 n 个阶梯来说, 它可以从 n-1 阶跳 1 级得到, 也可以由 n-2 阶跳 2 级得到, 自然第 n 阶的最小花费就是 n-1 阶和 n-2 阶的最小花费的较小值, 再加上跳到第 n 阶的花费
- 用数学语言来表示: 假设`dp[n]`代表到达第 n 个阶梯的最低花费, 那么就有`dp[n]=min(dp[n-1], dp[n-2])+cost[n]`, 另外开始时可以选择下标 0 或 1 作为初始阶梯, 所以初始化`dp[0]=cost[0]`, `dp[1]=cost[1]`
- 由于当前 dp 值只和前面两个 dp 值有关, 所以我们可以只使用两个变量 `pre` 和 `ppre`, 分别代表当前下标的前一个和更前一个下标的 dp 值 `dp[n-1]` 和 `dp[n-2]`.
- 然后当前的 dp 值就更新为它们两个的较小值加上当前花费
- 最后更新`ppre`和`pre`, 分别更新为旧的`pre`和当前 dp 值, 用于下一个 dp 值的计算
- 而最终结果就是`ppre`和`pre`的较小值, 因为楼顶可以从倒数第一阶或倒数第二阶上去
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 需要遍历整个数组一遍
- 空间复杂度 `O(1)`: 只需要几个常数空间的变量

#### 代码

```python
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        # 初始化ppre和pre为第0和第1阶梯的花费
        ppre, pre = cost[0], cost[1]
        for c in cost[2:]:
            # 当前dp值是ppre和pre的较小值再加上当前花费
            # 然后滚动更新ppre和pre
            ppre, pre = pre, min(ppre, pre) + c
        # 最终结果是倒数第二阶或倒数第一阶的最小花费的较小值
        return min(ppre, pre)
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
