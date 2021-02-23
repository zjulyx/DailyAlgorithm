> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

> 今天我们继续来做股票包含"手续费"的这道中等问题, 这道题和昨天"冷冻期"的问题挺类似的. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **股票** 就能看到了当前已经更新的股票系列了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个整数数组  prices，其中第  i  个元素代表了第  i  天的股票价格 ；非负整数  fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

- 0 < prices.length <= 50000.
- 0 < prices[i] < 50000.
- 0 <= fee < 50000.

## 题目样例

### 示例

#### 输入

prices = [1, 3, 2, 8, 4, 9], fee = 2

#### 输出

8

#### 解释

能够达到的最大利润:

- 在此处买入 prices[0] = 1
- 在此处卖出 prices[3] = 8
- 在此处买入 prices[4] = 4
- 在此处卖出 prices[5] = 9

总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

## 题目思考

1. 把冷冻期换成手续费后, 我们还能利用类似的动态规划思路吗?

## 解决方案

### 思路

- **分析**
  - 这道题我们同样可以采用动态规划的思路, 因为当前最大收益和之前的最大收益有很强的关联关系. P.S. 想要回顾昨天冷冻期思路的同学们可以在公众号"每日精选算法题"里回复 **股票** 查看 (309. 最佳买卖股票时机含冷冻期)
- **推导**
  - 设 dp[i]表示**在第 i 天卖出**时能获得的最大收益, 相比冷冻期, 这次我们可以直接利用 i-1 天的结果了
  - 我们仍然需要维护 mnprice, 表示当前 i 天之前的最低价格. 这个最低价格仍然是[j:i-1]之间的最低价格, j 是上次卖出的日期
  - 同样的, mxdp 表示在 i-1 天及之前的最大 dp 值. 如果当前的 dp[i-1] 比之前的 mxdp 大的话, 我们就更新 mxdp 和 mnprice (此时新的 mnprice 只能为 prices[i-1] 了)
  - 同样的, 第 i 天卖出的最大收益为 `dp[i] = max(mxdp + prices[i] - mnprice - fee)`, 多了一个减去 fee 的部分. 由于 prices[i]和 fee 是固定的, 所以我们只需要求 `max(mxdp - mnprice)`. 可以将它们视为一个整体, 再引入一个变量 mxdiff 来求截止到 i 天的最大 `mxdp - mnprice` 即可. (因为不一定 i-1 天卖出就是之前所能得到的最大收益, 有可能更早卖出才是)
- **总结**
  - 万变不离其宗, 核心转移方程仍为`dp[i] = max(mxdp + prices[i] - mnprice - fee) = prices[i] - fee + mxdiff`, 只是多了一个手续费的固定数值
  - 下面的代码中对每一步有详细的注释, 帮助大家理解~

### 复杂度

- 时间复杂度 O(N): 需要遍历整个数组一遍
- 空间复杂度 O(1): 这里对 dp 数组进行了空间优化, 只需要存前一个 dp 值和当前 dp 值即可, 所以是 O(1)

### 代码

#### Python 3

```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        # 只需要使用两个变量存dp
        # 仍然需要mxdp, mnprice和mxdiff
        n = len(prices)
        # predp - 第i-1天卖出的最高收益
        # curdp - 第i天卖出的最高收益
        # mxdp - i-1天及之前所能获得的最高收益
        predp, curdp, mxdp = 0, 0, 0
        # mnprice - 上一次卖出之后到当前的最低价格
        # mxdiff - 最大的mxdp-mnprice
        mnprice, mxdiff, res = float('inf'), -float('inf'), 0
        for i in range(1, n):
            # 更新最低价格
            mnprice = min(mnprice, prices[i - 1])
            if predp > mxdp:
                # 当前predp比mxdp更大, 更新它
                mxdp = predp
                # 同时mnprice也要被更新, 因为上一次卖出是i-1, 现在可能值只能是prices[i-1]了(即立即买入)
                mnprice = prices[i - 1]
            # 更新mxdp - mnprice这个整体的最大值
            mxdiff = max(mxdiff, mxdp - mnprice)
            # 更新predp和curdp, curdp就是prices[i] + mxdiff - fee
            predp = curdp
            curdp = prices[i] + mxdiff - fee
            # 更新最终结果
            res = max(res, curdp)
        return res
```

#### C++

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int predp = 0, curdp = 0, mxdp = 0;
        int mnprice = INT_MAX, mxdiff = INT_MIN, res = 0;
        for (int i = 1; i < prices.size(); ++i) {
            mnprice = min(mnprice, prices[i - 1]);
            if (predp > mxdp) {
                mxdp = predp;
                mnprice = prices[i - 1];
            }
            mxdiff = max(mxdiff, mxdp - mnprice);
            predp = curdp;
            curdp = prices[i] + mxdiff - fee;
            res = max(res, curdp);
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
