> 题目难度: 困难

> [原题链接](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

在股票交易中，如果前一天的股价高于后一天的股价，则可以认为存在一个「交易逆序对」。请设计一个程序，输入一段时间内的股票交易记录 record，返回其中存在的「交易逆序对」总数。

### 示例 1：

- 输入：record = [9, 7, 5, 4, 6]
- 输出：8
- 解释：交易中的逆序对为 (9, 7), (9, 5), (9, 4), (9, 6), (7, 5), (7, 4), (7, 6), (5, 4)。

### 提示：

- 0 <= record.length <= 50000

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
    def reversePairs(self, record: List[int]) -> int:
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
            left = record[s:m + 1][::-1]
            right = record[m + 1:e + 1][::-1]
            for i in range(s, e + 1):
                if not right or left and left[-1] <= right[-1]:
                    # 当前左侧数字更小或者右侧数字已经用完, 此时不构成逆序对
                    record[i] = left.pop()
                else:
                    # 当前右侧数字更小, 和剩余的左侧部分都构成逆序对
                    self.res += len(left)
                    record[i] = right.pop()

        mergesort(0, len(record) - 1)
        return self.res
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
