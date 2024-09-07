> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/IY6buf/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定三个字符串 s1、s2、s3，请判断 s3 能不能由 s1 和 s2 交织（交错） 组成。

两个字符串 s 和 t 交织 的定义与过程如下，其中每个字符串都会被分割成若干 非空 子字符串：

- s = s1 + s2 + ... + sn
- t = t1 + t2 + ... + tm
- |n - m| <= 1
- 交织 是 s1 + t1 + s2 + t2 + s3 + t3 + ... 或者 t1 + s1 + t2 + s2 + t3 + s3 + ...

提示：a + b 意味着字符串 a 和 b 连接。

### 示例 1：

![](https://assets.leetcode.com/uploads/2020/09/02/interleave.jpg)

- 输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
- 输出：true

### 示例 2：

- 输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
- 输出：false

### 示例 3：

- 输入：s1 = "", s2 = "", s3 = ""
- 输出：true

### 提示：

- 0 <= s1.length, s2.length <= 100
- 0 <= s3.length <= 200
- s1、s2、和 s3 都由小写英文字母组成

## 题目思考

1. s1 和 s2 要想交织成 s3, 要满足什么条件? 是否可以从单个字符的角度出发?

## 解决方案

- 分析题目, 要想求字符串 s1 和 s2 是否可以交织成 s3, 首先 s3 的长度一定要等于 s1 和 s2 的长度之和, 不等时一定无法交织
- 然后我们来考虑各自的最后一个字符`s1[-1]`和`s2[-1]`, 不难发现有三种情况:
  1. `s3[-1]`等于`s1[-1]`
  2. `s3[-1]`等于`s2[-1]`
  3. `s3[-1]`不等于上述两字符
- 显然第三种情况无法交织, 对于情况 1, 需要继续判断`s3[-2]`是否与`s1[-2]`或`s2[-1]`相等, 而对于情况 2, 需要继续判断`s3[-2]`是否与`s2[-2]`或`s1[-1]`相等
- 不难发现当前结果和前面的结果存在转移关系, 可以尝试用动态规划来解决
- 如果我们维护 s1 的前 i 个字符和 s2 的前 j 个字符是否可以交织成 s3 的前 i+j 个字符, 那么上面的情况 1 和 2 就可以分别从`(i-1,j)`和`(i,j-1)`下标对转移而来
- 我们保存前面下标对的结果, 那么只要前面下标对可以交织, 且当前字符相等, 则当前下标对也就可以交织, 这就是典型的动态规划的思想
- 用数学语言来表示: 假设`dp[i][j]`代表以 s1 的前 i 个字符和 s2 的前 j 个字符是否可以交织成 s3 的前 i+j 个字符, 那么就有:
  - `if i > 0:`
    - `dp[i][j] |= s1[i-1]==s3[i+j-1] && dp[i-1][j]`
  - `if j > 0:`
    - `dp[i][j] |= s2[j-1]==s3[i+j-1] && dp[i][j-1]`
- 最终结果就是`dp[len(s1)][len(s2)]`
- 注意`dp[0][0]`初始化为 true, 因为空字符串总是可以交织, 而其他 dp 值都初始化为 false, 需要判断可以交织后才能置为 true
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(MN)`: 假设 s1 和 s2 的长度分别是 M 和 N, 需要两重循环求 DP 值
- 空间复杂度 `O(MN)`: 二维 DP 数组的空间消耗

#### 代码

```python
class Solution:
    def isInterleave(self, s1: str, s2: str, s3: str) -> bool:
        # dp[i][j]表示s1和s2的前i个和前j个字符能否组成s3的前i+j的字符, 结果就是dp[le1][le2]
        le1, le2, le3 = len(s1), len(s2), len(s3)
        if le1 + le2 != le3:
            # s3长度不是前两者之和, 不可能交错
            return False
        dp = [[False] * (le2 + 1) for _ in range(le1 + 1)]
        # 初始化dp[0][0]是True, 因为两个空字符串组成的仍为空字符串
        dp[0][0] = True
        for i in range(le1 + 1):
            for j in range(le2 + 1):
                # k代表当前组成的新字符串的最后一个下标, 也即s3待匹配的字符下标
                k = i + j - 1
                if i > 0:
                    # i>0, 可以尝试将s3和s1对应位置的字符进行匹配
                    # 如果s3和s1对应的字符相同, 且前面的拼接也有效(即dp[i-1][j]为true), 则当前也有效
                    dp[i][j] |= s3[k] == s1[i - 1] and dp[i - 1][j]
                if j > 0:
                    # j>0, 可以尝试将s3和s2对应位置的字符进行匹配
                    # 如果s3和s2对应的字符相同, 且前面的拼接也有效(即dp[i][j-1]为true), 则当前也有效
                    dp[i][j] |= s3[k] == s2[j - 1] and dp[i][j - 1]
        return dp[le1][le2]
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
