> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/consecutive-numbers-sum/)

> 这道题虽然难度是困难, 但是由于题目描述比较简单, 网上看到有出现在面试中的情况, 还是值得思考一下, 而且最终可以一行代码搞定你敢信? 😂

## 题目描述

给定一个正整数 N，试求有多少组连续正整数满足所有数字之和为 N?

1 <= N <= 10 ^ 9

## 题目样例

### 示例 1

#### 输入

5

#### 输出

2

#### 解释

5 = 5 = 2 + 3，共有两组连续整数([5],[2,3])求和后为 5。

### 示例 2

#### 输入

9

#### 输出

3

#### 解释

9 = 9 = 4 + 5 = 2 + 3 + 4

## 题目思考

1. 根据题目数据规模, 这道题要想不超时就要想出时间复杂度\<O(N)的方案
2. 试着列出几个例子, 你能否发现什么规律?

## 解决方案

### 思路

- 找规律, 利用小学/初中数学知识?
  - 对于连续正整数为奇数的情况, 设其长度为 x, 中间值为 m, 那么 N 一定为 m\*x, 例如 15=1+2+3+4+5=3\*5
  - 对于连续正整数为偶数的情况, 设其长度为 x, 两个中间值为 m 和 m+1, 那么 N 一定为(m+0.5)\*x, 例如 10=1+2+3+4=2.5\*4
- 分析出来这个就很简单了, 具体步骤如下:
  1. 按照长度 x 从 1 开始遍历, 最大长度要小于 sqrt(2\*N)
     - 因为假设长度为 x, 从 1 开始的连续整数之和为 x\*(x+1)/2, 这是最小的和了, 它要<=N, 那么 x 就要小于 sqrt(2N), 那 x<=int(sqrt(2N))
  2. 当前长度为奇数的话, 要保证其能被 N 整除, 那样就一定存在一个可行区间了
  3. 当前长度为偶数的话, 要保证 N/x 得到一个.5 的小数, 也就是检查 N/x 和 int(N/x)的差是否是 0.5 即可, 对应 python 3 就是 N/x-N//x==0.5
- python 完全可以一行代码搞定~

### 复杂度

- 时间复杂度 O(sqrt(N)): 只需要遍历 2N 的平方根个数, 根据此题数据规模, 2\*10^9 的平方根是 44000 多, 满足运行时间要求
- 空间复杂度 O(1) 或 O(sqrt(N))
  - 老老实实循环的话可以只使用 1 个变量, 空间复杂度 O(1)
  - 一行代码的话
    - 如果使用列表表达式+len, 那么需要保存 sqrt(2N)的元素到数组, 空间复杂度 O(sqrt(N))
    - 如果使用生成器表达式+sum, 也不需要额外的空间, 因为生成器中的元素是一个一个出来的, 而不是一股脑全存在内存中, 类似其他语言的迭代器(yield return...), 空间复杂度 O(1)

### 代码

#### Python 3

##### 中规中矩循环版

```python
class Solution:
    def consecutiveNumbersSum(self, N: int) -> int:
        # 根据区间长度, 分奇偶找规律
        # 注意区间长度上界
        res = 0
        le = 1
        while le * (le + 1) // 2 <= N:
            # 循环条件表示1开头长度为i的区间和不能大于N, 否则不可能有满足要求的区间, 因为区间至少要从1开始
            if le & 1:
                if N % le == 0:
                    res += 1
            else:
                if N / le - N // le == 0.5:
                    res += 1
            le += 1
        return res
```

##### 一行代码版 - 利用 sum 和生成器表达式

```python
class Solution:
    def consecutiveNumbersSum(self, N: int) -> int:
        # 利用sum, 将满足要求的元素映射为1, sum后即为结果, 且好处是不需要转成数组来操作, 直接sum接生成器就OK
        return sum(1 for i in range(1, int(math.sqrt(2 * N) + 1)) if i & 1 and N % i == 0 or i & 1 == 0 and (N / i - N // i == 0.5))
```

##### 一行代码版 - 利用 len 和列表表达式

```python
class Solution:
    def consecutiveNumbersSum(self, N: int) -> int:
        # 也可以利用len统计满足要求的元素个数 (len不能统计迭代器, 因为它没有长度的概念, 所以只能转成数组表达式)
        return len([i for i in range(1, int(math.sqrt(2 * N) + 1)) if i & 1 and N % i == 0 or i & 1 == 0 and (N / i - N // i == 0.5)])
```

#### C++

```cpp
class Solution {
public:
    int consecutiveNumbersSum(int N) {
        int res = 0;

        for (int le = 1; le * (le + 1) / 2 <= N; ++le) {
            if (le & 1) {
                if (N % le == 0) {
                    ++res;
                }
            } else {
                if (1.0 * N / le - N / le == 0.5) {
                    ++res;
                }
            }
        }

        return res;
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
