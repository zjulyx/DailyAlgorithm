> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

数字以 0123456789101112131415…的格式序列化到一个字符序列中。在这个序列中，第 5 位（从下标 0 开始计数）是 5，第 13 位是 1，第 19 位是 4，等等。

请写一个函数，求任意第 n 位对应的数字。

- 0 <= n < 2^31

## 题目样例

### 示例

- 输入：n = 3
- 输出：3

- 输入：n = 11
- 输出：0

## 题目思考

1. 能否找到什么规律?

## 解决方案

### 思路

- 观察序列本身:
  - 0~9 只占 1 位, 共 10 个
  - 10~99 占 2 位, 共 90 个
  - 100~999 占 3 位, 共 900 个
  - 1000~9999 占 4 位, 共 9000 个
  - ...
- **为了保持一致, 我们可以将 1 位的时候改成从 1 开始, 把 n=0 作为特殊情况, 这样 1 位也只有 9 个, 即 1~9**
- 这样我们就很容易发现规律, 假设当前位数是 le:
  - 每一位的开始值 start 是`pow(10, le-1)`
  - 每一位的数字数目是`9*start`
  - 每一位的字符总数则是`9*start*le` (因为每个数字有 le 个字符)
- 所以我们可以逐渐增加位数, 统计当前总共的字符个数
- 假设 le 位数下的字符总数是 cnt, le+1 下的字符总数是 nextcnt, 那么如果 n 在`[cnt, nextcnt)`范围内, 那就说明 n 落在的数字一定有 le 位
- 这样一来, 我们只需要定位 n 具体对应到的数字, 以及所在数字的第几位即可
- 而由于每个数字占有 le 位, 所以 n 对应的数字就是 `start+(n-cnt)/le`, 而具体 n 是在该数字的第几位, 则是`(n-cnt)%le`
- 下面的代码对必要步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 `O(logN)`
  - 只需要按位数遍历即可, n 的位数是 logN
- 空间复杂度 `O(1)`
  - 只使用了常数个变量

### 代码

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        if n == 0:
            return 0
        # 初始化计数值为1, 因为start最开始是1, 此时已经有1个字符了
        cnt = 1
        # 初始化位数为1位
        le = 1
        while n >= cnt:
            # 求当前位数下的start
            start = 10**(le - 1)
            # 求当前位数+1情况下的字符总数
            nexcnt = cnt + 9 * start * le
            if n <= nexcnt:
                # 当前n落在范围内, 找对应的数字和该数字中n对应的位(偏移量)
                i, offset = divmod(n - cnt, le)
                num = start + i
                # 将数字转成字符串, 其偏移量下标对应的位即为所求
                return int(str(num)[offset])
            # 更新字符总数, 同时位数加1, 继续循环
            cnt = nexcnt
            le += 1
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
