> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/VabMRr)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个字符串 s 和 p，找到 s 中所有 p 的 变位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

变位词 指字母相同，但排列不同的字符串。

### 示例 1：

- 输入: s = "cbaebabacd", p = "abc"
- 输出: [0,6]
- 解释:
  - 起始索引等于 0 的子串是 "cba", 它是 "abc" 的变位词。
  - 起始索引等于 6 的子串是 "bac", 它是 "abc" 的变位词。

### 示例 2：

- 输入: s = "abab", p = "ab"
- 输出: [0,1,2]
- 解释:
  - 起始索引等于 0 的子串是 "ab", 它是 "ab" 的变位词。
  - 起始索引等于 1 的子串是 "ba", 它是 "ab" 的变位词。
  - 起始索引等于 2 的子串是 "ab", 它是 "ab" 的变位词。

### 提示:

- 1 <= s.length, p.length <= 3 \* 10^4
- s 和 p 仅包含小写字母

## 题目思考

1. 如何优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 不难发现这道题和上周的题目[剑指 Offer II 014. 字符串中的变位词](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484585&idx=1&sn=509940c185f4ed6bb6f846edd66c72e3&cur_album_id=2662717821826908161#rd)很类似, 只不过需要返回所有变位词的起点下标, 而不是判断变位词是否存在
- 所以我们可以利用相同的滑动窗口思路, 只需要稍微改变一下输出, 具体做法如下:
- 首先我们可以先比较 s1 和 s2 的长度, 如果 s2 更短, 则一定不可能包含 s1 的变位词, 直接返回空列表即可
- 然后创建两个计数字典 cnts1 (初始化为 s1 的字符计数)和 cnts2 (初始化为空)
- 同时维护一个尚未匹配的字符个数 unmatch, 用于加速判断窗口的有效性, 且其值初始化为 s1 的长度, 因为还没开始遍历 s2
- 接下来开始遍历 s2 的滑动窗口右边界, 并将 cnts2 中对应字符的计数加 1, 然后分两种情况更新 unmatch:
  - 如果加 1 之后的 cnts2 计数小于等于 cnts1 计数, 则说明找了一个匹配, unmatch 减 1;
  - 否则说明 s2 多了一个该字符, unmatch 要加 1
- 如果当前右边界达到了 s1 的长度, 则需要开始移除上一个左边界, 因为窗口长度是固定的 s1 的长度
- 我们需要将 cnts2 中上个左边界对应字符的计数减 1, 然后同样分两种情况更新 unmatch:
  - 如果减 1 之后的计数 cnts2 计数大于等于 cnts1 计数, 则说明移除了一个 s2 的多余字符, unmatch 减 1;
  - 否则说明移除后 s1 又多了一个未匹配的该字符, unmatch 要加 1
- 在每次 unmatch 更新完毕后, 我们都检查它是否变为 0, 是的话则说明所有字符都得到了匹配, 找到一个变位词, 将其起点加入最终结果列表即可
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 假设 M 是 s1 的长度, N 是 s2 的长度, 只有 N>=M 时才需要遍历 s2 的 N 个字符
- 空间复杂度 `O(M)`: 两个计数字典最多都只有 M 个元素

#### 代码

```python
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        if len(s) < len(p):
            # s更短, 一定没有满足条件的子串
            return []
        res = []
        # 初始化p计数字典
        cntp = collections.Counter(p)
        # 初始化s计数字典为空, 因为尚未添加s的字符
        cnts = collections.Counter()
        # 初始化未匹配数目是p的长度
        unmatch = len(p)
        le1, le2 = len(p), len(s)
        for r in range(le2):
            # 遍历滑动窗口右边界
            rc = s[r]
            cnts[rc] += 1
            if cnts[rc] <= cntp[rc]:
                # 找到一个匹配
                unmatch -= 1
            else:
                # s多了一个rc字符
                unmatch += 1
            if r >= le1:
                # 需要移除上个左边界
                lc = s[r - le1]
                cnts[lc] -= 1
                if cnts[lc] >= cntp[lc]:
                    # 移除了一个 s 的多余字符lc
                    unmatch -= 1
                else:
                    # 移除后p多了一个未匹配的lc
                    unmatch += 1
            if unmatch == 0:
                # 找到一个有效变位词子串s[r-le1+1:r+1], 将其起点r-le1+1加入结果列表
                res.append(r - le1 + 1)
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
