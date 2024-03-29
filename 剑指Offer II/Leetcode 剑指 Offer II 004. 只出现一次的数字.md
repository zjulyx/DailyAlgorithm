> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/WGki4K/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

- 给你一个整数数组 nums ，除某个元素仅出现 一次 外，其余每个元素都恰出现 三次 。请你找出并返回那个只出现了一次的元素。

### 示例 1：

- 输入：nums = [2,2,3,2]
- 输出：3

### 示例 2：

- 输入：nums = [0,1,0,1,0,1,100]
- 输出：100

### 提示：

- 1 <= nums.length <= 3 \* 10^4
- -2^31 <= nums[i] <= 2^31 - 1
- nums 中，除某个元素仅出现 一次 外，其余每个元素都恰出现 三次

### 进阶

- 你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

## 题目思考

1. 如何利用恰好出现 3 次这一条件?
2. 如何既做到线性时间复杂度, 又不使用额外空间?

## 解决方案

### 思路

- 分析题目, 一个很容易想到的方法使用一个计数字典存储每个数字的出现次数, 最后找到出现次数为 1 的数字即可
- 这种算法虽然做到了线性时间复杂度, 但需要额外的空间 (计数字典), 如何满足题目的进阶要求呢?
- 我们换一个角度, 考虑二进制『每一位』的计数
- 注意到其他数字都出现 3 次, 那么如果我们累加这一位上 1 的计数, 那么出现 3 次的数字对该计数的贡献值一定也是 3 的倍数
- 所以最终一定只有两种情况:
  - a. 计数模 3 的余数为 0 => 出现 1 次的数字在该位上也是 0
  - b. 计数模 3 的余数为 1 => 出现 1 次的数字在该位上也是 1
- 有了这个结论, 我们就可以依次遍历二进制的每一位, 然后应用上述判断, 从而组装出那个出现 1 次的数字
- 另外特别注意, 由于 python 的语言特性, 它的第 32 位并不是符号位, 当它为 1 时并不像其他语言的 32 位数字一样表示负数
- 所以我们需要将其更高位都填充为 1 才行, 具体做法就是先获得 32 位下取反后对应的正数 (与 0xFFFFFFFF 异或), 再整体取反即可
- 下面代码中对上述每个步骤都有详细注释, 方便大家理解

### 复杂度

- 时间复杂度 O(N): 外层循环需要遍历每一位 O(32), 内层循环需要遍历每个数字 O(N), 整体就是 O(32N), 等价于 O(N)
- 空间复杂度 O(1): 只使用了几个常数空间的变量

### 代码

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        res = 0
        for i in range(32):
            cnt = 0
            for x in nums:
                # 统计所有数字在这一位上1的计数
                cnt += (x >> i) & 1
            if cnt % 3 == 1:
                # 说明只出现一次的数字对这一位贡献了1
                # 因为其他数字都出现3次, 它们在这一位的计数和对3取模肯定是0
                res += 1 << i
                if i == 31:
                    # 第31位是1, 说明是负数, 需要将更高位也转成1, 转成正确的负数表示形式
                    res = ~(0xFFFFFFFF ^ res)
        return res
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
