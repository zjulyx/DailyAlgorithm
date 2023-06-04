> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/dKk3P7/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个字符串 s 和 t ，编写一个函数来判断它们是不是一组变位词（字母异位词）。

注意：若  s 和 t  中每个字符出现的次数都相同且字符顺序不完全相同，则称  s 和 t  互为变位词（字母异位词）。

### 示例  1:

- 输入: s = "anagram", t = "nagaram"
- 输出: true

### 示例 2:

- 输入: s = "rat", t = "car"
- 输出: false

### 示例 3:

- 输入: s = "a", t = "a"
- 输出: false

### 提示:

- 1 <= s.length, t.length <= 5 \* 10^4
- s and t  仅包含小写字母

## 题目思考

1. 如何统计字符串的字符出现次数？

## 解决方案

#### 思路

- 要想统计字符串, 我们可以利用计数字典或长度为 26 的数组(因为只包含小写字母)
- 然后遍历 s 的每个字符, 将对应计数加 1
- 接下来遍历 t 的每个字符, 将对应计数减 1
- 最后每个字符的计数都应该是 0, 这样才说明两个字符串的每个字符出现的次数都相同
- 对于 Python3 而言, 也可以直接使用 collections.Counter 来统计并比较, 这样可以简化到一行代码
- 另外可以进行一些优化, 先判断两字符串长度是否相等, 不等直接返回 False, 避免后面的耗时比较
- 而且特别注意题目要求两个字符串不能完全相同, 所以也要比较两个字符串本身, 相同的话直接返回 False

### 复杂度

- 时间复杂度 O(N): 需要统计每个字符
- 空间复杂度 O(C): C 是字符串的字符种类数

### 代码

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        # 两字符串长度相等&&自身不相同&&字符计数相等
        return len(s) == len(t) and s != t and collections.Counter(s) == collections.Counter(t)
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
