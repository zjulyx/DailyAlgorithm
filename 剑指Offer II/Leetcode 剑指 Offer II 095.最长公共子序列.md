> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/qJnOS7/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个字符串 text1 和 text2，返回这两个字符串的最长 公共子序列 的长度。如果不存在 公共子序列 ，返回 0 。

一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。
两个字符串的 公共子序列 是这两个字符串所共同拥有的子序列。

### 示例 1：

- 输入：text1 = "abcde", text2 = "ace"
- 输出：3
- 解释：最长公共子序列是 "ace" ，它的长度为 3 。

### 示例 2：

- 输入：text1 = "abc", text2 = "abc"
- 输出：3
- 解释：最长公共子序列是 "abc" ，它的长度为 3 。

### 示例 3：

- 输入：text1 = "abc", text2 = "def"
- 输出：0
- 解释：两个字符串没有公共子序列，返回 0 。

### 提示：

- 1 <= text1.length, text2.length <= 1000
- text1 和 text2 仅由小写英文字符组成。

## 题目思考

1. 针对某个字符对, 以它们为结尾的两个子串的最长公共子序列可以根据什么得出?

## 解决方案

- 分析题目, 要想求字符串 s1 和 s2 的最长公共子序列, 我们先来考虑各自的最后一个字符`s1[-1]`和`s2[-1]`
- 不难发现有四种情况:
  1. 最长子序列使用了`s1[-1]`和 s2 前面某个等于`s1[-1]`的字符
  2. 最长子序列使用了`s2[-1]`和 s1 前面某个等于`s2[-1]`的字符
  3. 最长子序列同时使用了`s1[-1]`和`s2[-1]`, 此时需要满足两字符相等
  4. 最长子序列既没有使用`s1[-1]`, 也没有使用`s2[-1]`
- 如果我们维护以 s1 下标 i 和 s2 下标 j 结尾的两个子串的最长公共子序列长度, 那么以上四种情况就可以从(i,j-1), (i-1,j)或(i-1,j-1)下标对转移而来, 其最大值就是当前最长公共子序列长度, 这就是典型的动态规划的思想
- 用数学语言来表示: 假设`dp[i][j]`代表以 s1 下标 i 和 s2 下标 j 结尾的两个子串的最长公共子序列长度, 那么就有:
  - `if s1[i]==s2[j]:`
    - `dp[i][j]=max(dp[i][j-1],dp[i-1][j],dp[i-1][j-1]+1)`
  - `else:`
    - `dp[i][j]=max(dp[i][j-1],dp[i-1][j],dp[i-1][j-1])`
- 最终结果就是`dp[-1][-1]`
- 注意如果 i-1 或者 j-1 小于 0, 则对应 dp 值也是 0
- 另外 if 和 else 也可以合并: 用一个变量记录`dp[i-1][j-1]`, 如果当前字符对相等, 则该变量加 1
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N^2)`: 需要两重循环求 DP 值
- 空间复杂度 `O(N^2)`: 二维 DP 数组的空间消耗

#### 代码

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)
        dp = [[0] * n for _ in range(m)]
        for i in range(m):
            for j in range(n):
                # 情况1: 使用当前j但不使用i
                pdp1 = 0 if i == 0 else dp[i - 1][j]
                # 情况2: 使用当前i但不使用j
                pdp2 = 0 if j == 0 else dp[i][j - 1]
                # 情况3/4: 同时使用/不使用当前i和j
                pdp3 = 0 if i == 0 or j == 0 else dp[i - 1][j - 1]
                # 此时只有当i和j的字符相等时才能同时使用, 此时将公共长度加1
                pdp3 += 1 if text1[i] == text2[j] else 0
                dp[i][j] = max(pdp1, pdp2, pdp3)
        return dp[-1][-1]
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
