> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

> 今天继续更新剑指 offer 系列, 这道题又是相当经典, 这里提供一种非常通用的方法供大家参考, 它还能解决类似的问题, 比如数字的排列, 也不用考虑有重复元素的情况

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个字符串，打印出该字符串中字符的所有排列。

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

1 <= s 的长度 <= 8

## 题目样例

### 示例

输入：s = "abc"
输出：["abc","acb","bac","bca","cab","cba"]

## 题目思考

1. 如何不重复地得到所有排列?

## 解决方案

#### 思路

- 要想不重复地得到所有排列, 我们可以定义一个方法, 来求字典序的下一个排列, 正如题目示例中的输出数组的顺序那样
- 初始化为最小的字典序, 然后当遍历到最大的字典序的时候, 就说明所有排列都被找到了
- 所以算法的核心就是如何通过一个字符串找它的字典序的下一个排列
- 下一个排列一定是所有排列中大于当前字符串且最接近它的, 所以我们可以利用贪心算法, 具体步骤如下:
  1. 从后向前找第一个**小于后一个字符**的字符 i (因为如果大于等于后一个字符的话, 就没法与后面的字符交换来使得整体字符串更大了)
  2. 找刚才遍历的部分的大于且最接近 i 的字符 j
  3. 将它们两个互换
  4. 然后 i 往后的部分都按字典序从小到大排列
- 这样就保证了新的字符串一定是大于当前字符串且最接近它的, 不可能有更小的了
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N*N!)`
  - 最差情况下 N 个字符各不相同, 排列就有 `N!` 种, 从当前字符串转入下一个字符串最坏情况下需要 `O(N)` 时间
- 空间复杂度 `O(1)`
  - 只使用了几个变量 (不考虑输出结果集)

#### 代码

```python
class Solution:
    def permutation(self, s: str) -> List[str]:
        res = []

        def getNext(s):
            for i in range(len(s) - 1)[::-1]:
                # 从后向前查找
                if s[i] < s[i + 1]:
                    # 找到目标字符了, 接下来找后面部分中大于s[i]且最接近它的字符
                    j = i + 1
                    while j < len(s) and s[j] > s[i]:
                        j += 1
                    # s[j-1]就是后面部分中大于s[i]且最接近它的字符
                    j -= 1
                    # 单独拿出右边部分, 肯定严格按照降序排列
                    right = s[i + 1:j] + s[i] + s[j + 1:]
                    # 交换字符后, 并加上右边的翻转部分(增序, 最小字典序), 就是下一个字典序的字符串
                    return s[0:i] + s[j] + right[::-1]
            # 没找到下一个字符串, 说明当前字符串就是字典序最大的了, 直接返回None
            return None

        # 先拿到字典序最小的字符串
        s = ''.join(sorted(s))
        while s is not None:
            res.append(s)
            s = getNext(s)
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
