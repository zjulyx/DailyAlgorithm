> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/aseY1I/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

- 给定一个字符串数组 words，请计算当两个字符串 words[i] 和 words[j] 不包含相同字符时，它们长度的乘积的最大值。假设字符串中只包含英语的小写字母。如果没有不包含相同字符的一对字符串，返回 0。

### 示例 1:

- 输入: words = ["abcw","baz","foo","bar","fxyz","abcdef"]
- 输出: 16
- 解释: 这两个单词为 "abcw", "fxyz"。它们不包含相同字符，且长度的乘积最大。

### 示例 2:

- 输入: words = ["a","ab","abc","d","cd","bcd","abcd"]
- 输出: 4
- 解释: 这两个单词为 "ab", "cd"。

### 示例 3:

- 输入: words = ["a","aa","aaa","aaaa"]
- 输出: 0
- 解释: 不存在这样的两个单词。

### 提示：

- 2 <= words.length <= 1000
- 1 <= words[i].length <= 1000
- words[i] 仅包含小写字母

## 题目思考

1. 如何利用『字符串中只包含英语的小写字母』这一条件?
2. 如何尽可能优化时间复杂度?

## 解决方案

### 思路

- 分析题目, 最容易想到的做法就是暴力两重循环, 依次判断每对字符串是否包含相同字符
- 但这样时间复杂度来到了`O(N*N*C)` (C 是每个字符串的平均长度), 结合题目规模, 是 10^9, 大概率超时, 如何尽可能优化时间复杂度呢?
- 重新分析题目, 我们可以发现, 给定的字符串都只包含英语的小写字母, 最多 26 种
- 所以我们可以先进行一次预处理, 将每个字符串转换成对应的位图, 例如 abd 就是 0b1011 (省略更高位的 0)
- 这样在判断是否包含相同字符时, 就不需要再遍历整个字符串, 直接利用位图求与即可, 如果结果是 0, 则说明不包含相同字符
- 这还没完, 注意题目中要求的只是『最大乘积』, 所以我们并不需要检查那些明显不符合的字符串
- 例如 abd 和 aabdd, 它们对应的位图都是 0b1011, 但显然 abd 不可能构成最终结果, 因为 aabdd 更长
- 所以我们可以额外维护一个位图到最大长度的字典, 这样在判断时只需要将对应的最大长度乘起来即可, 此时如果 输入里面存在很多类似 abd 和 aabdd 的情况, 就可以进一步优化时间
- 下面代码中对上述每个步骤都有详细注释, 方便大家理解

### 复杂度

- 时间复杂度 `O(N*C + N*N)`: 预处理部分, 需要遍历一遍字符串的每个字符, 所以是 `O(N*C)`; 判断部分, 需要两重循环, 而位运算求与部分是 `O(1)`, 所以是 `O(N*N)`
- 空间复杂度 `O(N)`: 最多需要存储 N 个元素

### 代码

```python
class Solution:
    def maxProduct(self, words: List[str]) -> int:
        def getWordMask(word):
            # 优化1: 将字符串转成26位的位图, 这样只需要O(1)的位运算即可判断两个单词是否包含共同字符
            mask = 0
            for c in word:
                mask |= 1 << (ord(c) - ord("a"))
            return mask

        # 优化2: 对于同一个位图, 只需要维护其最大长度 (较小的长度一定不可能是最终结果的乘数)
        mask2len = collections.defaultdict(int)
        for word in words:
            mask = getWordMask(word)
            mask2len[mask] = max(mask2len[mask], len(word))
        masks = list(mask2len.keys())
        res = 0
        # 两重循环判断两个位图求交是否为0, 是的话则说明不包含共同字符
        for i in range(len(masks)):
            mask1 = masks[i]
            for j in range(i + 1, len(masks)):
                mask2 = masks[j]
                if (mask1 & mask2) == 0:
                    # 更新最终结果为乘积的最大值
                    res = max(res, mask2len[mask1] * mask2len[mask2])
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
