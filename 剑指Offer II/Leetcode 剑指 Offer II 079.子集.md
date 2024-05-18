> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/TVdhkn/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。

解集 不能 包含重复的子集。你可以按 任意顺序 返回解集。

### 示例 1：

- 输入：nums = [1,2,3]
- 输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]

### 示例 2：

- 输入：nums = [0]
- 输出：[[],[0]]

### 提示：

- 1 <= nums.length <= 10
- -10 <= nums[i] <= 10
- nums 中的所有元素 互不相同

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 首先我们可以尝试用递归的思路来解决
- 观察幂集的特点, 我们可以发现它的每个子集都可以细分成两种情况: 使用原集合的某个元素以及不使用该元素
- 大家可以将整个过程想象成一个二叉树: 每遍历到原集合的一个元素, 都可以将其分成两个分支继续向下, 直到遍历完所有元素为止
- 我们可以基于上述分析进行递归求解, 具体做法如下:
  - 传入当前下标以及当前使用的元素组成的列表
  - 然后递归调用下一下标, 此时分为两种情况: 使用当前元素和不使用当前元素
  - 最后递归出口是下标达到原集合长度, 此时形成的列表即为幂集的其中一个子集
- 由于原集合中不包含重复元素, 所以每个递归出口形成的列表也各不相同, 无需手动去重

#### 复杂度

- 时间复杂度 O(2^N): 每遍历一个元素我们都有两种选择, 所以总时间是 2^N
- 空间复杂度 O(N): 递归栈的消耗, 对应的是上述分析中二叉树的高度, 即原集合长度 N

#### 代码

##### Python 3

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        # 方法1: 递归传入当前下标和列表
        res = []

        def getSet(i, cur):
            if i == len(nums):
                # 递归出口, 将其加入最终结果集
                res.append(cur)
                return
            # 两种情况/分支
            getSet(i + 1, cur)
            getSet(i + 1, cur + [nums[i]])

        getSet(0, [])
        return res
```

### 方案 2

#### 思路

- 接下来我们尝试用迭代的思路来解决
- 根据方案 1 的分析, 我们不难发现幂集的所有子集都可以用一个长度为 N 的 mask 来表示 (N 是原集合长度)
- 其中 mask 的第 i 位为 1 就对应使用下标 i 的元素, 为 0 则不使用
- 所以我们可以依次遍历[0, 2^N-1], 统计其哪些位为 1, 从而得到对应的子集并加入结果集即可

#### 复杂度

- 时间复杂度 O(N\*2^N): 需要遍历 2^N 个元素, 内层循环需要遍历 N 位得到具体的子集
- 空间复杂度 O(1): 只使用了几个常数空间的变量 (结果集占用的空间不计算在内)

#### 代码

##### Python 3

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        # 方法2: 迭代+位运算
        n = len(nums)
        res = []
        for mask in range(1 << n):
            cur = []
            for i in range(n):
                if (mask >> i) & 1:
                    # 第 i 位为 1, 使用对应下标 i 的元素
                    cur.append(nums[i])
            res.append(cur)
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
