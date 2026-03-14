> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

闯关游戏需要破解一组密码，闯关组给出的有关密码的线索是：

一个拥有密码所有元素的非负整数数组 password
密码是 password 中所有元素拼接后得到的最小的一个数
请编写一个程序返回这个密码。

### 示例 1：

- 输入：password = [15, 8, 7]
- 输出："1578"

### 示例 2：

- 输入：password = [0, 3, 30, 34, 5, 9]
- 输出："03033459"

### 提示：

- 0 < password.length <= 100

### 说明:

- 输出结果可能非常大，所以你需要返回一个字符串而不是整数
- 拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

## 题目思考

1. 怎么定义最小?

## 解决方案

### 思路

- 分析题目, 要使得拼接起来的数字最小, 只有当各个拼接元素按照从小到大拼接起来才可以
- 而这里的从小到大拼接, 不是指按照数字本身顺序或者字典序从小到大拼接
- 举个例子, 90 和 902 的拼接结果应该是 90290, 但是 90 不管是数字还是字典序都小于 902
- 那如何定义哪个数字作为拼接后的第一个数字呢?
- 假如这个数字和任意其他数字拼接起来, 都小于将两个数字互换后拼接的结果, 那么显然这个数字就应该放在首位
- 到这里就知道了, 我们应该自定义一个排序方法, 对于两个数字 a 和 b 而言, 比较 `str(a)+str(b)` 与 `str(b)+str(a)` 的大小关系, 然后对整个列表排序后组成一个字符串即可
- 下面的代码使用两种方案实现, 一种是快速排序, 一种是语言内置排序, 供大家参考

### 复杂度

- 时间复杂度 `O(NlogN)`
  - 排序的时间复杂度
- 空间复杂度 `O(N)`
  - 使用了额外的字符串数组

### 代码

#### 方案 1 - 自定义快速排序

```python
class Solution:
    def crackPassword(self, password: List[int]) -> str:
        # 使用map将原数组的数字转成字符串
        password = list(map(str, password))

        def quicksort(l, r):
            # 经典快速排序实现
            if l >= r:
                return
            pivot = password[l]
            i, j = l, r
            while i < j:
                # 只需要把这里改成自定义的排序方法即可
                while i < j and password[j] + pivot >= pivot + password[j]:
                    j -= 1
                password[i] = password[j]
                # 只需要把这里改成自定义的排序方法即可
                while i < j and password[i] + pivot <= pivot + password[i]:
                    i += 1
                password[j] = password[i]
            password[i] = pivot
            quicksort(l, i - 1)
            quicksort(i + 1, r)

        quicksort(0, len(password) - 1)
        return ''.join(password)
```

#### 方案 2 - 自定义内置排序

```python
class Solution:
    def crackPassword(self, password: List[int]) -> str:
        class compare(str):
            def __lt__(self, x):
                return self + x < x + self

        # 使用python内置sorted的key, 传入一个重载__lt__的类, 自定义排序
        return ''.join(sorted(map(str, password), key=compare))
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
