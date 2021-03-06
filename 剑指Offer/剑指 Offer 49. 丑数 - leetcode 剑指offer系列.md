> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/chou-shu-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。

- 1 是丑数。
- n 不超过 1690。

## 题目样例

### 示例

- 输入: n = 10
- 输出: 12
- 解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。

## 题目思考

1. 如何从当前的丑数得到后面的丑数?
2. 如何保证从小到大?

## 解决方案

### 方案 1

#### 思路

- 一个比较容易想到的思路是使用一个小顶堆, 每次从堆顶 pop 出当前最小的丑数, 然后乘以 2/3/5 得到新丑数, 如果新丑数没有在堆中的话, 就将其加入堆中
- 这样 pop n 次即为第 n 个丑数
- 判断新丑数是否存在, 可以额外使用一个集合, 这样判断存在性就只需要 O(1)
- 显然初始化堆和集合中的元素都是 1, 代表第 1 个丑数
- 以上的操作是不是很类似 BFS 的思路? 不同的是这里利用了堆而不是双端队列/列表来处理当前的元素, 所以举一反三很重要

#### 复杂度

- 时间复杂度 O(NlogN): 共需要 N 次堆操作, 每次堆操作的时间复杂度是 logN
- 空间复杂度 O(N): 使用了一个小顶堆和一个集合

#### 代码

```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        # 初始化集合和堆中元素都为1
        v = {1}
        q = [1]
        for i in range(n):
            res = heapq.heappop(q)
            for factor in (2, 3, 5):
                nex = res * factor
                if nex not in v:
                    # 新丑数没在堆里的话, 将其加入堆中
                    v.add(nex)
                    heapq.heappush(q, nex)
        return res
```

### 方案 2

#### 思路

- 回顾方案 1, 因为引入了堆, 所以时间复杂度达到了 O(NlogN), 那有没有更优的方案呢, 比如 O(N) 时间复杂度?
- 答案也是有的, 我们重新分析题目, 要求数字的质因子只有 2/3/5, 我们就可以把当前丑数乘以 2/3/5 的数字分别存入三个数组中, 并将 1 作为第 1 个值, 这样就可以将题目转换成将**三个有序数组进行合并去重后求第 n 个最小值**
  - `arr2 = [1*2, 2*2, 3*2, 4*2, 5*2, 6*2, 8*2, ...] (注意7不是丑数)`
  - arr3 和 arr5 类似, 只是把乘数改成了 3 和 5
- **为什么需要去重呢?** 举个例子, 对于 6, 它既存在于 arr2 中, 也存在于 arr3 中
- **如何合并去重呢?** 我们可以维护 3 个指针, 分别对应这三个数组遍历到的元素位置, 那么当前最小值自然就是 3 个元素中最小的那个了, 然后将最小元素对应的指针后移(可能会遇到最小值不止一个, 这个时候移动的指针也不止一个), 继续判断即可
- **需要保存哪些数组呢?** 注意 arr2/arr3/arr5 有个共同特点是作为因子的丑数序列是相同的, 都是`[1,2,3,4,5,6,8,...]`, 只是需要乘的质因子不同. 所以我们并没有必要真正保存 3 个数组, 而只需要保存升序丑数序列即可, 这样恰好该序列的第 n 个数就是最终结果. **而 arr2/arr3/arr5 只需要在该丑数序列基础上乘以 2/3/5 即可得到**, 然后三个数组的指针移动还和上面的分析一样
- 下面的代码对必要步骤有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 只需要遍历一遍
- 空间复杂度 O(N): 额外使用了一个数组存升序丑数序列

#### 代码

```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        # 初始化丑数序列第一个元素为1
        ugly = [1]
        # 初始化arr2/arr3/arr5的下标都为0
        i2, i3, i5 = 0, 0, 0
        while len(ugly) < n:
            # 取arr2/arr3/arr5三者中的最小值追加到当前升序丑数序列中
            # 根据下面三个if判断的逻辑, 新追加的值一定大于之前丑数序列的最大值(最后一个值), 因为之前的最大值若等同于arr2/arr3/arr5的下标对应的值的话, 会将下标+1的, 新下标的值一定大于原下标的
            ugly.append(min(
                2 * ugly[i2],
                3 * ugly[i3],
                5 * ugly[i5],
            ))
            if ugly[-1] == 2 * ugly[i2]:
                # 最小值和arr2下标的值*2一样, i2加1
                i2 += 1
            if ugly[-1] == 3 * ugly[i3]:
                # 同上
                i3 += 1
            if ugly[-1] == 5 * ugly[i5]:
                # 同上
                i5 += 1
        return ugly[-1]
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
