> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/qiu-12n-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

求 1+2+...+n ，要求不能使用乘除法、for、while、if、else、switch、case 等关键字及条件判断语句（A?B:C）。

- 1 <= n <= 10000

## 题目样例

### 示例

- 输入: n = 3
- 输出: 6

- 输入: n = 9
- 输出: 45

## 题目思考

1. 除了 for 和 while, 还有哪些方式可以达到循环的目的?
2. 时间复杂度可以小于 O(N)吗?

## 解决方案

### 方案 1

#### 思路

- 题目限制很多, 条件判断/循环/乘除法都不能用, 常规高斯公式或者迭代循环的方案直接 GG 了
- 既然显式循环 for/while 不能用, 试试隐式循环?
- 我们可以利用递归的思路, 每次传入 n-1 递归调用, 这样也达到了循环的目的
- 但是这又引入一个新问题, 递归出口怎么办呢? 正常情况下应该是到 n=1 就直接返回 1, 但需要 if 判断, 如何在不使用 if 的情况下达到同样目的呢?
- 其实条件判断还可以用逻辑运算符来实现, 它们具有短路特性, 也就是说如果前面的条件已经构成最终结果了, 就不再判断后面的条件 (比如 and 的前面条件是 False, 以及 or 的前面条件是 True)
- 所以这里我们可以将递归出口改为判断 n 是否为 0, 然后与后面的递归调用 and 起来即可, 这样就做到调用到 0 的时候就不再执行递归调用了
- 对于 python 3 而言, and 会返回第一个为 0/False/None 的结果, 如果每一项都不是的话则返回最后一项, 所以这里直接返回 and 表达式的结果即可

#### 复杂度

- 时间复杂度 O(N): 需要遍历 N 个数字
- 空间复杂度 O(N): 递归栈的消耗

#### 代码

```python
class Solution:
    def sumNums(self, n: int) -> int:
        # 方法1: 递归 + and的短路特性
        # python 3 中 a and b and c 会返回第一个为0/None/False的变量, 如果都不是则返回最后的变量
        return n and n + self.sumNums(n - 1)
```

### 方案 2

#### 思路

- 回顾方案 1, 虽然代码很简洁, 但是还是需要 O(N)时间和空间, 有没有更高效的方案呢?
- 答案也是肯定的, 回忆高斯公式 `n*(n+1)/2`, 唯一不满足条件的是乘法, 因为除以 2 可以用右移代替, 所以我们只要找到乘法的替代方案即可, 这就是方案 2 的核心
- 求 `A*B` 的时候, 我们可以利用位运算的思路, 将 B 按照二进制展开, 然后对于它的第 i 位来说, 如果它是 1, 就在最终乘积中加上`A*(1<<i)`, 也即加上`A<<i`, 这样就只需要移位和加法操作即可
- 而循环部分, 我们仍然可以采用方案 1 的思路, 利用递归来代替显式的 for/while 循环, 来处理 B 的每一位
- 以上就是经典的**快速乘**算法, 显然我们只需要遍历`logN`位即可, 相比方案 1 效率有很大提升
- 下面代码对必要的步骤有详细的解释, 特别是不太好理解的循环转递归的过程, 方便大家参考. 相应的, 我也把正常的显式循环快速乘法写了下来, 可读性会好很多, 大家可以对比两种实现方式~

#### 复杂度

- 时间复杂度 O(logN): 需要遍历 logN 位
- 空间复杂度 O(logN): 递归栈的消耗

#### 代码

```python
class Solution:
    def sumNums(self, n: int) -> int:
        # 方法2: 快速乘法, 二进制移位乘法
        A, B = n, n + 1

        def quickMultiply(i):
            # i表示当前处理到B的二进制第i位
            # B>>i作为递归出口, 为0后表示当前的i已经超过了B的最高位(B是正数, 所以可以这样..), 就不再继续递归
            # quickMultiply(i + 1)得到第i+1位以及之后的和
            # 而对于B的当前第i位, 需要判断其是否为0, 仍然利用and短路特性, 得到A(该位是1)或者0(该位是0), 然后左移i位累加到结果中即可
            return B >> i and (quickMultiply(i + 1) + ((B &
                                                        (1 << i) and A) << i))

        return quickMultiply(0) >> 1

        # 快速乘法的常规实现
        def quickMultiply2(A, B):
            res = 0
            for i in range(32):
                if B < 1 << i:
                    break
                if B & 1 << i:
                    res += A << i
            return res

        return quickMultiply2(n, n + 1) >> 1
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
