> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字 2，该函数将返回左旋转两位得到的结果"cdefgab"。

- 1 <= k < s.length <= 10000

## 题目样例

### 示例

- 输入: s = "abcdefg", k = 2
- 输出: "cdefgab"

- 输入: s = "lrloseumgh", k = 6
- 输出: "umghlrlose"

## 题目思考

1. 如果要求不能使用语言内置函数, 该如何做?

## 解决方案

### 方案 1

#### 思路

- 根据题目描述, 使用 python 的话完全可以直接利用切片, 一行代码来搞定..

#### 复杂度

- 时间复杂度 O(N): 切片的时间复杂度
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        # 切片
        return s[n:] + s[0:n]
```

### 方案 2

#### 思路

- 如果要求不能使用语言内置函数, 又该怎么做呢?
- 很显然我们可以从下标为 k 的地方开始遍历, 将后面的字符依次加入结果中
- 遍历到末尾后, 再从 0 开始遍历到 k, 将这部分字符再加入结果中即可
- 这种方案跟语言无关, 比较通用, 只是通常情况下没必要, 因为内置操作会有一些优化, 一般会比自己实现的效率高..

#### 复杂度

- 时间复杂度 O(N): 每个字符只遍历一次
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def reverseLeftWords(self, s: str, n: int) -> str:
        res = ''
        for i in range(n, len(s)):
            res += s[i]
        for i in range(n):
            res += s[i]
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
