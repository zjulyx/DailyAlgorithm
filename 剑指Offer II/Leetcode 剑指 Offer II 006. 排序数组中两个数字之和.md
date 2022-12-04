> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/kLl5u1/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个已按照 升序排列 的整数数组  numbers ，请你从数组中找出两个数满足相加之和等于目标数  target 。

函数应该以长度为 2 的整数数组的形式返回这两个数的下标值。numbers 的下标 从 0  开始计数 ，所以答案数组应当满足 0 <= answer[0] < answer[1] < numbers.length 。

假设数组中存在且只存在一对符合条件的数字，同时一个数字不能使用两次。

### 示例 1：

- 输入：numbers = [1,2,4,6,10], target = 8
- 输出：[1,3]
- 解释：2 与 6 之和等于目标数 8 。因此 index1 = 1, index2 = 3 。

### 示例 2：

- 输入：numbers = [2,3,4], target = 6
- 输出：[0,2]

### 示例 3：

- 输入：numbers = [-1,0], target = -1
- 输出：[0,1]

### 提示：

- 2 <= numbers.length <= 3 \* 104
- -1000 <= numbers[i] <= 1000
- numbers 按 递增顺序 排列
- -1000 <= target <= 1000
- 仅存在一个有效答案

## 题目思考

1. 如何利用『升序排列』这一条件?

## 解决方案

### 思路

- 分析题目, 最容易想到的做法就是暴力两重循环, 外层固定一个数字, 内层遍历另外一个数字, 判断两者之和是否是目标数
- 还有另一个比较容易想到的思路是使用一个集合, 然后遍历一遍数组: 如果`target-当前的数`已经在集合的话, 就说明找到了一对结果, 直接返回即可; 否则就把当前的数加入集合
- 但以上两种做法要么时间效率低 (第一种的时间复杂度是`O(N*N)`), 要么空间效率低(第二种的空间复杂度是`O(N)`), 且都没有利用到『升序排列』这一条件, 如何优化呢?
- 对于有序数组, 通常有两种处理思路: **二分或者双指针**
- 这里如果使用二分的话, 意味着固定当前的数为起点, 然后二分查找右侧区间`target-当前的数`是否存在, 会额外引入 logN 的时间复杂度, 还没有上面的第二种思路好
- 所以尝试使用双指针的做法, 将两个下标 i 和 j 初始化为数组的头和尾, 然后往中间靠拢
- 根据当前的和, 具体分为以下三种情况:
  1. `numbers[i] + numbers[j] == target`: 找到一对满足条件的数字了, 直接返回
  2. `numbers[i] + numbers[j] < target`: 当前和小于 target, 因为数组有序, 如果保留 numbers[i], 而 j 继续往左的话, 新的和肯定更小于 target, 所以 numbers[i]可以被安全排除, 即 i 直接加 1
  3. `numbers[i] + numbers[j] > target`: 当前和大于 target, 因为数组有序, 如果保留 numbers[j], 而 i 继续往右的话, 新的和肯定更大于 target, 所以 numbers[j]可以被安全排除, 即 j 直接减 1
- 由于题目说明数组中存在且只存在一对符合条件的数字, 所以这样遍历下去, 最终肯定能找到那对符合条件的数字
- 使用双指针做法后, 不仅时间优化到了 `O(N)`, 也不需要额外的空间了

### 复杂度

- 时间复杂度 `O(N)`: 只需要遍历整个数组一遍
- 空间复杂度 `O(1)`: 只使用了几个常数空间的变量

### 代码

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        # 左右两指针, 分别初始化为开头和结尾
        i, j = 0, len(numbers) - 1
        while i < j:
            sm = numbers[i] + numbers[j]
            if sm == target:
                # 找到有效数字对了
                return [i, j]
            elif sm < target:
                # 当前和小于目标, 左指针向右移动
                i += 1
            else:
                # 当前和大于目标, 右指针向左移动
                j -= 1
        # 注意题目说明数组中存在且只存在一对符合条件的数字, 所以一定会在上面返回
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
