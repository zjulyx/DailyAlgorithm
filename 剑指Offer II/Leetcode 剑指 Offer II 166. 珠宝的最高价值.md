> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

- 现有一个记作二维矩阵 frame 的珠宝架，其中 frame[i][j] 为该位置珠宝的价值。拿取珠宝的规则为：
  - 只能从架子的左上角开始拿珠宝
  - 每次可以移动到右侧或下侧的相邻位置
  - 到达珠宝架子的右下角时，停止拿取
- 注意：珠宝的价值都是大于 0 的。除非这个架子上没有任何珠宝，比如 frame = [[0]]。

### 示例 1：

- 输入：frame = [[1,3,1],[1,5,1],[4,2,1]]
- 输出：12
- 解释：路径 1→3→5→2→1 可以拿到最高价值的珠宝

### 提示：

- 0 < frame.length <= 200
- 0 < frame[0].length <= 200

## 题目思考

1. 每一个格子的珠宝最多价值跟什么有关?

## 解决方案

### 思路

- 分析题目, 只能向右或向下移动, 那么每个格子的最大价值只受左边一格和上边一格(如果存在的话)的最大价值的影响, 所以当前格子之前所能获得的最大价值 `premx` 就是两者中的较大值
- 假设 dp[r,c]代表格子(r,c)所能获得的最大价值, 自然就有`dp[r,c] = max(dp[r-1,c], dp[r,c-1]) + frame[r][c] (r-1>=0 and c-1>=0)`
- 而对于起点和左/上边界的情况(即不能同时满足`r-1>=0 and c-1>=0`时), 有:
  - 如果是左上角, 那么显然最大价值就是本身, `premx = 0`
  - 如果是左边界, 那么意味着只能从上边一格往下, 之前的最大价值就是上边一格的最大价值, `premx = frame[r - 1][c]`
  - 如果是上边界, 那么意味着只能从左边一格往右, 之前的最大价值就是左边一格的最大价值, `premx = frame[r][c - 1]`
- 所以我们可以把这几种情况统一起来, 每次遍历到一个格子时, 将 `premx` 置为 0, 然后如果左边或者上边一格存在的话, 就更新 `premx` 为其中的较大值即可
- 最终结果自然就是右下角的 dp 值
- 以上就是典型的动态规划的思想, 利用前面的计算结果来推导出当前的结果
- 另外这里还可以进行空间上的优化, 就是直接基于原数组进行修改, `frame[r][c]` 累加之前计算的 `premx` 即可, 这样就不需要额外 dp 矩阵了
- 下面的代码对必要步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 O(RC): 只需要遍历矩阵中的每个元素即可
- 空间复杂度 O(1): 原地修改矩阵, 只使用了几个变量

### 代码

```python
class Solution:
    def jewelleryValue(self, frame: List[List[int]]) -> int:
        rows, cols = len(frame), len(frame[0])
        for r in range(rows):
            for c in range(cols):
                # 分别求左边一格和上边一格(如果存在的话)的最大价值, 取较大的就是当前格子之前所能取到的最大价值premx
                # premx加上当前珠宝价值, 即为当前格子所能获得的最大价值
                premx = 0
                if r - 1 >= 0:
                    premx = max(premx, frame[r - 1][c])
                if c - 1 >= 0:
                    premx = max(premx, frame[r][c - 1])
                # 原地修改矩阵, 累加premx
                frame[r][c] += premx
        # 最后结果即为右下角的值
        return frame[rows - 1][cols - 1]
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
