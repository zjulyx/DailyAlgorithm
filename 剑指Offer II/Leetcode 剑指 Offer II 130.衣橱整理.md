> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

家居整理师将待整理衣橱划分为 m x n 的二维矩阵 grid，其中 grid[i][j] 代表一个需要整理的格子。整理师自 grid[0][0] 开始 逐行逐列 地整理每个格子。

整理规则为：在整理过程中，可以选择 向右移动一格 或 向下移动一格，但不能移动到衣柜之外。同时，不需要整理 digit(i) + digit(j) > cnt 的格子，其中 digit(x) 表示数字 x 的各数位之和。

请返回整理师 总共需要整理多少个格子。

### 示例 1：

- 输入：m = 4, n = 7, cnt = 5
- 输出：18

### 提示：

- 1 <= n, m <= 100
- 0 <= cnt <= 20

## 题目思考

1. 如何快速判断某个格子是否可达?

## 解决方案

### 思路

#### 分析

- 起点是左上角, 且只能向下或向右走, 我们可以使用动态规划来求解, 因为每个点能否可达都可以根据其上边或左边的点的可达性得到
- 这里总结一个规律: **如果一个二维图需要遍历大于两个方向的邻居, 一般需要 BFS 或者 DFS; 而如果只需要两个相邻方向的话, 一般就可以用 DP 来根据递推关系求解 (因为对应方向的上个点的 dp 值可以在之前的计算中得到)**
- 另外注意我们每次循环可能会重复计算行或列的数位和, 这就引出了一个优化: 类似记忆化搜索的思想, 将计算所得的数位和缓存起来, 之后直接用缓存的值即可
- 由于这里计算并不复杂, 所以优化不太明显, 但如果把数位和改成一个非常复杂的计算时, 这个优化就很有必要了

##### 实现

- `dp[r,c]`表示下标`(r,c)`的点的可达性, 能走到就是 True, 否则就是 False
- 初始化`dp[0,0] = True`, 起点一定可达
- 如果某个`(r,c)`的数位和大于 k, 直接将其 dp 值设为 False
- 否则就根据其左边和上边的 dp 值来得到, 即`dp[r,c] = dp[r-1,c] or dp[r,c-1]`
- 注意 dp 是 bool 字典, 所以可以将其转换成一个集合, 集合中的元素就代表可达的行列下标, 这样最后结果就是 dp 的长度

#### 复杂度

- 时间复杂度 `O(MN)`: 需要遍历整个矩阵
- 空间复杂度 `O(MN)`: dp 字典/集合的空间消耗

### 代码

```python
class Solution:
    def wardrobeFinishing(self, m: int, n: int, cnt: int) -> int:
        # DP, 如果rc数位和大于cnt, dp[r,c] = False
        # 否则dp[r,c] = dp[r-1,c] or dp[r,c-1]
        # 这里使用集合代替该bool字典, 初始元素是起点下标, 结果就是集合的长度
        dp = {(0, 0)}

        # 优化: 缓存数位和结果, 避免重复计算
        @functools.cache
        def digit(x):
            # 求数位和, 即循环累加模10的结果然后除以10即可
            # 注意: 这里也可以将数字转换成字符串, 然后逐位字符转int求sum
            # return sum([int(c) for c in str(x)])
            res = 0
            while x:
                res += x % 10
                x //= 10
            return res

        for r in range(m):
            for c in range(n):
                if digit(r) + digit(c) <= cnt and ((r - 1, c) in dp or (r, c - 1) in dp):
                    dp.add((r, c))
        return len(dp)
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
