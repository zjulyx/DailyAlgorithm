> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

购物车内的商品价格按照升序记录于数组 price。请在购物车中找到两个商品的价格总和刚好是 target。若存在多种情况，返回任一结果即可。

### 示例 1：

- 输入：price = [3, 9, 12, 15], target = 18
- 输出：[3,15] 或者 [15,3]

### 示例 2：

- 输入：price = [8, 21, 27, 34, 52, 66], target = 61
- 输出：[27,34] 或者 [34,27]

### 提示：

- 1 <= price.length <= 10^5
- 1 <= price[i] <= 10^6
- 1 <= target <= 2\*10^6

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
  1. `price[i] + price[j] == target`: 找到一对满足条件的数字了, 直接返回
  2. `price[i] + price[j] < target`: 当前和小于 target, 因为数组有序, 如果保留 price[i], 而 j 继续往左的话, 新的和肯定更小于 target, 所以 price[i]可以被安全排除, 即 i 直接加 1
  3. `price[i] + price[j] > target`: 当前和大于 target, 因为数组有序, 如果保留 price[j], 而 i 继续往右的话, 新的和肯定更大于 target, 所以 price[j]可以被安全排除, 即 j 直接减 1
- 这样遍历下去最终肯定 i 和 j 会相遇, 此时退出循环, 说明没找到满足条件的数字对, 返回空数组即可
- 使用双指针做法后, 时间复杂度没有变差, 也不需要额外的空间了

#### 复杂度

- 时间复杂度 O(N): 只遍历了一遍数组
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def twoSum(self, price: List[int], target: int) -> List[int]:
        # 双指针
        i, j = 0, len(price) - 1
        while i < j:
            if price[i] + price[j] == target:
                # 找到一对满足条件的数字, 直接返回
                return [price[i], price[j]]
            elif price[i] + price[j] < target:
                # 当前和小于target, 只能是i向右移, 这样后续和才会更大
                i += 1
            else:
                # 当前和大于target, 只能是j向左移, 这样后续和才会更小
                j -= 1
        return []
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
