> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

在一个 m\*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

- 0 < grid.length <= 200
- 0 < grid[0].length <= 200

## 题目样例

### 示例

- 输入:

```
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
```

- 输出: 12
- 解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物

## 题目思考

1. 每一个格子的礼物最多价值跟什么有关?

## 解决方案

### 思路

- 分析题目, 只能向右或向下移动, 那么每个格子的最大价值只受左边一格和上边一格(如果存在的话)的最大价值的影响, 所以当前格子之前所能获得的最大价值 `premx` 就是两者中的较大值
- 假设 dp[r,c]代表格子(r,c)所能获得的最大价值, 自然就有`dp[r,c] = max(dp[r-1,c], dp[r,c-1]) + grid[r][c] (r-1>=0 and c-1>=0)`
- 而对于起点和左/上边界的情况(即不能同时满足`r-1>=0 and c-1>=0`时), 有:
  - 如果是左上角, 那么显然最大价值就是本身, `premx = 0`
  - 如果是左边界, 那么意味着只能从上边一格往下, 之前的最大价值就是上边一格的最大价值, `premx = grid[r - 1][c]`
  - 如果是上边界, 那么意味着只能从左边一格往右, 之前的最大价值就是左边一格的最大价值, `premx = grid[r][c - 1]`
- 所以我们可以把这几种情况统一起来, 每次遍历到一个格子时, 将 `premx` 置为 0, 然后如果左边或者上边一格存在的话, 就更新 `premx` 为其中的较大值即可
- 最终结果自然就是右下角的 dp 值
- 以上就是典型的动态规划的思想, 利用前面的计算结果来推导出当前的结果
- 另外这里还可以进行空间上的优化, 就是直接基于原数组进行修改, `grid[r][c]` 累加之前计算的 `premx` 即可, 这样就不需要额外 dp 矩阵了
- 下面的代码对必要步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 O(RC): 只需要遍历矩阵中的每个元素即可
- 空间复杂度 O(1): 原地修改矩阵, 只使用了几个变量

### 代码

```python
class Solution:
    def maxValue(self, grid: List[List[int]]) -> int:
        rows, cols = len(grid), len(grid[0])
        for r in range(rows):
            for c in range(cols):
                # 分别求左边一格和上边一格(如果存在的话)的最大价值, 取较大的就是当前格子之前所能取到的最大价值premx
                # premx加上当前礼物价值, 即为当前格子所能获得的最大价值
                premx = 0
                if r - 1 >= 0:
                    premx = max(premx, grid[r - 1][c])
                if c - 1 >= 0:
                    premx = max(premx, grid[r][c - 1])
                # 原地修改矩阵, 累加premx
                grid[r][c] += premx
        # 最后结果即为右下角的值
        return grid[rows - 1][cols - 1]
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
