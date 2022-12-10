> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/2VG8Kg/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个含有 n 个正整数的数组和一个正整数 target 。

找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。

### 示例 1：

- 输入：target = 7, nums = [2,3,1,2,4,3]
- 输出：2
- 解释：子数组 [4,3] 是该条件下的长度最小的子数组。

### 示例 2：

- 输入：target = 4, nums = [1,4,4]
- 输出：1

### 示例 3：

- 输入：target = 11, nums = [1,1,1,1,1,1,1,1]
- 输出：0

### 提示：

- 1 <= target <= 10^9
- 1 <= nums.length <= 10^5
- 1 <= nums[i] <= 10^5

### 进阶：

- 如果你已经实现 O(n) 时间复杂度的解法, 请尝试设计一个 O(n log(n)) 时间复杂度的解法。

## 题目思考

1. 题目要求『连续子数组』, 如何利用这一条件?
2. 如何设计 O(n log(n)) 时间复杂度的解法?

## 解决方案

### 方案 1

#### 思路

- 分析题目, 最容易想到的做法就是暴力两重循环: 遍历每个下标起点, 累加当前的和, 大于等于 target 时更新最短长度
- 但这种做法时间效率太低 (`O(N^2)`), 如何优化呢?
- 基于暴力法进行分析, 假设外层遍历到 i 作为起点, 内层遍历到下标 j 时子数组和大于等于了 target
- 此时暴力法的做法是将其长度 j-i+1 更新到最终结果, 然后跳出内层循环 (因为后面的子数组长度只会更长), 并以 i+1 作为起点重新开始遍历
- 但这样做完全没有必要, 因为`[i+1,j]`的子数组和可以直接根据`[i,j]`的子数组和计算得到, 即减去`nums[i]`, 完全可以用常数时间判断子数组`[i+1,j]`是否满足要求
- 所以更优化的做法是**固定当前的终点 j, 将起点 i 继续向右移动, 并更新子数组和, 满足条件时更新长度到最终结果, 否则停止移动 i, 继续遍历下一个终点 j 即可**
- 这样起点和终点都只需要遍历一遍, 时间复杂度降到了 O(N)
- 以上就是典型的滑动窗口的思想, 通常做法就是维护双指针代表窗口起点和终点, 然后根据当前窗口是否满足要求来进行不同的处理
- 下面代码中对上述每个步骤都有详细注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 最多遍历数组每个元素两遍
- 空间复杂度 `O(1)`: 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        # 方法1: 前缀和+滑动窗口+满足条件时更新
        n = len(nums)
        i = 0
        sm = 0
        res = n + 1
        for j in range(n):
            # 固定终点j
            sm += nums[j]
            while sm >= target:
                # 当前区间[i,j]满足条件, 其长度为j-i+1, 更新最终结果
                res = min(res, j - i + 1)
                # 更新子数组和
                sm -= nums[i]
                # 起点i向右移动
                i += 1
        return 0 if res == n + 1 else res
```

### 方案 2

#### 思路

- 注意到题目有个进阶要求: 尝试设计一个 O(n log(n)) 时间复杂度的解法
- 说到 log(n) 复杂度, 首先可以想到的几个常见操作就是排序/二分/优先队列
- 这里题目说明了所有数字都是正整数, 那么它们的前缀和一定是有序的
- 我们可以利用这一条件, 使用二分查找`当前前缀和-之前前缀和>=target`的之前最大下标
- 具体做法就是维护每个下标 j 的`[0,j]`子数组的前缀和 `sms[j]`
- 然后使用右二分查找 `sms[j]-target` 在前缀和数组 sms 中的插入位置 i
- 右二分保证了 `sms[i-1]` 一定是小于等于 `sm - target` 的, 也即 `sms[j]-sms[i-1]>=target`
- 这是因为右二分查找有序数组的某一元素时, 如果该元素存在于数组中, 则其插入位置是该元素在数组中的最后一个下标加 1; 而左二分(bisect_left)返回的插入位置则是该元素在数组中的头一个下标
- 这样区间`[i,j]`就是以 j 为终点满足条件的最小子数组, 将其更新到最终结果即可

#### 复杂度

- 时间复杂度 `O(NlogN)`: 遍历数组复杂度 `O(N)`, 二分查找复杂度 `O(logN)`
- 空间复杂度 `O(N)`: 需要存储前缀和数组

#### 代码

```python
# 方法2: 前缀和+右二分
        n = len(nums)
        res = n + 1
        sm = 0
        sms = []
        for j in range(n):
            sm += nums[j]
            sms.append(sm)
            if sm >= target:
                # 右二分, sms[i-1]一定是小于等于sm - target的
                # 也即sms[j]-sms[i-1]>=target, 区间[i,j]是以j为终点满足条件的最小子数组
                # 注意这里不能用bisect_left, 不然假设sms[i]恰好等于sms[j]-target, 即sms[j]-sms[i-1]==target, 此时区间[i+1,j]也满足条件
                i = bisect.bisect(sms, sm - target)
                res = min(res, j - i + 1)
        return 0 if res == n + 1 else res
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
