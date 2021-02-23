> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

0 <= s 的长度 <= 50000

## 题目样例

### 示例

- s = "abaccdeff"
- 返回 "b"

- s = ""
- 返回 " "

## 题目思考

1. 如何遍历一遍字符串就得到结果?

## 解决方案

#### 思路

- 一个比较容易想到的思路是使用一个计数字典, 遍历一遍字符串存每个字符的数目, 然后把数目为 1 的字符单独放在集合中, 再从头遍历一遍字符串, 找第一个在集合中的字符, 没有的话就返回' '
- 但这种做法需要遍历两遍字符串, 效率不高, 如何做到只遍历一遍字符串就得到结果呢?
- 重新思考, 如果我们遍历的过程中额外存第一个下标, 然后后续遇到该字符时只更新计数, 这样是不是就无需再次遍历了呢?
- 也就是说, 将计数字典改造为 `c => [firstIndex, cnt]` 这样的 kv 组合, 最后只需要找`cnt==1` 且 firstIndex 最小的 c 即可

#### 复杂度

- 时间复杂度 O(N+C): 只需要遍历一遍字符串, 之后遍历字典的复杂度为 O(C) (C 是字符种类数, 常数 26)
- 空间复杂度 O(C): 使用了一个字典, 存 C 个 kv

#### 代码

```python
class Solution:
    def firstUniqChar(self, s: str) -> str:
        # 字典存c => [firstIndex, cnt]
        # 这样只需要遍历一遍字符串
        d = {}
        for i, c in enumerate(s):
            if c in d:
                # 只更新cnt
                d[c][1] += 1
            else:
                d[c] = [i, 1]
        res = ' '
        # 初始化最小下标为字符串长度, 有效下标肯定都小于它
        mnIndex = len(s)
        for c in d:
            firstIndex, cnt = d[c]
            if cnt == 1 and firstIndex < mnIndex:
                mnIndex = firstIndex
                res = c
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
