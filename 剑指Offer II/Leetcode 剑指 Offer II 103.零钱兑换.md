> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/gaM7Ch/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

### 示例 1：

- 输入：coins = [1, 2, 5], amount = 11
- 输出：3
- 解释：11 = 5 + 5 + 1

### 示例 2：

- 输入：coins = [2], amount = 3
- 输出：-1

### 示例 3：

- 输入：coins = [1], amount = 0
- 输出：0

### 示例 4：

- 输入：coins = [1], amount = 1
- 输出：1

### 示例 5：

- 输入：coins = [1], amount = 2
- 输出：2

### 提示：

- 1 <= coins.length <= 12
- 1 <= coins[i] <= 2^31 - 1
- 0 <= amount <= 10^4

## 题目思考

1. 如何优化算法复杂度?

## 解决方案

- 分析题目, 每个硬币都可以使用任意次, 一个很容易想到的做法就是回溯:
  - 函数传入当前硬币下标 i 和总金额 sum, 返回所需最少硬币的个数, 如果返回 inf 则说明没有有效解
  - 然后遍历每一个硬币, 记录从当前状态(第 i 个硬币和已有金额 sum)开始能凑成 amount 所需的最少硬币数 minCnt, 初始化为 inf
  - 然后从 0 开始遍历当前硬币的使用个数 curCnt, 只要已有金额 sum 加上当前硬币的总金额(`curCnt*coins[i]`)不超过 amount, 就可以使用这么多当前硬币, 就可以更新 minCnt 为当前值和下个状态`(i+1, sum+curCnt*coins[i])+cnt` 的较小值, 遍历完有效个数后返回 minCnt 即可, 就代表从当前状态开始能凑成 amount 所需的最少硬币数
  - 最终遍历完所有硬币时: 如果总金额恰好等于 amount, 就返回 0, 代表找到一个有效解; 否则返回 inf, 代表无效情况
- 不过回溯的时间复杂度很高, 按照题目规模, 最差情况下 coins 有 12 个, 面值从 1 到 12, amount 是 10^4, 回溯的总操作数就是`(10^4)^12/(1*2*3*...*12)`, 这样肯定会超时, 如何优化呢?
- 由于 amount 的范围较小, 我们可以利用这一点, 通过动态规划解决这个问题, 具体做法如下:
  - 定义 `dp[money]` 代表凑成 money 金额所需要的最少硬币数, 显然 money 取值范围是`[0,amount]`, 而初始情况是 `dp[0]=0`, 其他 dp 值为 inf
  - 然后针对每个硬币 coin, 枚举它可能组成的所有金额 money, 即从其自身面值开始, 一直遍历到最大值 amount, 更新对应的 `dp[money]=min(dp[money], dp[money-coin]+1)`
  - 最终遍历完成后的 `dp[amount]` 即为所求, 如果它仍是 inf, 则说明无法找到有效组合能组成金额 amount, 返回-1
- 举个例子, 假设有两枚硬币, 面值分别为 2 和 3, amount 是 6, 那么在处理硬币 2 时, 就有`dp[2]=1, dp[4]=2, dp[6]=3`, 然后再处理硬币 3, 可以得到`dp[3]=1, dp[5]=2, dp[6]=2`, 最终`dp[6]`就是 2
- 这样我们就将原来回溯的指数时间复杂度降低到了动态规划的两重循环
- 下面的代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N*A)`: N 是 coins 数组长度, A 是 amount, 外层循环需要遍历所有 coins, 内层循环需要遍历不超过 amount 的金额
- 空间复杂度 `O(A)`: dp 数组需要保存 amount 个数字

#### 代码

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        # dp[money]存储得到金额money所需要的最少硬币数, 初始化为inf, 代表没找到有效组合的情况
        dp = [float("inf")] * (amount + 1)
        # 金额为0时所需的硬币数也是0
        dp[0] = 0
        for coin in coins:
            for money in range(coin, amount + 1):
                # 当前金额可以通过money-c的金额加上当前硬币转移得到, 当前dp更新为两者较小值
                dp[money] = min(dp[money], dp[money - coin] + 1)
        # 如果dp[amount]仍为inf, 则说明无法找到有效组合能组成金额amount, 返回-1, 否则返回对应最小组合数
        return -1 if dp[amount] == float("inf") else dp[amount]
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
