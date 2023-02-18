> 题目难度: 困难

> [原题链接](https://leetcode.cn/problems/M1oyTv)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个字符串 s 和  t 。返回 s 中包含  t  的所有字符的最短子字符串。如果 s 中不存在符合条件的子字符串，则返回空字符串 "" 。

如果 s 中存在多个符合条件的子字符串，返回任意一个。

注意： 对于 t 中重复字符，我们寻找的子字符串中该字符数量必须不少于 t 中该字符数量。

### 示例 1：

- 输入：s = "ADOBECODEBANC", t = "ABC"
- 输出："BANC"
- 解释：最短子字符串 "BANC" 包含了字符串 t 的所有字符 'A'、'B'、'C'

### 示例 2：

- 输入：s = "a", t = "a"
- 输出："a"

### 示例 3：

- 输入：s = "a", t = "aa"
- 输出：""
- 解释：t 中两个字符 'a' 均应包含在 s 的子串中，因此没有符合条件的子字符串，返回空字符串。

### 提示：

- 1 <= s.length, t.length <= 10^5
- s 和 t 由英文字母组成
- 进阶：你能设计一个在 o(n) 时间内解决此问题的算法吗？

## 题目思考

1. 如何在 o(n) 时间内解决此问题?

## 解决方案

#### 思路

- 分析题目, 不难发现这道题和之前的题目[剑指 Offer II 014. 字符串中的变位词](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484585&idx=1&sn=509940c185f4ed6bb6f846edd66c72e3&cur_album_id=2662717821826908161#rd)很类似, 只不过这里的子串字符数不是严格相等, 而是只需要包含目标字符串的所有字符即可
- 所以我们可以举一反三, 利用相同的滑动窗口思路, 只需要稍微改变一下判断条件和输出, 具体做法如下:
- 首先我们可以先比较 s 和 t 的长度, 如果 s 更短, 则一定不可能包含 t 的全部字符, 直接返回空字符串即可
- 然后创建两个计数字典 cntt (初始化为 t 的字符计数)和 cnts (初始化为空)
- 同时维护一个尚未匹配的字符个数 unmatch, 用于加速判断窗口的有效性, 且其值初始化为 t 的长度, 因为还没开始遍历 s
- 接下来开始遍历 s 的滑动窗口右边界, 并将 cnts 中对应字符的计数加 1, 然后更新 unmatch: 如果加 1 之后的 cnts 计数小于等于 cntt 计数, 则说明找到一个有效包含字符, unmatch 减 1
- 如果当前 unmatch 变成了 0, 则说明当前窗口包含了 t 的所有字符, 需要开始移除左边界, 因为题目要求最短子串
- 我们需要将 cnts 中当前左边界对应字符的计数减 1, 然后左边界右移, 并更新 unmatch: 如果减 1 之后的计数 cnts 计数小于 cntt 计数, 说明移除后 t 又多了一个未匹配的该字符, unmatch 要加 1
- 当 unmatch 大于 0 时, 则说明前一个左边界到当前右边界对应的窗口是以当前右边界作为终点的最短子串, 将其与最终结果比较, 并将最终结果更新为两者中的较小值
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 假设 M 是 t 的长度, N 是 s 的长度, 只有 N>=M 时才需要遍历 s 的 N 个字符
- 空间复杂度 `O(N)`: 最多存储 N 个元素

#### 代码

```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        # 滑窗+双计数字典+unmatch变量
        if len(s) < len(t):
            # s更短, 一定没有满足条件的子串
            return ""
        # 初始化t计数字典
        cntt = collections.Counter(t)
        # 初始化s计数字典为空, 因为尚未添加s的字符
        cnts = collections.Counter()
        # 初始化未匹配数目是t的长度
        unmatch = len(t)
        l = 0
        res = ""
        for r, c in enumerate(s):
            # 遍历滑动窗口右边界
            cnts[c] += 1
            if cnts[c] <= cntt[c]:
                # 新增一个有效包含字符
                unmatch -= 1
            if unmatch == 0:
                # 当前窗口包含t的全部字符, 开始移动左边界
                while unmatch == 0:
                    pc = s[l]
                    cnts[pc] -= 1
                    if cnts[pc] < cntt[pc]:
                        # 减少一个有效包含字符
                        unmatch += 1
                    l += 1
                # 此时s[l-1:r+1]就是以r为终点的最短有效子串
                if res == "" or len(s[l - 1 : r + 1]) < len(res):
                    res = s[l - 1 : r + 1]
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
