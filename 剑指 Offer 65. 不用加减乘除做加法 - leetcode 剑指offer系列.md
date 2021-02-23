> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

写一个函数，求两个整数之和，要求在函数体内不得使用 “+”、“-”、“\*”、“/” 四则运算符号。

- a, b 均可能是负数或 0
- 结果不会溢出 32 位整数

## 题目样例

### 示例

- 输入: a = 1, b = 1
- 输出: 2

## 题目思考

1. 不能用四则运算, 那还能用哪些运算符来实现呢?

## 解决方案

#### 思路

- 相比昨天的题目[剑指 Offer 64. 求 1+2+…+n - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484121&idx=1&sn=0e2c06592854dce01ea1e217eee1f382&chksm=900285d4a7750cc20f19a4b799f5aefb7c9e380d1037514ffdc72f94b53d20e1371483052861&token=1057874384&lang=zh_CN#rd), 这道题目限制少了很多, 至少循环/条件判断/位运算这些都能用了
- 我们先来尝试分析数字的二进制表示, 看看能否用位运算来替代加法
- 如果某一位的两个数字都是 1, 那么这一位的加法就会产生进位, 所以`与`可以用来计算进位, 而且显然进位是要左移一位的
- 接下来考虑如何得到当前位的加法结果. `异或`在两个数字都为 1 或者都为 0 的情况下结果是 0, 否则结果是 1, 换言之**异或就是不带进位的加法**
- 将上面两步结合在一起, 我们就能把 `a+b` 转换成`((a&b)<<1)+(a^b)`, 虽然这里仍然有加号, 但是我们将其置为新的 a 和 b, 循环这个过程, 直到进位变成 0, 这样最终异或结果就是两者之和了
- 注意最终进位一定能变成 0, 这是因为如果有进位的话, 进位是会不断左移的, 而题目保证最终结果不会溢出, 那么经过若干次循环后, 一定会达到一个不需要进位的状态, 不然无限左移就会溢出了
- C++/JAVA 等语言分析到这里就 OK 了, 但 python 需要特别处理负数问题. 因为 python 的负数表示方法不是像其他语言那样的 32 位补码, 而是更高位也全是 1, 这样在处理负数的时候必须手动模拟 32 位补码, 才能正确得出结果, 不然最后结果就不满足 python 正确的负数表示方式, 而变成无符号正数了
- 所以我们需要先将负数转成 32 位补码 (`&0xFFFFFFFF`, 正数仍为自身, 负数相当于 32 位补码形式, 因为去掉了更高位上的 1), 然后利用上述结果求完之后, 如果结果是负数(`>0x7FFFFFFF`)的话再转成正常的 python 负数表示方式(`~(a ^ 0xFFFFFFFF)`, 即先对低 32 位的取反, 更高位不变, 然后整体再取反, 从而将大于等于 32 位的数字重新转成 1)
- 下面代码额外列出了 Java 版本, 方便大家对比. Java 的负数就是 32 位补码表示, 所以不需要额外进行处理

#### 复杂度

- 时间复杂度 O(logN): 最多需要遍历所有位数, 数字 N 的位数为 logN
- 空间复杂度 O(1): 只需要维护常数个变量

#### 代码

##### python 3

```python
class Solution:
    def add(self, a: int, b: int) -> int:
        # 32位数掩码
        mask = 0XFFFFFFFF
        # 32位数的最大正数
        posMx = 0X7FFFFFFF
        while b != 0:
            # a是不带进位的和, 都要转成32位整数
            # b是进位, 都要转成32位整数
            # 循环直到进位为0, 那么a就是最终结果
            smwithoutcarry = (a ^ b) & mask
            carry = ((a & b) << 1) & mask
            a, b = smwithoutcarry, carry
        # 最终如果是32位负数的话, 需要将其转回python正常的负数表示形式(高于32位的全是1, 而不是32位负数那样更高位全为0), 做法是先对低 32 位的取反, 更高位不变, 然后整体再取反, 从而将大于等于 32 位的数字重新转成 1
        return a if a <= posMx else ~(a ^ mask)
```

##### Java

```java
class Solution {
    public int add(int a, int b) {
        while (b != 0)
        {
            int smwithoutcarry = a ^ b;
            int carry = (a & b) << 1;
            a = smwithoutcarry;
            b = carry;
        }
        return a;
    }
}
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
