> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/XltzEq)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个字符串 s ，验证 s  是否是   回文串  ，只考虑字母和数字字符，可以忽略字母的大小写。

本题中，将空字符串定义为有效的   回文串  。

### 示例 1:

- 输入: s = "A man, a plan, a canal: Panama"
- 输出: true
- 解释："amanaplanacanalpanama" 是回文串

### 示例 2:

- 输入: s = "race a car"
- 输出: false
- 解释："raceacar" 不是回文串

### 提示：

- 1 <= s.length <= 2 \* 10^5
- 字符串 s 由 ASCII 字符组成

## 题目思考

1. 如何做到只考虑字母和数字, 且忽略大小写?

## 解决方案

#### 思路

- 我们先从最基础的回文问题分析, 最经典的思路就是使用头尾双指针, 具体做法如下:
  - 初始化双指针分别指向字符串开头和结尾
  - 然后逐步向中间靠拢, 依次判断当前两个字符是否相等, 不相等时说明不是回文
  - 这样我们就能在 O(N)时间内完成回文判断
- 不过这道题目额外加了一些条件, 我们只考虑字母和数字, 且忽略大小写
- 换句话说, 我们可以在比较当前两个指针前, 先使用 while 循环跳过连续非字母和数字字符
- 对于 Python 来说, 可以利用 isalnum()判断, 其他语言也有类似的方法
- 接下来比较两个指针对应的字符时, 统一将它们转成小写 (Python 的 lower()函数), 这样就做到了忽略大小写
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 只需要遍历每个字符一遍
- 空间复杂度 `O(1)`: 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        # 双指针+循环时跳过其他字符+比较lower
        i, j = 0, len(s) - 1
        while i < j:
            while i < j and not s[i].isalnum():
                # isalnum()用于判断当前字符是否是字母或数字, 跳过非字母数字字符
                i += 1
            while i < j and not s[j].isalnum():
                j -= 1
            if s[i].lower() != s[j].lower():
                # 使用lower得到小写字母, 因为题目不区分大小写
                # 数字字符的lower()仍是自身
                return False
            i += 1
            j -= 1
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
