> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/0i0mDW/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：一个机器人每次只能向下或者向右移动一步。

### 示例 1：

![](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)

- 输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
- 输出：7
- 解释：因为路径 1→3→1→1→1 的总和最小。

### 示例 2：

- 输入：grid = [[1,2,3],[4,5,6]]
- 输出：12

### 提示：

- m == grid.length
- n == grid[i].length
- 1 <= m, n <= 200
- 0 <= grid[i][j] <= 100

## 题目思考

1. 如何优化算法复杂度?

## 解决方案

- 分析题目, 机器人只能向下或向右移动, 我们可以利用这一点, 根据当前坐标的**左边和上边相邻坐标**的路径和来得到当前坐标的最小路径和
- 显然这就是动态规划的思路:
  - 设 `dp[r][c]` 代表坐标(r,c)的最小路径和
  - 初始化 `dp[0][0] = grid[0][0]`, 其他值全是 inf, 表示开始时只有起点的路径和确定
  - 然后进行状态转移, 对于不是(0,0)的坐标(r,c): `dp[r][c] = dp[r-1][c] + dp[r][c-1]` (如果 r 或 c 为 0, 则相应的 r-1 或 c-1 的 dp 值就是 inf, 表示不能从该方向转移而来)
  - 最终右下角坐标的 `dp[m-1][n-1]` 就代表右下角的最小路径和
- 这里可以额外进行一些优化, 直接原地更新 grid, 这样就不需要单独的 dp 二维数组, 从而节省空间
- 下面的代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(MN)`: 需要两重循环求 DP 值
- 空间复杂度 `O(1)`: 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        for r in range(m):
            for c in range(n):
                # 获取左边和上边相邻坐标的最小路径和, 对应坐标出界时则为inf
                lsm = float("inf") if c == 0 else grid[r][c - 1]
                usm = float("inf") if r == 0 else grid[r - 1][c]
                if (r, c) != (0, 0):
                    # 只有非起点时才更新最小路径和, 否则起点就会错误地变成inf
                    grid[r][c] += min(lsm, usm)
        # 最终结果就是右下角最小路径和
        return grid[m - 1][n - 1]
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
