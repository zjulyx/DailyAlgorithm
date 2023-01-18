> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/MPnaiL)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个字符串 s1 和 s2，写一个函数来判断 s2 是否包含 s1 的某个变位词。

换句话说，第一个字符串的排列之一是第二个字符串的 子串 。

### 示例 1：

- 输入: s1 = "ab" s2 = "eidbaooo"
- 输出: True
- 解释: s2 包含 s1 的排列之一 ("ba").

### 示例 2：

- 输入: s1= "ab" s2 = "eidboaoo"
- 输出: False

### 提示：

- 1 <= s1.length, s2.length <= 10^4
- s1 和 s2 仅包含小写字母

## 题目思考

1. 如何优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 一个很容易想到的方案就是暴力两重循环:
  - 外层循环 s2 的每个下标作为起点
  - 内层循环从该起点开始, 遍历 s2 的 s1 长度个字符
  - 然后记录其每个字符的计数, 并与 s1 字符计数比较
  - 相等时则说明 s2 包含 s1 的某个变位词
- 但上述方案复杂度是 O(MN), 根据题目数据规模有可能会超时, 如何优化效率呢?
- 包含问题一般可以采用滑动窗口的思路, 这道题也不例外, 只是需要一些额外的数据结构
- 首先我们可以先比较 s1 和 s2 的长度, 如果 s2 更短, 则一定不可能包含 s1 的变位词, 直接返回 false 即可
- 然后创建两个计数字典 cnts1 (初始化为 s1 的字符计数)和 cnts2 (初始化为空)
- 同时维护一个尚未匹配的字符个数 unmatch, 用于加速判断窗口的有效性, 且其值初始化为 s1 的长度, 因为还没开始遍历 s2
- 接下来开始遍历 s2 的滑动窗口右边界, 并将 cnts2 中对应字符的计数加 1, 然后分两种情况更新 unmatch:
  - 如果加 1 之后的 cnts2 计数小于等于 cnts1 计数, 则说明找了一个匹配, unmatch 减 1;
  - 否则说明 s2 多了一个该字符, unmatch 要加 1
- 如果当前右边界达到了 s1 的长度, 则需要开始移除上一个左边界, 因为窗口长度是固定的 s1 的长度
- 我们需要将 cnts2 中上个左边界对应字符的计数减 1, 然后同样分两种情况更新 unmatch:
  - 如果减 1 之后的计数 cnts2 计数大于等于 cnts1 计数, 则说明移除了一个 s2 的多余字符, unmatch 减 1;
  - 否则说明移除后 s1 又多了一个未匹配的该字符, unmatch 要加 1
- 在每次 unmatch 更新完毕后, 我们都检查它是否变为 0, 是的话则说明所有字符都得到了匹配, 返回 true 即可
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 假设 M 是 s1 的长度, N 是 s2 的长度, 只有 N>=M 时才需要遍历 s2 的 N 个字符
- 空间复杂度 `O(M)`: 两个计数字典最多都只有 M 个元素

#### 代码

```python
class Solution:
    def checkInclusion(self, s1: str, s2: str) -> bool:
        # 滑窗+双计数字典+unmatch变量
        if len(s2) < len(s1):
            # s2更短, 一定不可能
            return False
        # 初始化s1计数字典
        cnts1 = collections.Counter(s1)
        # 初始化s2计数字典为空, 因为尚未添加s2的字符
        cnts2 = collections.Counter()
        # 初始化未匹配数目是s1的长度
        unmatch = len(s1)
        le1, le2 = len(s1), len(s2)
        for r in range(le2):
            # 遍历滑动窗口右边界
            rc = s2[r]
            cnts2[rc] += 1
            if cnts2[rc] <= cnts1[rc]:
                # 找到一个匹配
                unmatch -= 1
            else:
                # s2多了一个rc字符
                unmatch += 1
            if r >= le1:
                # 需要移除上个左边界
                lc = s2[r - le1]
                cnts2[lc] -= 1
                if cnts2[lc] >= cnts1[lc]:
                    # 移除了一个 s2 的多余字符lc
                    unmatch -= 1
                else:
                    # 移除后s1多了一个未匹配的lc
                    unmatch += 1
            if unmatch == 0:
                # 找到一个有效变位词子串
                return True
        # 遍历完毕后仍未找到有效变位词子串
        return False
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
