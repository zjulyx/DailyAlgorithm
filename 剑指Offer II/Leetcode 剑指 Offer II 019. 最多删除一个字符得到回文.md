> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/RQku0D)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个非空字符串  s，请判断如果   最多 从字符串中删除一个字符能否得到一个回文字符串。

### 示例 1:

- 输入: s = "aba"
- 输出: true

### 示例 2:

- 输入: s = "abca"
- 输出: true
- 解释: 可以删除 "c" 字符 或者 "b" 字符

### 示例 3:

- 输入: s = "abc"
- 输出: false

### 提示:

- 1 <= s.length <= 10^5
- s 由小写英文字母组成

## 题目思考

1. 注意数据规模很大, 如果直接删除每个字符再验证的话需要 O(N^2)时间, 肯定会超时
2. 怎么利用只删除 1 个字符这个条件?

## 解决方案

### 方案 1

#### 思路

- 回顾双指针判断字符串是否回文的过程, 此题加入了可以删除一个字符的可选项
- 所以可以递归处理, 在原来的基础上加入一个 flag, 判断当前是否还能删除
- 如果遇到不相等的字符时, 可以左边指针往右移动一位, 或者右边向左移动一位, 然后把 flag 置为 false 即可, 再遇到不相等的就说明不能变成回文了
- 递归传入左边界和右边界表示双指针, 注意递归出口
- 该方案优点是代码非常简洁, 思路也比较直观, 缺点是需要额外的空间消耗

#### 复杂度

- 时间复杂度 O(N): 遇到不相等的字符时, 需要再判断两次, 所以总时间是 O(2N) = O(N)
- 空间复杂度 O(N): 递归的栈的消耗

#### 代码

```python
class Solution:
    def validPalindrome(self, s: str) -> bool:
        def check(b, e, canDelete):
            if b >= e:
                return True
            if s[b] == s[e]:
                return check(b + 1, e - 1, canDelete)
            elif canDelete:
                # 还有一次删除机会, 左指针右移或者右指针左移, 同时更新flag保证之后的判断不能再删除字符了
                return check(b + 1, e, False) or check(b, e - 1, False)
            else:
                # 遇到不相等的字符且不能再删除了, 直接返回false
                return False

        return check(0, len(s) - 1, True)
```

### 方案 2

#### 思路

- 回顾方案 1, 我们是把迭代做法的双指针改成了递归版本, 那可不可以继续迭代搞定呢? 这样就不需要递归的栈的空间消耗了.
- 答案也是肯定的, 我们可以把在不能删除字符前提下的判断回文的双指针算法给提取出来
- 然后从两边开始往中间遍历, 遇到不相等的字符时就先后将两边的指针各往中间移动一位, 对剩下的两个子字符串调用那个算法, 如果有一边是回文, 则说明符合要求
- 该方案优点是空间复杂度更小, 但缺点是代码没有方案 1 简洁

#### 复杂度

- 时间复杂度 O(N): 遇到不相等的字符时, 需要再判断两次, 所以总时间是 O(2N) = O(N)
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def validPalindrome(self, s: str) -> bool:
        b = 0
        e = len(s) - 1

        def isPalindrome(b, e):
            while b < e:
                if s[b] == s[e]:
                    b += 1
                    e -= 1
                else:
                    return False
            return True

        while b < e:
            if s[b] == s[e]:
                b += 1
                e -= 1
            else:
                return isPalindrome(b + 1, e) or isPalindrome(b, e - 1)
        return True
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
