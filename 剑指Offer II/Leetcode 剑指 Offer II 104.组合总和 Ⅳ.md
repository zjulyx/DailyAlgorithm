> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/D0F0SV/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个由 不同 正整数组成的数组 nums ，和一个目标整数 target 。请从 nums 中找出并返回总和为 target 的元素组合的个数。数组中的数字可以在一次排列中出现任意次，但是顺序不同的序列被视作不同的组合。

题目数据保证答案符合 32 位整数范围。

### 示例 1：

- 输入：nums = [1,2,3], target = 4
- 输出：7
- 解释：
  - 所有可能的组合为：
  - (1, 1, 1, 1)
  - (1, 1, 2)
  - (1, 2, 1)
  - (1, 3)
  - (2, 1, 1)
  - (2, 2)
  - (3, 1)
  - 请注意，顺序不同的序列被视作不同的组合。

### 示例 2：

- 输入：nums = [9], target = 3
- 输出：0

### 提示：

- 1 <= nums.length <= 200
- 1 <= nums[i] <= 1000
- nums 中的所有元素 互不相同
- 1 <= target <= 1000

- 进阶：如果给定的数组中含有负数会发生什么？问题会产生何种变化？如果允许负数出现，需要向题目中添加哪些限制条件？

## 题目思考

1. 如何回答进阶问题?

## 解决方案

- 分析题目, 可以发现它跟上道题目[Leetcode 剑指 Offer II 103.零钱兑换](https://mp.weixin.qq.com/s/30XLoZpbXABzMev9KMjQXA?token=260437935&lang=zh_CN)非常类似, 只是把求最少数字个数改成了求有效组合数
- 所以我们可以同样利用动态规划的思路, 只需要稍作改动, 具体做法如下:
  - 定义 `dp[t]` 代表总和为 t 的有效组合数, 显然 t 的取值范围是`[0,target]`, 而初始情况是 `dp[0]=1`, 代表空集时的有效组合数为 1, 其他 dp 值为 0
  - 由于顺序不同的序列被视作不同的组合, 所以即使组合总和相同, 只要其最后一个数字不同, 就是不同的组合
  - 我们可以利用这一点, 外层循环遍历所有可能的组合总和 sm, 即从 1 到 target, 然后内层循环遍历数组的每个数字 num, 作为当前组合的最后一个数字, 只要它不超过当前组合总和, 就可以将`dp[t-num]`的值累加到当前`dp[t]`
  - 用数学公式来表示, 就是 `dp[t]=sum(dp[t-num]) (num in nums && num <= t)`
  - 最终遍历完成后的 `dp[target]` 即为所求
- 接下来我们来思考进阶问题: 数组包含负数的情况
  - 此时总是可以构造出总和为 0 的数字组合, 例如正数 a 和负数-b, 我们可以使用 b 个 a 和 a 个 -b 形成组合, 其总和就是 0
  - 这样有效组合数就会变得无限了, 因为总是可以加上任意个 0, 总和仍保持不变
  - 所以当数组存在负数时, 需要额外加上对有效组合长度的限制, 否则总是可以无限扩展
- 下面的代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N*T)`: N 是数组长度, T 是 target, 外层循环需要遍历 1~target, 内层循环需要遍历数组所有数字
- 空间复杂度 `O(T)`: dp 数组需要保存 target+1 个数字

#### 代码

```python
class Solution:
    def combinationSum4(self, nums: List[int], target: int) -> int:
        # dp[t]存储总和为t的有效组合个数
        dp = [0] * (target + 1)
        # 初始化dp[0]为1, 即空集
        dp[0] = 1
        for t in range(1, target + 1):
            for num in nums:
                if t >= num:
                    # 当前dp[t]可以从dp[t-num]转移而来, 即最后一个元素使用num
                    dp[t] += dp[t - num]
        # 最终结果就是dp[target]
        return dp[target]
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
