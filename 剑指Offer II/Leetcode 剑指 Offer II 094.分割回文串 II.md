> 题目难度: 困难

> [原题链接](https://leetcode.cn/problems/omKAoA/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个字符串 s，请将 s 分割成一些子串，使每个子串都是回文串。

返回符合要求的 最少分割次数 。

### 示例 1：

- 输入：s = "aab"
- 输出：1
- 解释：只需一次分割就可将 s 分割成 ["aa","b"] 这样两个回文子串。

### 示例 2：

- 输入：s = "a"
- 输出：0

### 示例 3：

- 输入：s = "ab"
- 输出：1

### 提示：

- 1 <= s.length <= 2000
- s 仅由小写英文字母组成

## 题目思考

1. 如何尽可能优化时间复杂度?

## 解决方案

- 分析题目, 最容易想到的思路就是从起点开始, 每遇到一个回文子串, 就可以将其作为一段分割, 或者不分割向后继续找更长的回文子串
- 不过这个思路问题比较多, 不管是判断回文子串, 还是判断是否分割, 都需要大量重复计算, 如何优化呢?
- 首先我们来优化回文子串的判断: 我们可以预先定义一个二维 flag 数组来加速, `isPalindrome[i][j]`用于表示闭区间子串`[i,j]`是否是回文, 这样我们只需要计算一遍每个子串是否回文, 后面直接判断 isPalindrome 即可, 从而避免重复计算
- 那么对于当前子串`[i,j]`, 如果`s[i]==s[j]`且子串`[i+1,j-1]`是回文, 则当前子串也是回文
- 具体实现时, 我们可以使用两重循环求出: 外层循环从 n-1 开始逆序遍历 i, 内层循环从 i+1 开始顺序遍历 j, 这样可以保证 `isPalindrome[i+1][j-1]` 一定是之前已经求出的值, `isPalindrome[i][j]` 就等于`isPalindrome[i + 1][j - 1] && s[i] == s[j]`
- 然后我们来优化分割位置的判断: 对于闭区间子串`[0,j]`而言, 假设下标 i 位于 0 到 j 之间, 且子串`[i,j]`是回文子串, 那么显然`[0,j]`的最小分割次数可以是`[0,i-1]`的最小分割次数加 1 (即加上新一段的回文子串`[i,j]`), 这样我们就可以利用之前保存的最小分割次数推导出来当前的最小分割次数, 从而避免重复判断
- 由于可能存在多个满足要求的 i, 所以找到这些 i 之后, 取新得到的分割次数的最小值, 那就是`[0,j]`的最小分割次数
- 这就是典型的动态规划的思想, 用数学语言来表示, 假设`dp[j]`代表子串`[0,j]`的最小分割次数, 那么就有:
  - `if isPalindrome[0][j]: dp[j]=0`
  - `else: dp[j]=min(dp[i-1]+1) (1<=i<=j and isPalindrome[i][j])`
  - 最终结果就是`dp[n-1]`
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N^2)`: 求回文子串需要两重循环, 这部分是`O(N^2)`; 动态规划部分也是两重循环, 同样是`O(N^2)`. 所以整体时间复杂度就是`O(N^2)`
- 空间复杂度 `O(N^2)`: 二维回文子串 flag 数组的空间消耗

#### 代码

```python
class Solution:
    def minCut(self, s: str) -> int:
        n = len(s)
        # isPalindrome[i][j]用于表示闭区间子串[i,j]是否是回文
        isPalindrome = [[True] * n for _ in range(n)]
        for i in range(n - 1)[::-1]:
            for j in range(i + 1, n):
                # 对于当前子串[i,j], 如果s[i]==s[j]且子串[i+1,j-1]是回文, 则当前子串也是回文
                # 外层循环倒序, 内层循环正序, 这样可以保证isPalindrome[i+1][j-1]一定是之前已经求出的值
                isPalindrome[i][j] = isPalindrome[i + 1][j - 1] and s[i] == s[j]

        # dp[i]表示以i结尾的最少分割次数, 结果即为dp[-1]
        dp = [float("inf")] * n
        for j in range(n):
            if isPalindrome[0][j]:
                # [0,j]本身就是回文串, 无需分割
                dp[j] = 0
            else:
                for i in range(1, j + 1):
                    # i代表最后一个子串[i,j]的起点
                    if isPalindrome[i][j]:
                        # 如果[i,j]回文的话, dp[j]就可以从dp[i-1]+1转移过来
                        # 因为有多种可能的i, 所以取最小的结果作为dp[j]的值
                        dp[j] = min(dp[j], dp[i - 1] + 1)
        return dp[-1]
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
