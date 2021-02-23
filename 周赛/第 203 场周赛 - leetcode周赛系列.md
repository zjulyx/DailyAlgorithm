> [竞赛链接](https://leetcode-cn.com/contest/weekly-contest-203/)

> 好久没更新周赛系列了, 正好这周末宅家, 就来试试拿个参与奖吧 😂 结果还不错, 最终排名 77 / 5284

# [5495] 圆形赛道上经过次数最多的扇区

> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/most-visited-sector-in-a-circular-track/)

## 题目描述

给你一个整数 n 和一个整数数组 rounds 。有一条圆形赛道由 n 个扇区组成，扇区编号从 1 到 n 。现将在这条赛道上举办一场马拉松比赛，该马拉松全程由 m 个阶段组成。其中，第 i 个阶段将会从扇区 rounds[i - 1] 开始，到扇区 rounds[i] 结束。举例来说，第 1 阶段从 rounds[0] 开始，到 rounds[1] 结束。

请你以数组形式返回经过次数最多的那几个扇区，按扇区编号 升序 排列。

注意，赛道按扇区编号升序逆时针形成一个圆（请参见第一个示例）。

- 2 <= n <= 100
- 1 <= m <= 100
- rounds.length == m + 1
- 1 <= rounds[i] <= n
- rounds[i] != rounds[i + 1] ，其中 0 <= i < m

## 题目样例

### 示例 1

![示例 1](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/08/22/3rd45e.jpg)

- 输入：n = 4, rounds = [1,3,1,2]
- 输出：[1,2]
- 解释：本场马拉松比赛从扇区 1 开始。经过各个扇区的次序如下所示：
- 1 --> 2 --> 3（阶段 1 结束）--> 4 --> 1（阶段 2 结束）--> 2（阶段 3 结束，即本场马拉松结束）
- 其中，扇区 1 和 2 都经过了两次，它们是经过次数最多的两个扇区。扇区 3 和 4 都只经过了一次。

### 示例 2

- 输入：n = 2, rounds = [2,1,2,1,2,1,2,1,2]
- 输出：[2]

### 示例 3

- 输入：n = 7, rounds = [1,3,5,7]
- 输出：[1,2,3,4,5,6,7]

## 题目思考

1. 如何简化问题?

## 解决方案

### 思路

- 题目 blahblah 说了很长, 但仔细分析可以发现, 中间部分对结果完全没影响, 中间不管有多少个值多少圈, 对于每个扇区增加的次数都是相同的
- 所以我们可以只考虑起点和终点, 简化为一圈的情况, 这一圈经过的扇区是额外的部分, 最终结果只需要考虑起点和终点途径的扇区即可
- 注意终点可能小于起点, 这时候就要先从 1 到终点, 再从起点遍历到 n 即可(因为最终结果要按顺序)

### 复杂度

- 时间复杂度 `O(N)`: 只需要遍历起点和终点之间的部分
- 空间复杂度 `O(1)`: 只使用了几个变量

### 代码

```python
class Solution:
    def mostVisited(self, n: int, rounds: List[int]) -> List[int]:
        s, e = rounds[0], rounds[-1]
        if s <= e:
            # [起点, 终点]
            return list(range(s, e + 1))
        else:
            # [1, 终点]+[起点, n]
            return list(range(1, e + 1)) + list(range(s, n + 1))
```

---

# [5496] 你可以获得的最大硬币数目

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/maximum-number-of-coins-you-can-get/)

## 题目描述

有 3n 堆数目不一的硬币，你和你的朋友们打算按以下方式分硬币：

每一轮中，你将会选出 任意 3 堆硬币（不一定连续）。
Alice 将会取走硬币数量最多的那一堆。
你将会取走硬币数量第二多的那一堆。
Bob 将会取走最后一堆。
重复这个过程，直到没有更多硬币。
给你一个整数数组 piles ，其中 piles[i] 是第 i 堆中硬币的数目。

返回你可以获得的最大硬币数目。

- 3 <= piles.length <= 10^5
- piles.length % 3 == 0
- 1 <= piles[i] <= 10^4

## 题目样例

### 示例 1

- 输入：piles = [2,4,1,2,7,8]
- 输出：9
- 解释：选出 (2, 7, 8) ，Alice 取走 8 枚硬币的那堆，你取走 7 枚硬币的那堆，Bob 取走最后一堆。
- 选出 (1, 2, 4) , Alice 取走 4 枚硬币的那堆，你取走 2 枚硬币的那堆，Bob 取走最后一堆。
- 你可以获得的最大硬币数目：7 + 2 = 9.
- 考虑另外一种情况，如果选出的是 (1, 2, 8) 和 (2, 4, 7) ，你就只能得到 2 + 4 = 6 枚硬币，这不是最优解。

### 示例 2

- 输入：piles = [2,4,5]
- 输出：4

### 示例 3

- 输入：piles = [9,8,7,6,5,1,2,3,4]
- 输出：18

## 题目思考

1. 如何保证自己每次拿到的都是最优的?

## 解决方案

### 思路

- 贪心法, 既然我们只能拿第二大的, 那每次都把最大的留给 Alice, 然后我们选第二大的, 再把当前最小的塞给 Bob 就行了
- 转换成代码就是先排序, 然后从大到小遍历, 从第二大的开始拿, 每次中间隔一个值(把当前最大的留给 Alice), 直到我们拿够 n 个硬币 (总共 3n 个硬币)

### 复杂度

- 时间复杂度 `O(NlogN)`: 需要排序
- 空间复杂度 `O(1)`: 只使用了几个变量

### 代码

```python
class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        piles.sort()
        cnt = 0
        res = 0
        for i in range(len(piles) - 2, -1, -2):
            # 从次大值开始, 隔一个硬币拿一次
            res += piles[i]
            cnt += 1
            if cnt == len(piles) // 3:
                break
        return res
```

---

# [5497] 查找大小为 M 的最新分组

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/find-latest-group-of-size-m/)

## 题目描述

给你一个数组 arr ，该数组表示一个从 1 到 n 的数字排列。有一个长度为 n 的二进制字符串，该字符串上的所有位最初都设置为 0 。

在从 1 到 n 的每个步骤 i 中（假设二进制字符串和 arr 都是从 1 开始索引的情况下），二进制字符串上位于位置 arr[i] 的位将会设为 1 。

给你一个整数 m ，请你找出二进制字符串上存在长度为 m 的一组 1 的最后步骤。一组 1 是一个连续的、由 1 组成的子串，且左右两边不再有可以延伸的 1 。

返回存在长度 恰好 为 m 的 一组 1  的最后步骤。如果不存在这样的步骤，请返回 -1 。

- n == arr.length
- 1 <= n <= 10^5
- 1 <= arr[i] <= n
- arr 中的所有整数 互不相同
- 1 <= m <= arr.length

## 题目样例

### 示例 1

- 输入：arr = [3,5,1,2,4], m = 1
- 输出：4
- 解释：
- 步骤 1："00100"，由 1 构成的组：["1"]
- 步骤 2："00101"，由 1 构成的组：["1", "1"]
- 步骤 3："10101"，由 1 构成的组：["1", "1", "1"]
- 步骤 4："11101"，由 1 构成的组：["111", "1"]
- 步骤 5："11111"，由 1 构成的组：["11111"]
- 存在长度为 1 的一组 1 的最后步骤是步骤 4 。

### 示例 2

- 输入：arr = [3,1,5,4,2], m = 2
- 输出：-1
- 解释：
- 步骤 1："00100"，由 1 构成的组：["1"]
- 步骤 2："10100"，由 1 构成的组：["1", "1"]
- 步骤 3："10101"，由 1 构成的组：["1", "1", "1"]
- 步骤 4："10111"，由 1 构成的组：["1", "111"]
- 步骤 5："11111"，由 1 构成的组：["11111"]
- 不管是哪一步骤都无法形成长度为 2 的一组 1 。

### 示例 3

- 输入：arr = [1], m = 1
- 输出：1

### 示例 4

- 输入：arr = [2,1], m = 2
- 输出：2

## 题目思考

1. 需要哪些数据结构?
2. 当前位置变成 1 之后会改变哪些位置的连续 1 的长度?

## 解决方案

### 思路

- 分析题目, 某个位置变成 1 之后, 最直接的影响就是其左右两边, 左右两边连续的 1 的长度都会变成新的总长度
- 所以首先我们需要一个字典 iToLen, 键值对为`{i:len}`, 存储某个下标对应的连续 1 的长度, 用于动态更新
- 其次我们还需要一个反向字典 lenToCnt, 键值对为`{len:cnt}`, 存储当前连续 1 长度对应的个数, 那么每次只要这个字典里 m 对应的 cnt 大于 0, 就说明仍有连续 1 长度为 m 的部分
- 如果我们在每次把某个位置变成 1 之后都修改左右两边所有连续 1 的位置的 iToLen 字典, 这样时间复杂度就达到了 O(N^2), 大概率会超时
- 但真的有必要修改所有下标吗? 答案是否定的, 其实我们只需要修改新的连续 1 的起点和终点的 iToLen 字典即可, **因为后面操作里新的 1 的左右两边绝不可能是当前连续 1 的中间部分, 只需要考虑两个边界就行**, 这样就把这部分操作从 O(N)降到了 O(1)
- 然后就是修改反向字典 lenToCnt 了, 这个也很简单, 就是拿到原来左右两侧连续 1 的长度 left 和 right, 将其对应的值各减去 left 和 right, 因为这些下标的长度都不再是原来的值了, 然后再把总长度 left+right+1 在 lenToCnt 字典中的值加上 left+right+1 即可
- 下面代码对必要的步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 `O(N)`: 只遍历了数组一遍
- 空间复杂度 `O(N)`: 使用了额外两个字典, 需要存 N 个元素

### 代码

```python
class Solution:
    def findLatestStep(self, arr: List[int], m: int) -> int:
        n = len(arr)
        lenToCnt = collections.defaultdict(int)
        iToLen = {}
        res = -1
        for index, x in enumerate(arr):
            # 转成以0为起点的下标
            i = x - 1
            # 原来的左侧和右侧的连续1的长度
            left = 0
            right = 0
            # 新的连续1的起点和终点下标, 初始化为当前下标
            start = i
            end = i
            if i - 1 >= 0 and i - 1 in iToLen:
                # 更新左侧长度和起点下标
                left = iToLen[i - 1]
                start -= left
            if i + 1 < n and i + 1 in iToLen:
                # 更新右侧长度和终点下标
                right = iToLen[i + 1]
                end += right
            newlen = left + right + 1
            # 更新iToLen字典, 只需要更新两个边界即可
            iToLen[start] = newlen
            iToLen[end] = newlen
            # 更新lenToCnt字典, 减去旧长度的值, 加上新长度的值
            lenToCnt[left] -= left
            lenToCnt[right] -= right
            lenToCnt[newlen] += newlen
            if lenToCnt[m] > 0:
                # 如果仍有连续1长度为m的部分, 更新最终结果为当前arr下标+1
                res = index + 1
        return res
```

---

# [5498] 石子游戏 V

> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/stone-game-v/)

## 题目描述

几块石子 排成一行 ，每块石子都有一个关联值，关联值为整数，由数组 stoneValue 给出。

游戏中的每一轮：Alice 会将这行石子分成两个 非空行（即，左侧行和右侧行）；Bob 负责计算每一行的值，即此行中所有石子的值的总和。Bob 会丢弃值最大的行，Alice 的得分为剩下那行的值（每轮累加）。如果两行的值相等，Bob 让 Alice 决定丢弃哪一行。下一轮从剩下的那一行开始。

只 剩下一块石子 时，游戏结束。Alice 的分数最初为 0 。

返回 Alice 能够获得的最大分数 。

- 1 <= stoneValue.length <= 500
- 1 <= stoneValue[i] <= 10^6

## 题目样例

### 示例 1

- 输入：stoneValue = [6,2,3,4,5,5]
- 输出：18
- 解释：在第一轮中，Alice 将行划分为 [6，2，3]，[4，5，5] 。左行的值是 11 ，右行的值是 14 。Bob 丢弃了右行，Alice 的分数现在是 11 。
- 在第二轮中，Alice 将行分成 [6]，[2，3] 。这一次 Bob 扔掉了左行，Alice 的分数变成了 16（11 + 5）。
- 最后一轮 Alice 只能将行分成 [2]，[3] 。Bob 扔掉右行，Alice 的分数现在是 18（16 + 2）。游戏结束，因为这行只剩下一块石头了。

### 示例 2

- 输入：stoneValue = [7,7,7,7,7,7,7]
- 输出：28

### 示例 3

- 输入：stoneValue = [4]
- 输出：0

## 题目思考

1. 如何避免重复计算?

## 解决方案

### 思路

- 一般对于这种博弈问题, 都可以先尝试用记忆化搜索的思路来解决, 这个题也不例外
- 但这道题不需要双方做最优决策, 只需要 Alice 一个人来分, 所以不需要额外一个 flag 来判断当前是谁的回合
- 直接模拟整个过程, 传入当前元组(转成元组的目的是可以作为 memo 字典的 key), 然后依次遍历当前元组, 动态求得左侧和右侧部分的和, 根据题目描述的情况继续递归, 最终求得最大值作为当前元组的最终结果, 加入 memo 字典中
- 递归出口是元组长度为 1 的时候, 此时游戏结束, 直接返回 0 即可
- 下面代码对必要的步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 `O(N^3)`: memo 字典中最多存 N^2 个状态(对应长度为 2~N 的元组, 长度为 2 时有 N-1 个元组, 为 N 时有 1 个, 所以前 N-1 项和就是 N^2 数量级), 然后每次递归的时候需要遍历 N 个数
- 空间复杂度 `O(N^2)`: memo 字典需要存 `2*N` 个状态

### 代码

```python
class Solution:
    def stoneGameV(self, stoneValue: List[int]) -> int:
        # 记忆化搜索
        memo = {}

        def getMx(t):
            if len(t) == 1:
                # 递归出口, 只有1个石头, 返回0
                return 0
            # 用memo字典存当前元组下的最大值, 避免重复计算
            if t not in memo:
                # 先计算当前的总和
                sm = sum(t)
                # 记录左侧行的当前和
                leftsm = 0
                mx = 0
                for i in range(len(t) - 1):
                    leftsm += t[i]
                    # 根据总和以及当前左侧和得到右侧和
                    rightsm = sm - leftsm
                    # 模拟题目的三种情况, 求不同分片下的最大值, 作为当前元组的最终结果
                    if leftsm < rightsm:
                        mx = max(mx, leftsm + getMx(t[:i + 1]))
                    elif leftsm == rightsm:
                        mx = max(mx, leftsm + getMx(t[:i + 1]),
                                 leftsm + getMx(t[i + 1:]))
                    else:
                        mx = max(mx, rightsm + getMx(t[i + 1:]))
                memo[t] = mx

            return memo[t]

        return getMx(tuple(stoneValue))
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
