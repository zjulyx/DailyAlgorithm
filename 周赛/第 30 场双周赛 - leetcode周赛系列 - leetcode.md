# [5177] 转变日期格式

## 题目思考

1. 如何快速转换日和月?
2. 需要进行哪些特殊处理?

## 解决方案

### 思路

- 由于题目说了输入都合法, 所以直接将输入按照空格拆分成`日月年`, 然后分别对其处理即可
- Day: 都有两个字符的后缀, 去掉之后, 然后对于个位数前面补 0 即可
- Month: 直接将输入表示为数组, 找对应的下标+1, 即为月份数字, 同样对于个位数前面补 0
- Year: 最简单无需转换

### 复杂度

- 时间复杂度 `O(1)`: 常数次操作
- 空间复杂度 `O(1)`: 只使用了几个变量

### 代码

```python
class Solution:
    def reformatDate(self, date: str) -> str:
        months = [
            "Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep",
            "Oct", "Nov", "Dec"
        ]
        arr = date.split(' ')
        day, mon, year = arr
        # 去掉day的后缀, 注意个位数要补0
        day = '0' + day[:1] if len(day) == 3 else day[:2]
        for i in range(len(months)):
            if months[i] == mon:
                # 找到month下标, 注意个位数要补0
                mon = str(i + 1)
                if len(mon) == 1:
                    mon = '0' + mon
                break
        return year + '-' + mon + '-' + day
```

---

# [5445] 子数组和排序后的区间和

## 题目思考

1. 如何求所有非空连续子数组的和?

## 解决方案

### 思路

- 先观察数据规模, n 最大只有 `10^3`, `O(N^2)` 甚至 `O(N^2logN)` 算法应该都没问题
- 然后尝试直接模拟, 非空连续子数组和可以通过两重循环求得, 固定子数组起点, 依次计算前缀和即可
- 然后直接排序, 求[left,right]的和, 不要忘了对 10^9 + 7 取模

### 复杂度

- 时间复杂度 `O(N^2logN)`: 需要两重循环, 这部分是`O(N^2)`; 然后需要对`N^2`规模的子数组和数组排序, 这部分是 O`(N^2log(N^2)) = O(N^2logN)`. 所以总体就是 `O(N^2logN)`
- 空间复杂度 `O(N^2)`: 额外的子数组和数组的元素个数

### 代码

```python
class Solution:
    def rangeSum(self, nums: List[int], n: int, left: int, right: int) -> int:
        sms = []
        for i in range(len(nums)):
            sm = 0
            for x in nums[i:]:
                sm += x
                # 记录所有连续子数组和
                sms.append(sm)
        # 子数组和排序
        sms.sort()
        mod = 10**9 + 7
        # 求对应区间的和, 注意left和right是以1开头的, 另外还要模10**9 + 7
        return sum(sms[left - 1:right]) % mod
```

---

# [5446] 三次操作后最大值与最小值的最小差

## 题目思考

1. 改成任意值等价于什么?
2. 如何更高效地得到最大值和最小值?

## 解决方案

### 思路

- 很显然当数组长度小于等于 4 时, 可以把任意三个改成和剩下的一样, 这样最小差值就是 0
- 进一步推理, 可以发现把其中 3 个改成任意值, 其实等价于将它们 3 个从数组中移除, 因为它们完全可以变得和数组中另外一个值相等
- 所以一个很自然的思路就是贪心法, 总是移除最大的 3 个数和最小的 3 个数中的靠近边界的 3 个, 然后找移除后使得最大最小值差值最小的方案即可
- 举个例子, 假设数组是`[0,1,2,3,4,5]`, 我们可以移除`0,1,2`, `0,1,5`, `0,4,5`以及`3,4,5`, 一共这 4 种方案, 然后求这些方案中移除后的最大最小值差值的最小值
- 所以我们可以对数组进行排序(`O(NlogN)`), 或者只求最大的 4 个和最小的 4 个元素(`O(N)`, 取 4 个是因为可能把其中一边的 3 个全移除了, 需要用第 4 个和另一边的最值求查), 然后找最小值即可
- 这里的代码为了简单起见使用了排序的方法, 只用 3 行代码即可. 感兴趣的同学也可以自行尝试求最大最小 4 个元素的方法, 可以降低时间复杂度

### 复杂度

- 时间复杂度 `O(NlogN)` 或 `O(N)`: 排序的话需要`O(NlogN)`, 求最大最小 4 个元素只需要`O(N)`
- 空间复杂度 `O(1)`: 只使用了几个元素

### 代码

```python
class Solution:
    def minDifference(self, nums: List[int]) -> int:
        # 可以把任意3个变成剩余的元素, 最小值一定是0
        if len(nums) <= 4: return 0
        nums.sort()
        # 依次对应四种方案: 去除最小3个/去除最小2个和最大1个/去除最小1个和最大2个/去除最大3个
        return min(nums[-1] - nums[3], nums[-2] - nums[2], nums[-3] - nums[1],
                   nums[-4] - nums[0])
```

---

# [5447] 石子游戏 IV

## 题目思考

1. 如何设计最优策略?

## 解决方案

### 思路

- 一般对于这种博弈问题, 都可以先尝试用记忆化搜索的思路来解决, 这个题也不例外
- 具体来说就是定义一个子方法, 参数是当前石头数目以及当前是谁的回合这两个状态, 返回则是最优策略下这个人能不能赢
- 然后分为以下几种情况:
  - **当前石头数目为 0**: 不可能赢, 返回 false
  - **当前石头数目是平方数**: 一定赢, 返回 true
  - **不是以上两种情况**: 则要考虑所有可能的拿石头方案, 也即从 `1,4,9,...` 这样拿平方数的石头(且要小于当前石头数目), 看在剩余石头状态下对方是不是一定输. 是的话则说明找到了一个稳赢方案, 返回 true; 否则则说明不管拿哪种平方数, 对方一定能赢, 那自己这边只能返回 false 了
- 把(当前石头数目, 当前是谁的回合)作为二元组缓存起来, 这样可以避免重复计算, 就是记忆化搜索的思想

### 复杂度

- 时间复杂度 `O(N*sqrt(N))`: 对于每个石头数目, 可能需要循环`sqrt(N)`次求所有可能的剩余石头数, 所以总的时间复杂度是 `O(N*sqrt(N))`
- 空间复杂度 `O(N)`: memo 字典需要存 `2*N` 个状态

### 代码

```python
class Solution:
    def winnerSquareGame(self, n: int) -> bool:
        # 记忆化搜索
        memo = {}

        def willwin(n, alice):
            # n是剩余石头数目
            # alice = True表示当前是alice的回合
            if (n, alice) not in memo:
                if n == 0:
                    # 没有石头可选, 必输
                    memo[n, alice] = False
                else:
                    root = int(math.sqrt(n))
                    if root * root == n:
                        # 当前n是平方数, 必赢
                        memo[n, alice] = True
                    else:
                        for j in range(1, root + 1):
                            if not willwin(n - j * j, not alice):
                                # 如果对手在这种情况下必输, 则自己必赢
                                memo[n, alice] = True
                                break
                        else:
                            # 自己不管拿哪种平方数都无法做到对手必输, 那自己必输
                            memo[n, alice] = False
            return memo[n, alice]

        # 初始情况是有n个石头, 且是Alice的回合
        return willwin(n, True)
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
