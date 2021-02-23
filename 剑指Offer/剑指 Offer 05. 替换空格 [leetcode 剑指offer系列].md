> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

> 今天继续更新剑指 offer 系列, 这道题相对比较简单, 但也有多种实现方案, 可以用来扩展思路. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **offer** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

请实现一个函数，把字符串 s 中的每个空格替换成`"%20"`。

0 <= s 的长度 <= 10000

## 题目样例

### 示例

#### 输入

`s = "We are happy."`

#### 输出

`"We%20are%20happy."`

## 题目思考

1. 题意很简单, 你能想到哪些思路?

## 解决方案

### 思路

#### 分析

- 需要将空格转成`%20`, 理论上只需要遍历一遍字符串, 直接转换即可
- 需要注意的是原来空格是一位字符, 转换后的是 3 个字符, 这就意味着如果需要遍历字符串的话, 原始字符串和目标字符串的下标不相同

#### 实现

> 这里抛砖引玉, 提供三种实现方案和对应的代码, 大家如果有别的方案的话也欢迎交流 😁

1. 利用语言特性
   - 大部分高级语言都提供了 split 和 join
   - 所以我们只需要按照空格作为分隔符将字符串 split 成字符串数组
   - 最后再用`%20`作为分隔符 join 起来即可
   - 这样一行代码即可搞定 😂
2. 利用 string 操作
   - 我们完全可以新建一个初始为空的目标字符串
   - 然后直接遍历原始字符串: 遇到空格追加`%20`; 而遇到其他字符直接追加字符本身即可
3. 利用字符数组
   - 这种方案更适用于类似 C 语言这种提供原地修改功能的字符数组, python 的话就需要使用 list 存字符来模拟了, 最后再 join 得到字符串
   - 首先遍历原始字符串, 统计空格的数目`blankcnt`
   - 然后申请一个长度为`blankcnt * 3 + (len - blankcnt)`长度的字符数组 (因为空格的长度要变为原来的 3 倍, 其他字符长度保持不变)
   - 最后维护两个下标同时遍历即可: 遇到普通字符按原样复制, 两下标都+1; 而遇到空格则依次填入`%`,`2`,`0`, 原始下标+1, 新下标+3

### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍或两遍字符串
- 空间复杂度 `O(N)`
  - 结果字符串至少需要有 N 个字符

### 代码方案 1 - 一行代码 (split+join)

```python
class Solution:
    def replaceSpace(self, s: str) -> str:
        # 方法1: split+join
        return '%20'.join(s.split(' '))
```

### 代码方案 2 - 利用 string

```python
class Solution:
    def replaceSpace(self, s: str) -> str:
        # 方法2: string追加
        res = ''
        for c in s:
            res += '%20' if c == ' ' else c
        return res
```

### 代码方案 3 - 利用字符数组

```python
class Solution:
    def replaceSpace(self, s: str) -> str:
        # 方法3: 字符数组, 预分配空间
        blankcnt = 0
        for c in s:
            if c == ' ':
                # 计算空格数目
                blankcnt += 1
        # 根据计算所得的目标字符数组的字符个数预分配空间
        res = ['']*(len(s)-blankcnt+blankcnt*3)
        i, j = 0, 0
        while i < len(s):
            if s[i] == ' ':
                # 原始字符是空格时, 进行转换, 新下标+3
                res[j] = '%'
                res[j+1] = '2'
                res[j+2] = '0'
                i += 1
                j += 3
            else:
                # 否则直接复制, 两个下标都+1
                res[j] = s[i]
                i += 1
                j += 1
        return ''.join(res)
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
