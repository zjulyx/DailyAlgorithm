> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某套连招动作记作仅由小写字母组成的序列 arr，其中 arr[i] 第 i 个招式的名字。请返回第一个只出现一次的招式名称，如不存在请返回空格。

### 示例 1：

- 输入：arr = "abbccdeff"
- 输出：'a'

### 示例 2：

- 输入：arr = "ccdd"
- 输出：' '

### 限制：

- 0 <= arr.length <= 50000

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
    def dismantlingAction(self, arr: str) -> str:
        # 字典存c => [firstIndex, cnt]
        # 这样只需要遍历一遍字符串
        d = {}
        for i, c in enumerate(arr):
            if c in d:
                # 只更新cnt
                d[c][1] += 1
            else:
                d[c] = [i, 1]
        res = ' '
        # 初始化最小下标为字符串长度, 有效下标肯定都小于它
        mnIndex = len(arr)
        for c in d:
            firstIndex, cnt = d[c]
            if cnt == 1 and firstIndex < mnIndex:
                mnIndex = firstIndex
                res = c
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
