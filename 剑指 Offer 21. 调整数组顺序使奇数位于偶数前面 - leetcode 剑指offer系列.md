> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

> 今天继续更新剑指 offer 系列, 这道题比较简单, 但同样有多种方法, 大家可以想想看有哪些思路

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。

- 1 <= nums.length <= 50000
- 1 <= nums[i] <= 10000

## 题目样例

### 示例

#### 输入

nums = [1,2,3,4]

#### 输出

[1,3,2,4]

注：[3,1,2,4] 也是正确的答案之一。

## 题目思考

1. 如何一次遍历搞定?
2. 可以不使用额外空间, 直接原地修改吗?

## 解决方案

### 方案 1

#### 思路

##### 分析

- 分析题目, 我们要使得奇数在偶数后面, 那么只要保证所有奇数下标都从 0 开始, 而偶数下标从末尾往前即可
- 所以一个很自然的思路就是新建一个数组, 长度和当前一样
- 然后维护两个下标代表新数组的奇数和偶数当前下标
- 最后遍历原数组:
  - 遇到奇数就放到新数组的奇数下标处, 同时下标右移
  - 遇到偶数就放到新数组的偶数下标处, 同时下标左移

##### 实现

- 下面代码完全基于上述分析实现, 必要步骤都有详细注释

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍数组
- 空间复杂度 `O(N)`
  - 额外需要一个数组存 N 个元素

#### 代码

```python
class Solution:
    def exchange(self, nums: List[int]) -> List[int]:
        odd, even = 0, len(nums) - 1
        res = [0] * len(nums)
        for n in nums:
            if n & 1:
                # 当前是奇数, 存到新数组奇数下标处, 同时下标右移
                res[odd] = n
                odd += 1
            else:
                # 当前是偶数, 存到新数组偶数下标处, 同时下标左移
                res[even] = n
                even -= 1
        return res
```

### 方案 2

#### 思路

##### 分析

- 回顾方案 1, 我们需要额外使用一个数组保存新的数字, 那有没有可能原地修改呢?
- 答案也是可以的, 我们仍然借鉴方案 1 的思路, 只不过这次两个下标对应的是原数组的下标
- 其中左边的下标用于找当前往后第一个偶数, 而右边下标是从后往前第一个奇数, 这样只需要将它们两个交换位置, 就满足奇数在前偶数在后的要求
- 然后两下标继续往中间靠拢, 重复上述步骤即可

##### 实现

- 下面代码完全基于上述分析实现, 必要步骤都有详细注释

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个数字只需要被遍历一遍
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def exchange(self, nums: List[int]) -> List[int]:
        i, j = 0, len(nums) - 1
        while i < j:
            while i < j and nums[i] & 1:
                # 如果当前本身就是奇数, 继续往后找
                i += 1
            while i < j and nums[j] & 1 == 0:
                # 如果当前本身就是偶数, 继续往前找
                j -= 1
            if i < j:
                # 找到一对不满足条件的奇偶数, 交换它们位置
                nums[i], nums[j] = nums[j], nums[i]
            i += 1
            j -= 1
        return nums
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
