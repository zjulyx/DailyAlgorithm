> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/M99OJA/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个字符串 s ，请将 s 分割成一些子串，使每个子串都是 回文串 ，返回 s 所有可能的分割方案。

回文串 是正着读和反着读都一样的字符串。

### 示例 1：

- 输入：s = "google"
- 输出：[["g","o","o","g","l","e"],["g","oo","g","l","e"],["goog","l","e"]]

### 示例 2：

- 输入：s = "aab"
- 输出：[["a","a","b"],["aa","b"]]

### 示例 3：

- 输入：s = "a"
- 输出：[["a"]]

### 提示：

- 1 <= s.length <= 16
- s 仅由小写英文字母组成

## 题目思考

1. 如何快速判断子串是否回文?

## 解决方案

- 分析题目, 一个很直接的想法就是从开头开始, 找出每个可能的回文子串, 直到遍历完整个字符串
- 这就是典型递归回溯的思路, 具体如下:
  1. 假设输入字符串的长度是 n
  2. 记录当前子串起始下标 i (初始化为 0), 以及当前的回文子串分割 partition (初始化为空列表)
  3. 然后遍历 i 到 n-1 的下标 j, 将其作为子串的终点, 判断[i,j]闭区间的子串是否是回文
  4. 是的话可以将这个子串加入当前分割中, 然后从 j+1 开始继续递归
  5. 最终当 i 达到 n 时, 说明找到一个有效分割, 将其加入最终结果
- 在上面第三步中, 如何判断[i,j]闭区间的子串是否回文呢?
- 我们同样可以利用递归的方法判断:
  - 如果 i>=j 说明是空串或者单字符串, 直接返回 true
  - 否则就判断 s[i]和 s[j]是否相等, 相等的话再递归调用[i+1,j-1], 并返回其结果即可
- 这里我们可以将上述回文判断结果缓存起来, 避免重复计算, 这就是记忆化搜索的思想
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N*2^N)`: 记忆化搜索部分需要处理`N*N`种情况, 时间复杂度是 `O(N^2)`; 而对于递归回溯部分, 最差情况有 2^N 个有效分割 (每个字符都相等), 而对于每个分割, 都需要遍历字符串, 这部分是 `O(N)`, 综合起来时间复杂度是 `O(N*2^N)`, 大于 `O(N^2)`, 所以最终时间复杂度就是 `O(N*2^N)`
- 空间复杂度 `O(N^2)`: 记忆化搜索部分需要保存 N^2 个组合, 而递归栈的最大递归深度为 N, 综合起来空间复杂度就是 `O(N^2)`

#### 代码

```python
class Solution:
    def partition(self, s: str) -> List[List[str]]:
        @functools.cache
        def isPalindrome(i, j):
            if i >= j:
                # 空字符串或单个字符, 都是有效回文
                return True
            # 否则需满足两端字符相等, 且中间部分也是回文
            return s[i] == s[j] and isPalindrome(i + 1, j - 1)

        res = []

        def dfs(i, partition):
            if i == len(s):
                # 递归出口, 找到一个有效的划分, 将其加入最终结果
                res.append(partition)
                return
            for j in range(i, len(s)):
                if isPalindrome(i, j):
                    # s[i:j+1]子串是回文, 可以将其加入当前划分中
                    # 然后从j+1开始继续递归
                    dfs(j + 1, partition + [s[i : j + 1]])

        dfs(0, [])
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
