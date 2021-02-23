> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个递增排序的数组和一个数字 s，在数组中查找两个数，使得它们的和正好是 s。如果有多对数字的和等于 s，则输出任意一对即可。

- 1 <= nums.length <= 10^5
- 1 <= nums[i] <= 10^6

## 题目样例

### 示例

- 输入：nums = [2,7,11,15], target = 9
- 输出：[2,7] 或者 [7,2]

- 输入：nums = [10,26,30,31,47,60], target = 40
- 输出：[10,30] 或者 [30,10]

## 题目思考

1. 如何利用递增排序的条件?
2. 如何做到空间复杂度是 O(1)?

## 解决方案

#### 思路

- 一个比较容易想到的思路是使用一个集合, 然后遍历一遍数组: 如果`target-当前的数`已经在集合的话, 就说明找到了一对结果, 直接返回即可; 否则就把当前的数加入集合
- 但这个思路没有利用到递增排序的条件, 且使用了额外的空间, 并不是最优解
- 如何利用排序的条件呢? 通常有两种思路: **二分或者双指针**
- 这里如果使用二分的话, 意味着固定当前的数为起点, 然后二分查找右侧区间`target-当前的数`是否存在, 会额外引入 logN 的时间复杂度, 还没有上面的思路好
- 所以尝试使用双指针的做法, 将两个下标 i 和 j 初始化为数组的头和尾, 然后往中间靠拢
- 根据当前的和, 具体分为以下三种情况:
  1. `nums[i] + nums[j] == target`: 找到一对满足条件的数字了, 直接返回
  2. `nums[i] + nums[j] < target`: 当前和小于 target, 因为数组有序, 如果保留 nums[i], 而 j 继续往左的话, 新的和肯定更小于 target, 所以 nums[i]可以被安全排除, 即 i 直接加 1
  3. `nums[i] + nums[j] > target`: 当前和大于 target, 因为数组有序, 如果保留 nums[j], 而 i 继续往右的话, 新的和肯定更大于 target, 所以 nums[j]可以被安全排除, 即 j 直接减 1
- 这样遍历下去最终肯定 i 和 j 会相遇, 此时退出循环, 说明没找到满足条件的数字对, 返回空数组即可
- 使用双指针做法后, 时间复杂度没有变差, 也不需要额外的空间了

#### 复杂度

- 时间复杂度 O(N): 只遍历了一遍数组
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # 双指针
        i, j = 0, len(nums) - 1
        while i < j:
            if nums[i] + nums[j] == target:
                # 找到一对满足条件的数字, 直接返回
                return [nums[i], nums[j]]
            elif nums[i] + nums[j] < target:
                # 当前和小于target, 只能是i向右移, 这样后续和才会更大
                i += 1
            else:
                # 当前和大于target, 只能是j向左移, 这样后续和才会更小
                j -= 1
        return []
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
