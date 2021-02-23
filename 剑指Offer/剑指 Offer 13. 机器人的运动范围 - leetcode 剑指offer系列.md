> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

> 今天继续更新剑指 offer 系列, 这道题有至少三种解法, 很适合扩展思路

> 老样子**晚上 6 点 45 分**准时跟大家见面, 大家在我的公众号"每日精选算法题"中的聊天框中回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

地上有一个 m 行 n 列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于 k 的格子。例如，当 k 为 18 时，机器人能够进入方格 [35, 37] ，因为 3+5+3+7=18。但它不能进入方格 [35, 38]，因为 3+5+3+8=19。请问该机器人能够到达多少个格子？

- 1 <= n,m <= 100
- 0 <= k <= 20

## 题目样例

### 示例 1

#### 输入

m = 2, n = 3, k = 1

#### 输出

3

### 示例 2

#### 输入

m = 3, n = 1, k = 0

#### 输出

1

## 题目思考

1. 你能想到哪些思路?
2. 是否可以优化? (参考方案 3)

## 解决方案

### 方案 1

#### 思路

##### 分析

- 根据题意, 机器人可以向四个方向行走, 唯一的限制就是行列和不大于 k, 这个限制和传统 BFS 或 DFS 的边界限制其实是类似的, 所以也可以用相同的思路
- 方案 1 就使用 BFS: 维护一个队列和 visit 集合, 如果邻居满足条件(1.在方格内; 2.行列数位和不大于 k; 3.没被访问过, 即没在 visit 集合中)的话就加入队列和集合中

##### 实现

- 下面的代码有详细注释, 是 BFS 的经典模板了, 不熟悉的同学可以结合注释多思考下, 许多问题都可以复用这个模板并进行一些改动就能解决
- 注意行列数位和的求法, 这里也提供了两种方法实现

#### 复杂度

- 时间复杂度 `O(MN)`
  - 每个点被访问之后就会被加入 visit 集合, 只会被访问有限次, 所以时间复杂度就是点的个数`O(MN)`
- 空间复杂度 `O(MN)`
  - visit 集合的空间消耗

#### 代码

```python
class Solution:
    def movingCount(self, m: int, n: int, k: int) -> int:
        # 方法1: 经典BFS, 队列+visit集合
        q = [(0, 0)]
        v = set(q)

        def getSum(x):
            # 求数位和, 即循环累加模10的结果然后除以10即可
            res = 0
            while x:
                res += x % 10
                x //= 10
            return res
            # 注意: 这里也可以将数字转换成字符串, 然后逐位字符转int求sum
            # return sum([int(c) for c in str(x)])

        def isValid(r, c):
            # 判断某个下标是否有效: 是否在方格内/行列数位和是否不大于k/是否被访问过
            return 0 <= r < m and 0 <= c < n and getSum(r) + getSum(c) <= k and (r, c) not in v

        for point in q:
            r, c = point
            # 遍历四个方向的邻居
            for rr, cc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
                if isValid(rr, cc):
                    # 如果邻居有效的话, 将其加入BFS的queue里继续遍历
                    q.append((rr, cc))
                    v.add((rr, cc))
        return len(v)
```

### 方案 2

#### 思路

##### 分析

- 同样的, 这道题也可以用经典的 DFS
- 方案 2 就是这个思路: 维护 visit 集合, 如果邻居满足条件(1.在方格内; 2.行列数位和不大于 k; 3.没被访问过, 即没在 visit 集合中)的话就加入集合中, 并递归访问该邻居, 最后统计 visit 集合的元素数目即可
- 和昨天的`剑指 Offer 12. 矩阵中的路径 - leetcode 剑指offer系列` (大家在公众号里回复 **offer** 就能看到了)的 DFS 不一样的是, 这道题的 DFS 没有在递归调用邻居之后将其从 visit 中移除. 这是因为上道题中一个点可能出现在多条路径中, 所以必须调用完之后从集合移除, 避免之后的路径无法使用该节点; 而这道题只有一个共享的运动范围, 所以一个点如果出现在运动范围内的话, 那么它一定不需要再次被访问了, 直接永久加入 visit 集合中即可

##### 实现

- 下面的代码有详细注释, 也是 DFS 的经典模板

#### 复杂度

- 时间复杂度 `O(MN)`
  - 每个点被访问之后就会被加入 visit 集合, 只会被访问有限次, 所以时间复杂度就是点的个数`O(MN)`
- 空间复杂度 `O(MN)`
  - visit 集合的空间消耗

#### 代码

```python
class Solution:
    def movingCount(self, m: int, n: int, k: int) -> int:
        # 方法2: 经典DFS, 递归+visit集合
        v = {(0, 0)}

        def getSum(x):
            # 求数位和, 即循环累加模10的结果然后除以10即可
            res = 0
            while x:
                res += x % 10
                x //= 10
            return res
            # 注意: 这里也可以将数字转换成字符串, 然后逐位字符转int求sum
            # return sum([int(c) for c in str(x)])

        def isValid(r, c):
            # 判断某个下标是否有效: 是否在方格内/行列数位和是否不大于k/是否被访问过
            return 0 <= r < m and 0 <= c < n and getSum(r) + getSum(c) <= k and (r, c) not in v

        def dfs(r, c):
            for rr, cc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
                if isValid(rr, cc):
                    # 如果邻居有效的话, 将其加入集合中, 递归调用dfs该邻居
                    v.add((rr, cc))
                    dfs(rr, cc)
        # 初始从起点开始dfs
        dfs(0, 0)
        return len(v)
```

### 方案 3

#### 思路

##### 分析

- 回顾方案 1 和方案 2, 两种方案都需要遍历 4 个方向的邻居, 但我们真的有必要这样做吗?
- 再读题目, 起点是左上角, 所以第一步一定要么向下, 要么向右, 而第二步还是只能向下或向右, 否则就重走老路了, 以此类推, 每次移动都是向下或向右, 向左和向上一定要么出了边界, 要么已经在之前的行走中被访问过了
- 这样就得到了**优化 1**: 我们就可以将 4 个方向优化为只需要向下或向右的两个方向
- 于是就可以愉快地使用动态规划来求解啦, 因为每个点能否可达都可以根据其上边或左边的点的可达性得到
- 这里总结一个规律: **如果一个二维图需要遍历大于两个方向的邻居, 一般需要 BFS 或者 DFS; 而如果只需要两个相邻方向的话, 一般就可以用 DP 来根据递推关系求解 (因为对应方向的上个点的 dp 值可以在之前的计算中得到)**
- 另外注意我们每次循环可能会重复计算行或列的数位和, 这就引出了**优化 2**: 类似记忆化搜索的思想, 将计算所得的数位和缓存起来, 之后直接用缓存的值即可. 由于这里计算并不复杂, 所以优化不太明显, 但如果把数位和改成一个非常复杂的计算时, 这个优化就很有必要了.

##### 实现

- `dp[r,c]`表示下标`(r,c)`的点的可达性, 能走到就是 True, 否则就是 False
- 初始化`dp[0,0] = True`, 起点一定可达
- 如果某个`(r,c)`的数位和大于 k, 直接将其 dp 值设为 False
- 否则就根据其左边和上边的 dp 值来得到, 即`dp[r,c] = dp[r-1,c] or dp[r,c-1]`
- 注意 dp 是 bool 字典, 所以可以将其转换成一个集合, 集合中的元素就代表可达的行列下标, 这样最后结果就是 dp 的长度

#### 复杂度

- 时间复杂度 `O(MN)`
  - 需要遍历整个矩阵
- 空间复杂度 `O(MN)`
  - dp 字典/集合的空间消耗

#### 代码

```python
class Solution:
    def movingCount(self, m: int, n: int, k: int) -> int:
        # 方法3: DP
        # 如果rc数位和大于k, dp[r,c] = False
        # 否则dp[r,c] = dp[r-1,c] or dp[r,c-1]
        # 这里使用集合代替该bool字典, 初始元素是起点下标
        dp = {(0, 0)}
        # 另一个优化: 缓存数位和结果, 避免重复计算
        memo = {}

        def getSum(x):
            # 求数位和, 即循环累加模10的结果然后除以10即可
            if x not in memo:
                res = 0
                cur = x
                while cur:
                    res += cur % 10
                    cur //= 10
                memo[x] = res
                # 注意: 这里也可以将数字转换成字符串, 然后逐位字符转int求sum
                # memo[x] = sum([int(c) for c in str(x)])
            return memo[x]

        for r in range(m):
            for c in range(n):
                if getSum(r) + getSum(c) <= k and ((r-1, c) in dp or (r, c-1) in dp):
                    dp.add((r, c))
        return len(dp)
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
