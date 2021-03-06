> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

> 今天我们开始做股票最佳时机的最后一道困难题, 这道题和上一道题非常类似, 只是把交易次数限制变成了更加普遍的 k. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **股票** 就能看到了当前已经更新的股票系列了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。

注意:  你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

## 题目样例

### 示例 1

#### 输入

[2,4,1], k = 2

#### 输出

2

#### 解释

- 在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。

### 示例 2

#### 输入

[3,2,6,5,0,3], k = 2

#### 输出

7

#### 解释

- 在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。
- 随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。

## 题目思考

1. 相比上道题, 这道题不再局限 k 为 2, 我们还能利用类似的动态规划思路吗? 需要什么优化吗?

## 解决方案

### 思路

- **分析**
  - 这道题的大体思路和昨天的题目基本一致, 大家可能也注意到了, 昨天的题解中就没有固定 k 为 2, 所以我们完全可以复用昨天的代码
  - 但是进一步思考, k 没限制那么意味着 k 可能会很大, 比如远大于 n, 这样如果还用这个思路的话妥妥超时, 因为动态规划思路的时间复杂度为 O(NK)
  - 从另一个角度考虑, k 很大的话是不是就意味着相当于可以无限次交易了? 这样就能退化成 "122.买卖股票的最佳时机-ii" 这道题的贪心思路
  - 至于如何确定什么时候可以退化成贪心, 我们可以简单分析一下
    - 考虑每次交易, 都有一个买入和卖出, 它们至少需要是不同的两天
    - 如果当天卖出后立刻买入, 那么完全可以当天什么都不做, 可以和前后的交易合为一笔交易, 这也就意味着卖出和买入日期不能相同
    - 所以极限情况就是偶数下标日期买入(从 0 开始), 奇数下标日期卖出, 最大交易次数就是 n/2 (如果 n 为奇数的话就说明有一天是单独的, 无法构成有效交易, 所以最大交易次数仍为 n/2), 所以只要 `k>=n/2`, 那么就可以视为没有交易次数限制的情况
- **总结**
  - 为了方便大家查看, 我把之前的两个思路都搬了过来放在下面. 我们所要做的就是判断 k 和 n 的关系, 然后决定使用哪种思路
  - 这道题是买卖股票最佳时机系列的收尾问题, 大家只要掌握了文章中提到多次的动态规划的核心转移方程, 就完全可以应对多种变种了. 比如问题可以更复杂一点: 限制交易次数为 k, 然后每次交易有固定手续费和冷冻期, 求最大利润. 大家可以自己思考下应该如何处理~

#### 贪心部分的思路 (和 "122.买卖股票的最佳时机-ii" 完全相同)

- 可以买卖无限次, 那我们就尽可能地在每次能产生收益的时候都进行买卖
- 所以我们每次遇到下一个数比当前数大的时候, 我们就进行一次交易, 并把收益累加到最终结果中, 这样结果即为所求
- 注意这个过程并不违反题目规定 "不能同时参与多笔交易", 因为我们会在下一次买入前立刻卖出. 例如对于[1,2,3]这种情况, 我们在 1 的时候买入, 2 的时候卖出, 并重新买入(因为 3 比 2 大), 然后在 3 的时候再卖出, 总的最大收益就是 2

#### 动态规划部分的思路 (和 "123. 买卖股票的最佳时机 III" 完全相同)

- **分析**
  - 这道题我们同样可以采用动态规划的思路, 但是有了交易次数限制后, 我们必须多加一个维度, 代表当前交易次数下的最大收益. 当前最大收益只能关联到**交易次数更少**的之前的最大收益, 而不能像之前无限交易场景下可以直接利用前面的最大收益了
- **推导**
  - 设 dp[i, k]表示**允许交易 k 次且在第 i 天卖出**时能获得的最大收益, 相比之前的无限交易, 这次只能利用小于 k 次交易次数的结果
  - 我们仍然需要维护 mnprice, 但需要变成一个数组, mnprice[k]包含允许 k 次交易时的当前 i 天之前的最低价格. 这个最低价格仍然是[j:i-1]之间的最低价格, j 是上次卖出的日期. 不同交易限制下上次卖出的日期也不同, 所以每个 k 对应的 mnprice 也不一样, 这就是为什么我们需要维护多个 mnprice
  - 同样的, mxdp[k] 表示允许 k 次交易时在 i-1 天及之前的最大 dp 值. 如果当前的 dp[i-1, k] 比之前的 mxdp[k] 大的话, 我们就更新 mxdp[k] 和 mnprice[k] (此时新的 mnprice[k] 只能为 prices[i-1] 了)
  - 同样的, 允许 k 次交易时第 i 天卖出的最大收益为 `dp[i, k] = max(mxdp[k-1] + prices[i] - mnprice[k-1])`, 特别注意这里的 mxdp 和 mnprice 都取的是 k-1, 因为 `prices[i] - mnprice[k-1]`构成了新的一次交易, 使得交易次数达到了 k. 由于 prices[i] 是固定的, 所以我们只需要求 `max(mxdp[k-1] - mnprice[k-1])`. 可以将它们视为一个整体, 再引入一个数组 mxdiff, mxdiff[k-1] 就表示允许 k-1 次交易时截止到 i 天的最大 `mxdp - mnprice`. (因为不一定 i-1 天卖出就是之前所能得到的最大收益, 有可能更早卖出才是)
  - 特别注意一点, 当计算 dp[i,1]的时候, 由于 `k-1=0`, 表示不允许交易, 那么此时最大收益 mxdp[0]就一直为 0, 所以 mxdiff[0]就是 max(-mnprice[0]), 也即 `max(-prices[j]) (j < i)`, 我们也需要计算这个的值
- **总结**
  - 万变不离其宗, 核心转移方程仍为`dp[i, k] = max(mxdp[k-1] + prices[i] - mnprice[k-1]) = prices[i] + mxdiff[k-1]`, 只是多了个交易次数的维度
  - 下面的代码中对每一步有详细的注释, 帮助大家理解~

### 复杂度

- 时间复杂度
  - 如果 k>=n/2 时, 使用贪心算法, 时间复杂度为 O(N);
  - 否则使用动态规划, 时间复杂度为 O(NK)
- 空间复杂度:
  - 如果 k>=n/2 时, 使用贪心算法, 空间复杂度为 O(1);
  - 否则使用动态规划, dp 数组需要存 N\*K 个元素, 其他数组只需要 K 个元素, 所以空间复杂度为 O(NK). 但可以优化为只存 K 个元素, 因为 i 的 dp 值只和 i-1 的有关, 优化之后就只需要 O(K)空间了, 而且 hash 里面存的 key 变少了, 也会对运行时间有一定的加速. 下面的代码写出了两种方法, 未优化的方法更容易理解, 而优化后的方法节省一定空间和时间.

### 代码

#### 方法 1: 未优化空间复杂度 - O(NK)

##### Python 3

```python
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        n = len(prices)
        if k >= n // 2:
            # k大于等于数组长度的一半, 此时等价于无限交易, 直接利用"122.买卖股票的最佳时机-ii的做法"即可
            # 因为即使一天买入一天卖出, 总共也只有n//2次交易 (如果当天卖出后立刻买入, 那么完全可以和前后的交易合为一笔交易, 所以卖出和买入的时间各不相同)
            res = 0
            for i in range(1, len(prices)):
                res += max(0, prices[i] - prices[i - 1])
            return res
        # dp[i, k]表示允许k次交易情况下第i天的最大收益
        # 仍然可以用之前的mnprice/mxdp/mxdiff三变量思路, 但需要对每个允许交易的次数k都记录这三个变量
        # 不过需要注意的是dp[i,k]的值是基于mxdiff[k-1]而不是当前mxdiff[k]的, 因为mxdiff加上当前price之后就又构成了一次交易
        # 自然转移方程就是: dp[i, k] = max(mxdp[k-1]+prices[i]-mnprice[k-1]) = prices[i] + mxdiff[k-1]
        dp = collections.defaultdict(int)
        # mnprice[k] - 允许k次交易前提下, 上一次卖出之后到当前的最低价格
        # mxdp[k] - 允许k次交易前提下, i-1天及之前所能获得的最高收益
        # mxdiff[k] - 允许k次交易前提下, 最大的mxdp-mnprice
        mnprice, mxdp, mxdiff = [float('inf')] * (k + 1), [0] * (
            k + 1), [-float('inf')] * (k + 1)
        res = 0
        for i in range(1, n):
            # 注意如果不允许交易(k=0的情况), 那么意味着最大收益mxdp只能为0, mxdiff就自然等于-price的最大值了
            mxdiff[0] = max(mxdiff[0], -prices[i - 1])
            for kk in range(1, k + 1):
                # 下面的结果都是基于允许kk次交易的前提
                # 更新最低价格
                mnprice[kk] = min(mnprice[kk], prices[i - 1])
                if dp[i - 1, kk] > mxdp[kk]:
                    # i-1天时利润更大, 更新mxdp
                    mxdp[kk] = dp[i - 1, kk]
                    # 更新之后表示要在i-1天卖出, 需要重置mnprice, 只能为i-1天的价格了
                    mnprice[kk] = prices[i - 1]
                # 仍然将mxdp-mnprice视为一个整体, 更新允许kk次交易时最大的mxdp-mnprice
                mxdiff[kk] = max(mxdiff[kk], mxdp[kk] - mnprice[kk])
                # mxdiff[kk-1]即为允许kk-1次交易的最大利润, 那么加上prices[i]后就又多了次交易, 也即当前的dp[i,kk]了
                dp[i, kk] = prices[i] + mxdiff[kk - 1]
                res = max(res, dp[i, kk])
        return res
```

##### C++

```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int res = 0;
        int n = prices.size();

        if (k >= n / 2) {
            for (int i = 1; i < n; ++i) {
                res += max(0, prices[i] - prices[i - 1]);
            }
            return res;
        }

        vector<vector<int>> dp(n, vector<int>(k + 1, 0));
        vector<int> mnprice(k + 1, INT_MAX);
        vector<int> mxdp(k + 1, 0);
        vector<int> mxdiff(k + 1, INT_MIN);

        for (int i = 1; i < n; ++i) {
            mxdiff[0] = max(mxdiff[0], -prices[i - 1]);
            for (int j = 1; j <= k; ++j) {
                mnprice[j] = min(mnprice[j], prices[i - 1]);
                if (dp[i-1][j] > mxdp[j]) {
                    mxdp[j] = dp[i - 1][j];
                    mnprice[j] = prices[i - 1];
                }
                mxdiff[j] = max(mxdiff[j], mxdp[j] - mnprice[j]);
                dp[i][j] = prices[i] + mxdiff[j - 1];
                res = max(res, dp[i][j]);
            }
        }

        return res;
    }
};
```

#### 方法 2: 优化空间复杂度 - O(K)

##### Python 3

```python
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        n = len(prices)
        if k >= n // 2:
            res = 0
            for i in range(1, len(prices)):
                res += max(0, prices[i] - prices[i - 1])
            return res
        mnprice, mxdp, mxdiff = [float('inf')] * (k + 1), [0] * (
            k + 1), [-float('inf')] * (k + 1)
        # 使用两个dp数组分别存i和i-1的dp值
        dp, predp = [0] * (k + 1), [0] * (k + 1)
        res = 0
        for i in range(1, n):
            mxdiff[0] = max(mxdiff[0], -prices[i - 1])
            for kk in range(1, k + 1):
                # 注意此处不能通过倒序循环的方式优化为只使用一个dp数组, 因为下面的mxdiff依赖的是当前i情况下计算过的kk-1的值, 而不是i-1时计算的
                mnprice[kk] = min(mnprice[kk], prices[i - 1])
                if predp[kk] > mxdp[kk]:
                    mxdp[kk] = predp[kk]
                    mnprice[kk] = prices[i - 1]
                mxdiff[kk] = max(mxdiff[kk], mxdp[kk] - mnprice[kk])
                dp[kk] = prices[i] + mxdiff[kk - 1]
                res = max(res, dp[kk])
                predp[kk] = dp[kk]
        return res
```

##### C++

```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int res = 0;
        int n = prices.size();

        if (k >= n / 2) {
            for (int i = 1; i < n; ++i) {
                res += max(0, prices[i] - prices[i - 1]);
            }
            return res;
        }

        vector<int> mnprice(k + 1, INT_MAX);
        vector<int> mxdp(k + 1, 0);
        vector<int> mxdiff(k + 1, INT_MIN);
        vector<int> dp(k + 1, 0);
        vector<int> predp(k + 1, 0);

        for (int i = 1; i < n; ++i) {
            mxdiff[0] = max(mxdiff[0], -prices[i - 1]);
            for (int j = 1; j <= k; ++j) {
                mnprice[j] = min(mnprice[j], prices[i - 1]);
                if (predp[j] > mxdp[j]) {
                    mxdp[j] = predp[j];
                    mnprice[j] = prices[i - 1];
                }
                mxdiff[j] = max(mxdiff[j], mxdp[j] - mnprice[j]);
                dp[j] = prices[i] + mxdiff[j - 1];
                res = max(res, dp[j]);
                predp[j] = dp[j];
            }
        }
        return res;
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
