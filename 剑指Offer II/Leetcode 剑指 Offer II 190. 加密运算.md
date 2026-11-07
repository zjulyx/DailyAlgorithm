> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

计算机安全专家正在开发一款高度安全的加密通信软件，需要在进行数据传输时对数据进行加密和解密操作。假定 dataA 和 dataB 分别为随机抽样的两次通信的数据量：

- 正数为发送量
- 负数为接受量
- 0 为数据遗失

请不使用四则运算符的情况下实现一个函数计算两次通信的数据量之和（三种情况均需被统计），以确保在数据传输过程中的高安全性和保密性。

### 示例 1：

- 输入：dataA = 5, dataB = -1
- 输出：4

### 提示：

- dataA 和 dataB 均可能是负数或 0
- 结果不会溢出 32 位整数

## 题目思考

1. 不能用四则运算, 那还能用哪些运算符来实现呢?

## 解决方案

- 我们先来尝试分析数字的二进制表示, 看看能否用位运算来替代加法
- 如果某一位的两个数字都是 1, 那么这一位的加法就会产生进位, 所以`与`可以用来计算进位, 而且显然进位是要左移一位的
- 接下来考虑如何得到当前位的加法结果. `异或`在两个数字都为 1 或者都为 0 的情况下结果是 0, 否则结果是 1, 换言之**异或就是不带进位的加法**
- 将上面两步结合在一起, 我们就能把 `dataA+dataB` 转换成`((dataA&dataB)<<1)+(dataA^dataB)`, 虽然这里仍然有加号, 但是我们将其置为新的 dataA 和 dataB, 循环这个过程, 直到进位变成 0, 这样最终异或结果就是两者之和了
- 注意最终进位一定能变成 0, 这是因为如果有进位的话, 进位是会不断左移的, 而题目保证最终结果不会溢出, 那么经过若干次循环后, 一定会达到一个不需要进位的状态, 不然无限左移就会溢出了
- C++/JAVA 等语言分析到这里就 OK 了, 但 python 需要特别处理负数问题. 因为 python 的负数表示方法不是像其他语言那样的 32 位补码, 而是更高位也全是 1, 这样在处理负数的时候必须手动模拟 32 位补码, 才能正确得出结果, 不然最后结果就不满足 python 正确的负数表示方式, 而变成无符号正数了
- 所以我们需要先将负数转成 32 位补码 (`&0xFFFFFFFF`, 正数仍为自身, 负数相当于 32 位补码形式, 因为去掉了更高位上的 1), 然后利用上述结果求完之后, 如果结果是负数(`>0x7FFFFFFF`)的话再转成正常的 python 负数表示方式(`~(dataA ^ 0xFFFFFFFF)`, 即先对低 32 位的取反, 更高位不变, 然后整体再取反, 从而将大于等于 32 位的数字重新转成 1)
- 下面代码额外列出了 Java 版本, 方便大家对比. Java 的负数就是 32 位补码表示, 所以不需要额外进行处理

#### 复杂度

- 时间复杂度 O(logN): 最多需要遍历所有位数, 数字 N 的位数为 logN
- 空间复杂度 O(1): 只需要维护常数个变量

#### 代码

##### python 3

```python
class Solution:
    def encryptionCalculate(self, dataA: int, dataB: int) -> int:
        # 32位数掩码
        mask = 0XFFFFFFFF
        # 32位数的最大正数
        posMx = 0X7FFFFFFF
        while dataB != 0:
            # a是不带进位的和, 都要转成32位整数
            # b是进位, 都要转成32位整数
            # 循环直到进位为0, 那么a就是最终结果
            sumWithoutCarry = (dataA ^ dataB) & mask
            carry = ((dataA & dataB) << 1) & mask
            dataA, dataB = sumWithoutCarry, carry
        # 最终如果是32位负数的话, 需要将其转回python正常的负数表示形式(高于32位的全是1, 而不是32位负数那样更高位全为0), 做法是先对低 32 位的取反, 更高位不变, 然后整体再取反, 从而将大于等于 32 位的数字重新转成 1
        return dataA if dataA <= posMx else ~(dataA ^ mask)
```

##### Java

```java
class Solution {
    public int encryptionCalculate(int dataA, int dataB) {
        while (dataB != 0)
        {
            int sumWithoutCarry = dataA ^ dataB;
            int carry = (dataA & dataB) << 1;
            dataA = sumWithoutCarry;
            dataB = carry;
        }
        return dataA;
    }
}
```
