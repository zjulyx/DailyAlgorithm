> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

0 <= 数组长度 <= 50000

## 题目样例

### 示例

- 输入: [7,5,6,4]
- 输出: 5

## 题目思考

1. 如何批量得到逆序对?

## 解决方案

#### 思路

- 一个比较容易想到的思路是双重循环, 暴力求逆序对, 但这样时间复杂度是 O(N^2), 根据数据规模肯定超时, 如何进行优化呢?
- 如果我们能将问题进行分解, 先求出数组左右两部分的逆序对数目, 最后再一次遍历将分属两部分的逆序对(也即第一个数在左半部分, 第二个数在右半部分)也求出来并累加在一起, 这样就能优化时间复杂度到 O(NlogN), 这就是分治法的思想
- 此时关键在于如何一次遍历就求得分属两部分的逆序对呢? 如果左右两部分是有序的就很好办了, 可以利用归并排序的思路, 双指针遍历, 哪边小就把哪边加入结果数组中. **如果是右侧小的话就意味着左侧剩余的数字都和当前右侧数字构成逆序对, 将对应的数目加入结果中即可**.
- 而左右部分有序正好也是归并排序的结果, 所以整个思路就是在归并排序的基础上加上逆序对的统计
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(NlogN): 使用了归并排序, 时间复杂度是(`N+(N/2)*2+(N/4)*4+...+1*N = NlogN`, 因为一共 `logN` 个乘积)
- 空间复杂度 O(N): 归并排序需要用到临时数组, 长度为 N

#### 代码

```python
class Solution:
    def reversePairs(self, nums: List[int]) -> int:
        self.res = 0

        def mergesort(s, e):
            if s >= e:
                return
            m = (s + e) >> 1
            # 先排序并统计左右部分
            mergesort(s, m)
            mergesort(m + 1, e)
            # 使用临时数组存储排序好的左右部分
            # 注意这里选择逆序存储, 是因为可以利用O(1)的pop操作得到最小的元素
            # 当然这里也可以使用双端队列, 这样就无需逆序了
            left = nums[s:m + 1][::-1]
            right = nums[m + 1:e + 1][::-1]
            for i in range(s, e + 1):
                if not right or left and left[-1] <= right[-1]:
                    # 当前左侧数字更小或者右侧数字已经用完, 此时不构成逆序对
                    nums[i] = left.pop()
                else:
                    # 当前右侧数字更小, 和剩余的左侧部分都构成逆序对
                    self.res += len(left)
                    nums[i] = right.pop()

        mergesort(0, len(nums) - 1)
        return self.res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
