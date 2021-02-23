> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/product-of-the-last-k-numbers/)

> 今天继续来做一道中等设计题, 提示一下, 这道题和我之前的一篇文章 "[Leetcode 每日精选] 560. 和为 K 的子数组"在思路上有异曲同工之处, 大家在我的公众号"每日精选算法题"中的聊天框中回复 **子数组和** 就能看到啦~

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

请你实现一个「数字乘积类」ProductOfNumbers，要求支持下述两种方法：

1. add(int num)

- 将数字  num  添加到当前数字列表的最后面。

2. getProduct(int k)

- 返回当前数字列表中，最后  k  个数字的乘积。
- 你可以假设当前列表中始终 至少 包含 k 个数字。

题目数据保证：任何时候，任一连续数字序列的乘积都在 32-bit 整数范围内，不会溢出。

- add 和 getProduct 两种操作加起来总共不会超过 40000 次。
- 0 <= num <= 100
- 1 <= k <= 40000

## 题目样例

### 示例

#### 输入

`["ProductOfNumbers","add","add","add","add","add","getProduct","getProduct","getProduct","add","getProduct"]`
`[[],[3],[0],[2],[5],[4],[2],[3],[4],[8],[2]]`

#### 输出

`[null,null,null,null,null,null,20,40,0,null,32]`

#### 解释

```java
ProductOfNumbers productOfNumbers = new ProductOfNumbers();
productOfNumbers.add(3);        // [3]
productOfNumbers.add(0);        // [3,0]
productOfNumbers.add(2);        // [3,0,2]
productOfNumbers.add(5);        // [3,0,2,5]
productOfNumbers.add(4);        // [3,0,2,5,4]
productOfNumbers.getProduct(2); // 返回 20 。最后 2 个数字的乘积是 5 * 4 = 20
productOfNumbers.getProduct(3); // 返回 40 。最后 3 个数字的乘积是 2 * 5 * 4 = 40
productOfNumbers.getProduct(4); // 返回  0 。最后 4 个数字的乘积是 0 * 2 * 5 * 4 = 0
productOfNumbers.add(8);        // [3,0,2,5,4,8]
productOfNumbers.getProduct(2); // 返回 32 。最后 2 个数字的乘积是 4 * 8 = 32
```

## 题目思考

1. 我们先观察数据规模, 共计 4W 次操作, 如果我们每次求最后 k 个乘积的时候, 都从后往前遍历肯定会超时, 因为这样的话数量级就在 4W\*4W 了
2. 那么可以利用什么思路, 或者什么数据结构, 可以优化每次操作的时间复杂度呢, 至少要让它小于 O(N)

## 解决方案

### 思路

1. 先设计需要使用的数据结构
   - 对于这种要求连续 k 个区间的和/乘积的问题, 我们都可以利用前缀的思路进行优化, 所以和 560 那道题类似, 我们可以构造一个前缀列表, 存从开头到当前元素的乘积, 那么在求最后 k 个乘积的时候只需要用最后的乘积除以 n-k-1 对应的乘积即可, 结果即为[n-k,n-1]这个长度为 k 的区间的乘积了
   - 但你可能会注意到, 上述思路中忽略了一种可能情况, 就是如果其中数字有 0 如何处理? 我们必须要考虑这种情况, 不然就会有除以 0 的错误了
   - 所以额外引入一个变量 lastZero, 代表最后一个 0 对应的下标, 初始化为-1, 表示不存在最后一个 0
   - 然后把上面的列表稍作改造, 如果遇到 0 的时候, 就从下一个元素开始重新累积乘积, 这样就能保证遇到 0 之后, 后面的前缀乘积信息不会丢失(即全变为 0)
2. 接下来就是写具体的逻辑了, 这里一共有 2 种操作:
   1. **添加数字**
      1. 如果当前前缀乘积列表为空或者上一个乘积为 0 的话, 直接加入当前数字, 从头开始累积乘积
      2. 然后如果当前数字为 0 的话, 更新 lastZero 下标为当前列表长度-1(即最后一个元素)
   2. **求最后 k 个数的乘积**
      1. 假设此时列表有 n 个数
      2. 首先计算 prev, 表示需要被除的乘积对应的下标, 那么 `prev = n-k-1`, [prev+1, n-1]部分总共有 k 个数, 其乘积即为最后 k 个数的乘积
      3. 然后根据 prev 和 lastZero 的关系, 对应有以下三种情况:
         1. 当 lastZero 下标介于上面区间的话(即`prev < lastZero`), 那么最后 k 个数肯定要乘以至少一个 0, 所以最终结果为 0
         2. 当 prev 正好为-1(即 k==n, 求当前全部元素的乘积)或者 prev 正好就是最后一个 0 的下标时, 那么结果就是 product[n-1]
         3. 当区间里没有 0 的时候(即 lastZero 下标小于 prev 时), 也即 `product[n-1]/product[prev]`
3. 最后就是具体的代码部分了, 下面代码对每步操作都有详细的注释, 希望可以帮助大家更好理解

### 复杂度

- 时间复杂度 O(1): 每种操作都只需要常数次运算
- 空间复杂度 O(N): 前缀列表需要存 N 个元素

### 代码

#### Python 3

```python
class ProductOfNumbers:
    def __init__(self):
        # 不需要维护每个元素自身, 只需要维护一个前缀乘积和最后一个0的下标即可
        # self.product记录从最后一个0(如果存在的话, 不存在就是从列表开头开始)的下一个元素到最后一个元素的乘积
        # 这样就不需要每次添加一个新数重新计算k个乘积了
        # query的时候就是最后一个乘积除以n-k-1位置的乘积, 注意0的处理
        self.product = []
        self.lastZero = -1

    def add(self, num: int) -> None:
        if not self.product or self.product[-1] == 0:
            # 当前列表为空, 或者上一个乘积是0, 那么从头开始累积乘积
            self.product.append(num)
        else:
            # 将上一个乘积乘以当前数, 作为新的乘积
            self.product.append(num * self.product[-1])
        if num == 0:
            # 更新lastZero下标
            self.lastZero = len(self.product) - 1

    def getProduct(self, k: int) -> int:
        n = len(self.product)
        # prev表示需要除以的乘积对应的下标, [prev+1, n-1]部分总共有k个数, 其乘积即为最后k个数的乘积
        prev = len(self.product) - k - 1
        if prev < self.lastZero:
            # 当lastZero下标介于上面区间的话(即prev<lastZero), 那么最后k个数肯定要乘以至少一个0, 所以最终结果为0
            return 0
        if prev == -1 or prev == self.lastZero:
            # 当prev正好为-1(即k==n, 求当前全部元素的乘积)或者prev正好就是最后一个0的下标时, 那么结果就是product[n-1]
            return self.product[-1]
        # 当区间里没有0的时候(即lastZero下标小于prev时), 也即product[n-1]/product[prev]
        return self.product[-1] // self.product[prev]
```

#### C++

```cpp
class ProductOfNumbers {
public:
    void add(int num) {
        if (product.empty() || !product.back()) {
            product.push_back(num);
        } else {
            product.push_back(num * product.back());
        }
        if (!num) {
            lastZero = product.size() - 1;
        }
    }

    int getProduct(int k) {
        int prev = product.size() - k - 1;
        if (prev < lastZero) {
            return 0;
        }
        if (prev == -1 || prev == lastZero) {
            return product.back();
        }
        return product.back() / product[prev];
    }

private:
    int lastZero{-1};
    vector<int> product;
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
