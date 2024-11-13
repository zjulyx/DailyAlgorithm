> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/NUPfPr/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个非空的正整数数组 nums ，请判断能否将这些数字分成元素和相等的两部分。

### 示例 1：

- 输入：nums = [1,5,11,5]
- 输出：true
- 解释：nums 可以分割成 [1, 5, 5] 和 [11] 。

### 示例 2：

- 输入：nums = [1,2,3,5]
- 输出：false
- 解释：nums 不可以分为和相等的两部分

### 提示：

- 1 <= nums.length <= 200
- 1 <= nums[i] <= 100

## 题目思考

1. 如何优化算法复杂度?

## 解决方案

- 分析题目, 要想分成元素和相等的两部分, 原数组元素个数至少需要是 2, 且总和需要是偶数
- 这样每部分的目标总和就是原数组总和除以 2, 此时最直观的做法就是利用回溯:
  - 针对每个数字, 都有两种选择, 加入当前子数组和不加
  - 同时维护当前子数组的总和, 如果其大于目标总和返回 false, 等于目标总和返回 true, 否则继续回溯
- 不过回溯的时间复杂度达到了`O(2^N)`, 根据题目规模肯定会超时, 如何优化呢?
- 观察题目规模, 不难发现目标总和最大只到 10000, 我们如果利用一个集合维护当前可以取得的子集总和, 且限制其最大值不能超过目标总和, 这样时间复杂度也就可控了
- 具体做法如下:
  - 初始化集合存入 0, 代表不使用任何数字的情况
  - 然后遍历每个数字, 构造一个新集合, 初始化为原集合的复制, 代表不使用当前数字
  - 接下来处理使用当前数字的情况, 遍历原集合的每个和, 然后尝试加入当前数字得到新和, 并判断它与目标和的大小关系: 如果新和等于目标和, 则直接返回 true; 如果新和小于目标和, 则加入新集合; 否则不做任何处理, 因为肯定无效
  - 最后将原集合替换成新集合, 继续遍历下一个数字
  - 最终遍历结束后则直接返回 false, 因为遍历过程中如果找到某个子集的和等于目标和时会直接返回 true
- 下面的代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N*C)`: C 是目标总和, N 是元素数目, 集合最多保存 C 个数字, 所以外层循环 `O(N)`, 内层循环 `O(C)`
- 空间复杂度 `O(C)`: 集合最多保存 C 个数字

#### 代码

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        ### 集合存储不超过target的所有和
        if len(nums) < 2:
            # 元素个数小于2, 无法拆分成元素和相等的两部分
            return False
        sm = sum(nums)
        if sm & 1:
            # 总和是奇数, 无法拆分成元素和相等的两部分
            return False
        # 目标子集的和就是sm除以2
        target = sm // 2
        # 初始化集合元素为0, 代表不添加任何元素的情况
        sum_set = {0}
        for num in nums:
            # 新集合初始化为原集合的复制, 代表不使用当前数字
            new_sum_set = sum_set.copy()
            # 接下来考虑加入当前数字后的情况, 遍历前面的所有和
            for sm in sum_set:
                if sm + num == target:
                    # 找到一个子集满足要求了, 直接返回true
                    return True
                if sm + num < target:
                    # 只有新和小于target时才加入集合, 大于的一定无效
                    new_sum_set.add(sm + num)
            sum_set = new_sum_set
        return False
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
