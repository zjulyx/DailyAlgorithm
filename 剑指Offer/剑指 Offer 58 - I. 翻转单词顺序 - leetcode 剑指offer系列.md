> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

- 无空格字符构成一个单词。
- 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
- 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

## 题目样例

### 示例

- 输入: "the sky is blue"
- 输出: "blue is sky the"

- 输入: "  hello world!  "
- 输出: "world! hello"
- 解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。

## 题目思考

1. 如果要求不能使用语言内置函数, 该如何做?

## 解决方案

### 方案 1

#### 思路

- 根据题目描述, 我们可以直接利用语言内置的 split 和 join 函数来做, 这里以 python 3 为例
  1. 使用 split 函数, 这里不传入参数, 表示按照空字符作为分隔符将字符串隔开
  2. 翻转单词列表
  3. 使用 join 函数, 传入空格, 表示将单词列表按照空格分隔重新转成字符串即可
- 注意不传入任何参数的 split 会去掉连续的空白字符, 所以这时候就无需我们额外去除首尾或者单词之间多余的空格了, 感兴趣的同学可以参考[文档](https://docs.python.org/3/library/stdtypes.html?highlight=split#str.split)
- 这样 python 3 一行代码即可搞定, 将上述三个步骤合在一起..

#### 复杂度

- 时间复杂度 O(N): 只遍历了一遍字符串
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        # 方法1: 使用内置split和join函数
        # split => 翻转 => 空格join
        return ' '.join(s.split()[::-1])
```

### 方案 2

#### 思路

- 如果要求不能使用语言内置函数, 又该怎么做呢?
- 这时候我们只能老老实实地从右向左遍历
- 此时需要使用一个变量记录当前的单词(即连续的非空字符)
- 然后遇到空格时, **如果当前单词不为空**时 (因为可能会遇到连续的空格), 将当前单词**翻转** (因为单词遍历的时候也成倒序了) 并加入最终结果 res 中, 然后将当前单词重置为空
- 加入 res 时需要注意: 如果当前单词是第一个(res 为空的时候), 不加入空格, 否则先加空格再加这个单词, 保证不同单词以空格隔开
- **另外注意遍历结束后, 当前单词可能不为空, 也需要将其加入最终结果中**

#### 复杂度

- 时间复杂度 O(N): 只遍历了一遍字符串
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        # 方法2: 从右向左遍历, 模拟整个过程
        cur = ''
        res = ''

        def addCurrentWord(cur):
            nonlocal res
            # 将当前单词翻转
            cur = cur[::-1]
            # 如果不是第一个单词, 先加空格再加当前单词
            res = cur if not res else res + ' ' + cur

        for c in s[::-1]:
            if c == ' ':
                if cur:
                    # 当前单词非空, 将其加入结果中, 同时单词重置为空
                    addCurrentWord(cur)
                    cur = ''
            else:
                # 当前字符不是空格, 加入当前单词中
                cur += c
        if cur:
            # 当前单词非空, 将其加入结果中
            addCurrentWord(cur)
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
