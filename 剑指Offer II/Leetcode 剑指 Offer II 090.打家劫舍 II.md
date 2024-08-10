> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/PzWKhm/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

一个专业的小偷，计划偷窃一个环形街道上沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组 nums ，请计算 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。

### 示例 1：

- 输入：nums = [2,3,2]
- 输出：3
- 解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。

### 示例 2：

- 输入：nums = [1,2,3,1]
- 输出：4
- 解释：你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。偷窃到的最高金额 = 1 + 3 = 4 。

### 示例 3：

- 输入：nums = [0]
- 输出：0

### 提示：

- 1 <= nums.length <= 100
- 0 <= nums[i] <= 1000

## 题目思考

1. 如何处理环形街道首尾相连的问题?

## 解决方案

- 分析题目, 不难发现这道题和上一道题目([Leetcode 剑指 Offer II 089. 打家劫舍](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484853&idx=1&sn=f5bacc92269f4713aa5cf16ccee66206#rd))非常类似, 只是多了个环形街道首尾相连的条件, 那能否沿用之前的做法呢?
- 答案是肯定的, 既然不能同时偷开头和结尾, 那我们可以分两次计算, 先排除开头, 再排除结尾, 然后取两者偷窃金额的较大值即可
- 这样具体计算过程就可以使用之前的动态规划思路了, 这里就不再重复说明了, 不熟悉的同学可以回去看上一道题目([Leetcode 剑指 Offer II 089. 打家劫舍](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484853&idx=1&sn=f5bacc92269f4713aa5cf16ccee66206#rd))
- 另外这里存在一个问题, 假如只有一个房屋, 那么两次计算都计算的是空列表, 最后会错误地得到 0, 所以我们需要特殊判断这种情况, 如果只有一个房屋就直接偷它, 返回其金额即可
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 需要遍历整个数组两遍
- 空间复杂度 `O(1)`: 只需要几个常数空间的变量

#### 代码

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        # 两次动态规划+特殊处理单个房屋
        n = len(nums)
        if n == 1:
            # 只有一个房屋, 直接偷它
            return nums[0]

        def getMax(s, e):
            # 初始化ppre和pre都为0, 代表没偷时的金额
            ppre, pre = 0, 0
            for i in range(s, e + 1):
                x = nums[i]
                # 当前dp值是ppre+x和pre的较大值
                # 然后滚动更新ppre和pre
                ppre, pre = pre, max(pre, ppre + x)
            # 最终结果就是pre, 最后一个dp值
            return pre

        return max(getMax(0, n - 2), getMax(1, n - 1))
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
