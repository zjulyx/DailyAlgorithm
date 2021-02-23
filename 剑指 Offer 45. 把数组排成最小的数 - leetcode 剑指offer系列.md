> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

- 0 < nums.length <= 100
- 输出结果可能非常大，所以你需要返回一个字符串而不是整数
- 拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

## 题目样例

### 示例

- 输入: [10,2]
- 输出: "102"

- 输入: [3,30,34,5,9]
- 输出: "3033459"

## 题目思考

1. 怎么定义最小?

## 解决方案

### 思路

- 分析题目, 要使得拼接起来的数字最小, 只有当各个拼接元素按照从小到大拼接起来才可以
- 而这里的从小到大拼接, 不是指按照数字本身顺序或者字典序从小到大拼接
- 举个例子, 90 和 902 的拼接结果应该是 90290, 但是 90 不管是数字还是字典序都小于 902
- 那如何定义哪个数字作为拼接后的第一个数字呢?
- 假如这个数字和任意其他数字拼接起来, 都小于将两个数字互换后拼接的结果, 那么显然这个数字就应该放在首位
- 到这里就知道了, 我们应该自定义一个排序方法, 对于两个数字 a 和 b 而言, 比较 `str(a)+str(b)` 与 `str(b)+str(a)` 的大小关系, 然后对整个列表排序后组成一个字符串即可
- 下面的代码使用两种方案实现, 一种是快速排序, 一种是语言内置排序, 供大家参考

### 复杂度

- 时间复杂度 `O(NlogN)`
  - 排序的时间复杂度
- 空间复杂度 `O(N)`
  - 使用了额外的字符串数组

### 代码

#### 方案 1 - 自定义快速排序

```python
class Solution:
    def minNumber(self, nums: List[int]) -> str:
        # 使用map将原数组的数字转成字符串
        nums = list(map(str, nums))

        def quicksort(l, r):
            # 经典快速排序实现
            if l >= r:
                return
            pivot = nums[l]
            i, j = l, r
            while i < j:
                # 只需要把这里改成自定义的排序方法即可
                while i < j and nums[j] + pivot >= pivot + nums[j]:
                    j -= 1
                nums[i] = nums[j]
                # 只需要把这里改成自定义的排序方法即可
                while i < j and nums[i] + pivot <= pivot + nums[i]:
                    i += 1
                nums[j] = nums[i]
            nums[i] = pivot
            quicksort(l, i - 1)
            quicksort(i + 1, r)

        quicksort(0, len(nums) - 1)
        return ''.join(nums)
```

#### 方案 2 - 自定义内置排序

```python
class Solution:
    def minNumber(self, nums: List[int]) -> str:
        class compare(str):
            def __lt__(self, x):
                return self + x < x + self

        # 使用python内置sorted的key, 传入一个重载__lt__的类, 自定义排序
        return ''.join(sorted(map(str, nums), key=compare))
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
