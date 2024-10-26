> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/IlPe0q/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个三角形 triangle ，找出自顶向下的最小路径和。

每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。

### 示例 1：

- 输入：triangle = [[2],[3,4],[6,5,7],[4,1,8,3]]
- 输出：11
- 解释：如下面简图所示：

```
   2
  3 4
 6 5 7
4 1 8 3
```

自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。

### 示例 2：

- 输入：triangle = [[-10]]
- 输出：-10

### 提示：

- 1 <= triangle.length <= 200
- triangle[0].length == 1
- triangle[i].length == triangle[i - 1].length + 1
- -10^4 <= triangle[i][j] <= 10^4

### 进阶：

- 你可以只使用 O(n) 的额外空间（n 为三角形的总行数）来解决这个问题吗？

## 题目思考

1. 如何优化算法复杂度?

## 解决方案

- 分析题目, 每一步只能移动到下一行中相邻的结点上, 我们可以利用这一点, 根据上一行下标 i 的最小路径和来更新当前行下标 i 和 i+1 的最小路径和
- 显然这就是动态规划的思路:
  - 设 `dp[r][i]` 代表第 r 行第 i 个下标的最小路径和
  - 初始化 `dp[0][0] = triangle[0][0]`, 其他值全是 inf, 表示开始时只有起点的路径和确定
  - 然后进行状态转移, 对于第 r 行的第 i 个下标: `dp[r][i] = triangle[r][i]+min(dp[r-1][i-1],dp[r][i]` (如果 r 或 i 为 0, 则相应的 r-1 或 c-1 的 dp 值就是 inf, 表示不能从该方向转移而来)
  - 这样最后一行的最小 dp 值就代表整个三角形的最小路径和
- 这里可以额外进行一些优化, 将二维 dp 数组优化成一维, 这样就可以满足题目的进阶要求
- 具体做法就是只存储前一行和当前行的 dp 值, 记为 pdp 和 dp, 由于最后一行元素数目为 n, 所以整体空间复杂度就是 O(n)
- 然后在计算第 r 行的 dp 值时, 遍历 pdp, 根据 `pdp[i] `的值, 更新 `dp[i]`和 `dp[i+1]`
- 计算完毕后, 再将 dp 赋值给 pdp, 用于下一行的计算
- 下面的代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N^2)`: 需要两重循环求 DP 值
- 空间复杂度 `O(N)`: 最多使用长度为 N 的 dp 数组

#### 代码

```python
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        ### 动态规划+滚动数组
        n = len(triangle)
        # pdp存储上一行的最小路径, 初始化为第0行
        pdp = triangle[0]
        for r in range(1, n):
            # 初始化当前行的最小路径为+inf
            dp = [float("inf")] * len(triangle[r])
            for i in range(len(pdp)):
                # 状态转移, 上一行的下标i可以移动到i或者i+1, 更新当前行对应下标的最小路径和
                dp[i] = min(dp[i], triangle[r][i] + pdp[i])
                dp[i + 1] = min(dp[i + 1], triangle[r][i + 1] + pdp[i])
            pdp = dp
        # 最终结果就是最后一行的最小路径和
        return min(pdp)
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
