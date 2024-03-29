> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/skFtm2/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个只包含整数的有序数组 nums ，每个元素都会出现两次，唯有一个数只会出现一次，请找出这个唯一的数字。

### 示例 1:

- 输入: nums = [1,1,2,3,3,4,4,8,8]
- 输出: 2

### 示例 2:

- 输入: nums = [3,3,7,7,10,11,11]
- 输出: 10

### 提示:

- 1 <= nums.length <= 10^5
- 0 <= nums[i] <= 10^5

进阶: 采用的方案可以在 O(log n) 时间复杂度和 O(1) 空间复杂度中运行吗？

## 题目思考

1. 如何优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 一个最经典的思路就是全员异或, 最终得到的值就是那个单一元素, 因为其他成对元素的异或值是 0
- 不过这样做的时间复杂度达到了 O(N), 没有利用到给定数组有序的条件, 如何优化呢?
- 说到有序数组, 第一反应就是通过二分查找, 降低时间复杂度到 O(logN), 这道题也不例外
- 不过找到中点后, 如何判断向左还是向右查找呢?
- 我们先从最简单的所有元素都成对的数组开始, 例如`[0,0,1,1,3,3,5,5]`
- 不难发现它的偶数下标(0,2,4,6)都和右侧邻居值相同, 而奇数下标(1,3,5,7)都和左侧邻居值相同
- 如果在 1,3 之间插入一个单一元素 2, 数组变成`[0,0,1,1,2,3,3,5,5]`
- 显然左侧仍满足上述规律, 但 2 右侧的部分就正好颠倒了:
  - 奇数下标 5 的值是 3, 变成了和右侧邻居值相同
  - 偶数下标 8 的值是 5, 变成了和左侧邻居值相同
- 这就带来了二分查找的思路: 比较中点和左右邻居的值, 然后判断中点下标的奇偶性, 从而决定继续向左还是向右查找
- 假设中点下标是 m, 有以下几种情况:
  1. m 和右邻居相等: 如果 m 是偶数, 则说明左侧部分元素都成对, 单一元素在右侧, 向右查找; 否则说明左侧存在单一元素, 向左查找
  2. m 和左邻居相等: 如果 m 是偶数, 则说明左侧存在单一元素, 向左查找; 否则说明左侧部分元素都成对, 单一元素在右侧, 向右查找
  3. m 和左右邻居都不等: 自然 m 就是那个单一元素, 返回其值即可
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(logN): 二分查找每次都会将问题规模减半, 所以是 O(logN)
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def singleNonDuplicate(self, nums: List[int]) -> int:
        # 二分查找+比较左右邻居和中点下标奇偶性
        n = len(nums)
        s, e = 0, n - 1
        while s <= e:
            m = (s + e) >> 1
            if m + 1 < n and nums[m] == nums[m + 1]:
                if m & 1 == 0:
                    # 当前是偶数下标, 且其值和右侧数字相等
                    # 正常情况偶数下标就是和右侧数字相等
                    # 说明左侧都是成对元素, 单一元素在右侧, 向右查找
                    s = m + 1
                else:
                    # 当前是奇数下标, 且其值和右侧数字相等
                    # 正常情况奇数下标应该和左侧数字相等才对
                    # 说明单一元素就在左侧, 向左查找
                    e = m - 1
            elif m - 1 >= 0 and nums[m] == nums[m - 1]:
                if m & 1 == 0:
                    # 当前是偶数下标, 且其值和左侧数字相等
                    # 正常情况偶数下标应该和右侧数字相等才对
                    # 说明单一元素就在左侧, 向左查找
                    e = m - 1
                else:
                    # 当前是奇数下标, 且其值和左侧数字相等
                    # 正常情况奇数下标就是和左侧数字相等
                    # 说明左侧都是成对元素, 单一元素在右侧, 向右查找
                    s = m + 1
            else:
                # 当前数字和左右邻居都不相等, 正是要找的单一元素, 返回其值
                return nums[m]
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
