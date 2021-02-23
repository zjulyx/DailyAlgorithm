> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/powx-n/)

> 扩展思考: [372-超级次方](https://leetcode-cn.com/problems/super-pow/), 感兴趣的同学可以尝试下这道题, 看看能不能扩展一下思路, 举一反三?

## 题目描述

实现 [pow(_x_, _n_)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数。

- -100.0 < x < 100.0
- n 是 32 位有符号整数，其数值范围是 [−2^31, 2^31 − 1] 。

## 题目样例

### 示例 1

#### 输入

2.00000, 10

#### 输出

1024.00000

### 示例 2

#### 输入

2.10000, 3

#### 输出

9.26100

### 示例 3

#### 输入

2.00000, -2

#### 输出

0.25000

#### 解释

2^-2 = 0.5^2 = 0.25

## 题目思考

1. 根据数据规模, 暴力循环显然是不可行的, 那有没有降低复杂度的方法, 大而化小来解决?
2. 需要考虑到哪些边界条件?
3. 如何避免重复的计算过程?

## 解决方案

### 方案 1

#### 思路

- 分治法, 将当前值二分, 减少递归深度和运行时间
- 将每次的计算结果保存起来, 避免重复计算
- 注意边界条件：n == 0, n == INT_MIN

#### 复杂度

- 时间复杂度 O(logN)：二分递归
- 空间复杂度 O(logN)：memo 数组需要存 logN 个元素

#### 代码

##### Python 3

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        memo = {0 : 1.0, 1 : x}

        def helper(n):
            if n < 0:
                return 1.0 / helper(-n)

            if n not in memo:
                half_n = n // 2
                memo[n] = helper(half_n) * helper (n - half_n)

            return memo[n]

        return helper(n)
```

##### C++

```cpp
class Solution {
public:
    double myPow(double x, int n) {
        // 使用long而非int作为key，是为了应对边界条件INT_MIN
        unordered_map<long, double> memo({{0, 1.0}, {1, x}});

        function<double(long)> helper = [&](long n) {
            if (n < 0) {
                return 1.0 / helper(-n);
            }

            if (memo.find(n) == memo.end()) {
                long half_n = n / 2;
                memo.emplace(n, helper(half_n) * helper(n - half_n));
            }

            return memo[n];
        };

        return helper(n);
    }
};
```

### 方案 2

#### 思路

- 经典快速幂
- 注意下面的^表示求幂符号, 不是异或
- 根据 x^(2n) == (x^2)^n 的性质, 快速将 n 收敛到 0
- 举例来说: 比如 2^7 = (2^2)^3\*2 = 4^3\*2 = (4^2)^1\*4\*2 =16\*4\*2
- 所以就是针对当前的幂值 n
  - 如果它是奇数, 那么最终结果要乘上当前 x 值
  - 如果它是偶数, 那么最终结果不需要乘
  - 然后 x\*=x, n/=2(也可以选择右移一位)即可
  - 最终到 n=0 就结束循环
- 将空间复杂度降至 O(1)
- 进一步思考: 快速幂每次都要除以 2 吗? 针对不同的问题是不是也可以除以不同的数来优化? ==> [372-超级次方](https://leetcode-cn.com/problems/super-pow/)

#### 复杂度

- 时间复杂度 O(logN)：n 每次除以 2, logN 次就收敛到 0
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
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

##### C++

```cpp
class Solution
{
public:
    double myPow(double x, int n)
    {
        bool neg = false;
        long nn = n;
        if (nn < 0)
        {
            neg = true;
            nn = -nn;
        }
        double res = 1;
        while (nn > 0)
        {
            if (nn & 1)
            {
                res *= x;
            }
            x *= x;
            nn >>= 1;
        }
        return neg ? 1 / res : res;
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
