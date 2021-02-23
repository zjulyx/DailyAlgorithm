> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。

- s.length <= 40000

## 题目样例

### 示例

- 输入: "pwwkew"
- 输出: 3
- 解释: 因为无重复字符的最长子串是  "wke"，所以其长度为 3。请注意，你的答案必须是 子串 的长度，"pwke"  是一个子序列，不是子串。

## 题目思考

1. 如何通过一次遍历得出结果?
2. 如果统计当前子字符串的字符种类?

## 解决方案

### 思路

- 分析题目, 一个最简单的思路就是暴力法: 固定子字符串起点, 然后往后扩展, **因为不能含有重复, 所以可以使用集合统计当前子字符串的字符种类**, 直到发现重复字符或者到终点停止, 取最长的子字符串作为结果. 但这样需要两重遍历, 时间复杂度达到 `O(N*C)` (C 是字符的种类数目, 因为找到重复就会停下来, 所以不是 N^2), 不是很优
- 基于暴力法进行分析, 假设当前子字符串起点是 start, 发现重复字符的位置是 end, 然后对应的该字符上个下标是 dup, 显然`start <= dup < end`
- 此时暴力法的做法是将 start+1 重新开始遍历子字符串, 但这样做完全没有必要, 因为以`[start+1, dup]`中的任一下标作为起点的字符串肯定都会在 end 处停下来, 因为找到了重复的(end 和 dup), 而且这些子字符串长度必然小于以 start 为起点的
- 所以更优化的做法是**将起点向后遍历到 dup+1, 从字符集合中移除遍历过程中的字符, 然后将 dup+1 作为新的起点, 终点继续从 end 处开始遍历, 直到再次遇到重复字符**, 重复上述步骤即可
- 这样起点和终点都只需要遍历一遍, 相比暴力法有所优化
- 以上就是典型的滑动窗口的思想, 通常做法就是维护双指针代表窗口起点和终点, 然后根据当前窗口是否满足要求来进行不同的处理
- 下面的代码对必要步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 O(N): 起点和终点都只需要遍历一遍
- 空间复杂度 O(1): 只使用了几个变量

### 代码

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        # 滑动窗口+当前字符集合, 时刻更新res
        start = 0
        res = 0
        v = set()
        for end in range(len(s)):
            c = s[end]
            if c in v:
                # 发现重复了, start向后遍历找dup下标(即上一个c的下标)
                while start < end and s[start] != c:
                    v.remove(s[start])
                    start += 1
                # 此时dup = start, 需要将dup+1作为新的起点
                start += 1
            else:
                # 没有重复, 将当前字符加入字符集合中
                v.add(c)
                # 最大子字符串长度就是最大的字符集合的长度, 当然此处也可以用end-start+1代替
                res = max(res, len(v))
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
