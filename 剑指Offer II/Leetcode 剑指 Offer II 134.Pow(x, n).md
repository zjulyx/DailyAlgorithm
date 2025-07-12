> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

实现 pow(x, n) ，即计算 x 的 n 次幂函数（即，xn）。

### 示例 1：

- 输入：x = 2.00000, n = 10
- 输出：1024.00000

### 示例 2：

- 输入：x = 2.10000, n = 3
- 输出：9.26100

### 示例 3：

- 输入：x = 2.00000, n = -2
- 输出：0.25000
- 解释：2^-2 = 1/2^2 = 1/4 = 0.25

### 提示：

-100.0 < x < 100.0
-2^31 <= n <= 2^31-1
-10^4 <= x^n <= 10^4

## 题目思考

1. 根据数据规模, 暴力循环显然是不可行的, 那有没有降低复杂度的方法, 大而化小来解决?
2. 需要考虑到哪些边界条件?
3. 如何避免重复的计算过程?

## 解决方案

### 方案 1

#### 思路

- 分治法, 将当前幂值二分来求解每一部分的结果, 然后乘起来就是当前的结果, 这样就需要使用 `logN` 的时间
- 注意将每次的计算结果保存起来, 避免重复计算
- 注意边界条件：`n == 0`, `n < 0`, `x == 0`

#### 复杂度

- 时间复杂度 `O(logN)`
  - 二分递归
- 空间复杂度 `O(logN)`
  - memo 数组还有递归栈需要存 `logN` 个元素

#### 代码

##### Python 3

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if x == 0:
            return 0
        # 初始化幂为0和1的结果, 作为递归出口
        memo = {0 : 1.0, 1 : x}

        def helper(n):
            if n < 0:
                return 1.0 / helper(-n)

            if n not in memo:
                half_n = n // 2
                # 二分分成两部分求乘积
                memo[n] = helper(half_n) * helper (n - half_n)

            return memo[n]

        return helper(n)
```

### 方案 2

#### 思路

- 经典快速幂
- 注意下面的^表示求幂符号, 不是异或
- 根据 `x^(2n) == (x^2)^n` 的性质, 快速将 n 收敛到 0
- 举例来说: 比如 `2^7 = (2^2)^3*2 = 4^3*2 = (4^2)^1*4*2 =16*4*2`
- 所以就是针对当前的幂值 n
  - 如果它是奇数, 那么最终结果要乘上当前 x 值
  - 如果它是偶数, 那么最终结果不需要乘
  - 然后 `x*=x`, `n/=2`(也可以选择右移一位)即可
  - 最终到 `n=0` 就结束循环
- 这样就能将空间复杂度降至 O(1)
- 进一步思考: 快速幂每次都要除以 2 吗? 针对不同的问题是不是也可以除以不同的数来优化? ==> [372-超级次方](https://leetcode-cn.com/problems/super-pow/)

#### 复杂度

- 时间复杂度 `O(logN)`
  - n 每次除以 2, `logN` 次就收敛到 0
- 空间复杂度 `O(1)`
  - 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if x == 0:
            return 0
        if n < 0:
            return 1 / self.myPow(x, -n)
        res = 1
        while n > 0:
            if n & 1:
                res *= x
            x *= x
            n >>= 1
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

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
