> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

给定一个数组 A[0,1,…,n-1]，请构建一个数组 B[0,1,…,n-1]，其中 B 中的元素 B[i]=A[0]×A[1]×…×A[i-1]×A[i+1]×…×A[n-1]。不能使用除法。

- 所有元素乘积之和不会溢出 32 位整数
- a.length <= 100000

## 题目样例

### 示例

- 输入: [1,2,3,4,5]
- 输出: [120,60,40,30,24]

## 题目思考

1. 如何做到 O(N)时间复杂度?

## 解决方案

#### 思路

- 一个比较容易想到的思路是暴力法, 对每个元素都计算其左右乘积, 这样时间复杂度达到了 O(N^2), 按照题目数据规模肯定会超时
- 还有种方法是求所有元素的总乘积, 然后对于每个元素分别除以自身 (注意对 0 的处理), 这样虽然只用了 O(N)时间, 但不满足题目要求
- 那如何做到 O(N)时间复杂度且不使用除法呢? 我们如果能够在计算某个元素时利用到之前的计算结果, 那么就不需要重复计算当前的左右所有乘积了
- 比较容易想到的思路就是类似前缀和的预处理方案, 只是这里改成了**前缀积**, 具体步骤如下:
  1. 从左到右遍历一遍数组, 记录当前前缀积并保存到数组中
  2. 然后再从右向左遍历, 记录当前后缀积, 然后与前一个前缀积相乘, 即为当前元素的左右所有元素乘积了
- 下面的代码对必要步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 只需要遍历数组两次
- 空间复杂度 O(N): 需要维护一个前缀积数组

#### 代码

```python
class Solution:
    def constructArr(self, a: List[int]) -> List[int]:
        # 从左到右, 再从右向左遍历
        # 维护前缀积数组, 从右向左遍历时只需要维护后缀积即可, 然后乘以前一个前缀积, 其结果即为当前元素的左右元素乘积
        lefts = []
        left = 1
        for x in a:
            left *= x
            lefts.append(left)
        # 这里只需要维护后缀积, 没必要再建立一个后缀积数组
        right = 1
        res = [0] * len(a)
        for i in range(len(a))[::-1]:
            # 注意下标为0时左侧没有元素, 此时左侧部分乘积置为1
            left = lefts[i - 1] if i > 0 else 1
            res[i] = left * right
            # 注意当前元素处理完之后再乘以它, 因为结果是不包含当前元素自身的
            right *= a[i]
        return res
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
