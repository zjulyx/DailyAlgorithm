> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/qiu-12n-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

请设计一个机械累加器，计算从 1、2... 一直累加到目标数值 target 的总和。注意这是一个只能进行加法操作的程序，不具备乘除、if-else、switch-case、for 循环、while 循环，及条件判断语句等高级功能。

### 示例 1：

- 输入: target = 5
- 输出: 15

### 示例 2：

- 输入: target = 7
- 输出: 28

### 提示：

- 1 <= target <= 10000

## 题目思考

### 方案 1

#### 思路

- 题目限制很多, 条件判断/循环/乘除法都不能用, 常规高斯公式或者迭代循环的方案直接 GG 了
- 既然显式循环 for/while 不能用, 试试隐式循环?
- 我们可以利用递归的思路, 每次传入 target-1 递归调用, 这样也达到了循环的目的
- 但是这又引入一个新问题, 递归出口怎么办呢? 正常情况下应该是到 target=1 就直接返回 1, 但需要 if 判断, 如何在不使用 if 的情况下达到同样目的呢?
- 其实条件判断还可以用逻辑运算符来实现, 它们具有短路特性, 也就是说如果前面的条件已经构成最终结果了, 就不再判断后面的条件 (比如 and 的前面条件是 False, 以及 or 的前面条件是 True)
- 所以这里我们可以将递归出口改为判断 target 是否为 0, 然后与后面的递归调用 and 起来即可, 这样就做到调用到 0 的时候就不再执行递归调用了
- 对于 python 3 而言, and 会返回第一个为 0/False/None 的结果, 如果每一项都不是的话则返回最后一项, 所以这里直接返回 and 表达式的结果即可

#### 复杂度

- 时间复杂度 O(N): 需要遍历 N 个数字
- 空间复杂度 O(N): 递归栈的消耗

#### 代码

```python
class Solution:
    def mechanicalAccumulator(self, target: int) -> int:
        # 方法1: 递归 + and的短路特性
        # python 3 中 a and b and c 会返回第一个为0/None/False的变量, 如果都不是则返回最后的变量
        return target and target + self.mechanicalAccumulator(target - 1)
```

### 方案 2

#### 思路

- 回顾方案 1, 虽然代码很简洁, 但是还是需要 O(N)时间和空间, 有没有更高效的方案呢?
- 答案也是肯定的, 回忆高斯公式 `target*(target+1)/2`, 唯一不满足条件的是乘法, 因为除以 2 可以用右移代替, 所以我们只要找到乘法的替代方案即可, 这就是方案 2 的核心
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
    def mechanicalAccumulator(self, target: int) -> int:
        # 方法2: 快速乘法, 二进制移位乘法
        A, B = target, target + 1

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

        return quickMultiply2(target, target + 1) >> 1
```
