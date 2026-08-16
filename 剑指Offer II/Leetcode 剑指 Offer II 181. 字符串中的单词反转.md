> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/fan-zhuan-dan-ci-shun-xu-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

你在与一位习惯从右往左阅读的朋友发消息，他发出的文字顺序都与正常相反但单词内容正确，为了和他顺利交流你决定写一个转换程序，把他所发的消息 message 转换为正常语序。

注意：输入字符串 message 中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。

### 示例 1：

- 输入: message = "the sky is blue"
- 输出: "blue is sky the"

### 示例 2：

- 输入: message = " hello world! "
- 输出: "world! hello"
- 解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。

### 示例 3：

- 输入: message = "a good example"
- 输出: "example good a"
- 解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

### 提示：

- 0 <= message.length <= 104
- message 中包含英文大小写字母、空格和数字

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
    def reverseMessage(self, message: str) -> str:
        # 方法1: 使用内置split和join函数
        # split => 翻转 => 空格join
        return ' '.join(message.split()[::-1])
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
    def reverseMessage(self, message: str) -> str:
        # 方法2: 从右向左遍历, 模拟整个过程
        cur = ''
        res = ''

        def addCurrentWord(cur):
            nonlocal res
            # 将当前单词翻转
            cur = cur[::-1]
            # 如果不是第一个单词, 先加空格再加当前单词
            res = cur if not res else res + ' ' + cur

        for c in message[::-1]:
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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/message?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
