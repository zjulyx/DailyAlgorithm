> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

> 今天继续更新剑指 offer 系列, 这道题有一定难度, 对应的思路也比较巧妙, 大家可以尝试挑战一下~

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

例如，

- [2,3,4]  的中位数是 3

- [2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

- void addNum(int num) - 从数据流中添加一个整数到数据结构中。
- double findMedian() - 返回目前所有元素的中位数。

- 最多会对 addNum、findMedia 进行 50000 次调用。

## 题目样例

### 示例 1

#### 输入

s = "abaccdeff"

#### 输出

"b"

### 示例 2

#### 输入

`["MedianFinder","addNum","addNum","findMedian","addNum","findMedian"]`
`[[],[1],[2],[],[3],[]]`

#### 输出

`[null,null,2.00000,null,2.50000]`

## 题目思考

1. 观察数据规模, 会有 5W 次调用, 那么意味着每次调用的平均时间不能超过 O(N), 不然就需要 5W\*5W 的数量级, 有什么数据结构可以做到每次调用的时间复杂度小于 O(N) 呢?

## 解决方案

### 思路

- 根据中位数性质, 它要么是偶数长度数组的两个中间值的平均数, 要么是奇数长度数组的最中间的值
- 考虑到每次调用复杂度要小于 O(N), 显然 O(1)是不可能的, 我们没办法只根据当前插入的值直接判断更新后的中位值是什么, 必须通过一些扫描和判断
- 那么我们尝试 O(logN)复杂度的数据结构, 注意到中间值左边的部分一定是小于中间值的, 而右边的部分一定是大于中间值的, 是有一定的有序性的
- 我们可以利用这一点, 构造两个堆:
  - 左边是一个大顶堆, 存放所有小于等于中间值的数(奇数长度的话, 堆顶就是中间值)
  - 右边是一个小顶堆, 存放所有大于等于中间值的数(因为可能有很多重复元素)
- **查询中位数**
  - 当前元素个数为奇数时, 直接返回左堆顶
  - 当前元素个数为偶数时, 返回左堆顶和右堆顶的平均值
- **插入新元素**
  - 如果当前左堆和右堆个数相同, 那么左堆需要增加一个元素
    - 具体是增加新元素还是右堆顶, 则要看当前 num 和右堆顶的大小
    - 当前 num 更小的话直接插入左边即可
    - 否则就要先把右堆顶先挪到左堆, 然后右堆再插入新元素
  - 如果当前左堆比右堆多 1, 那么右堆需要增加一个元素
    - 具体是增加新元素还是左堆顶, 则要看当前 num 和左堆顶的大小
    - 当前 num 更大的话直接插入右边即可
    - 否则就要先把左堆顶先挪到右堆, 然后左堆再插入新元素
- 注意代码中实现了两个版本, 都有详细的注释. 一个是系统内置堆/优先队列, 更加简洁, 一个是自定义堆 (这里和昨天不同, 堆的下标从 1 开始, 这样父子节点的计算方式也就有所差别, 大家可以选择自己更喜欢的方式), 方便大家复习堆的构造~

### 复杂度

- 时间复杂度 `O(logN)`
  - 每次调用只需要常数次堆操作, 复杂度为 O(logN)
- 空间复杂度 `O(N)`
  - 两个堆共需要存 N 个元素

### 代码

#### 方法 1 - 使用内置优先队列/堆

##### Python

```python
import heapq

class MedianFinder:
    def __init__(self):
        """
        initialize your data structure here.
        """
        # 使用两个堆, 左边大顶堆, 右边小顶堆
        # 注意python内置的heapq是小顶堆
        # 所以大顶堆的话需要将其值取相反数再插入, 也不要忘了pop的时候也要再取反回来
        self.left = []
        self.right = []

    def addNum(self, num: int) -> None:
        if len(self.left) == len(self.right):
            # 左堆需要增加一个元素
            if not self.left or num <= self.right[0]:
                # 左堆不存在, 或者新元素不大于右堆顶, 插到左边大顶堆
                heapq.heappush(self.left, -num)
            else:
                # 否则先把右堆顶插到左边, 然后右堆再插入新元素
                heapq.heappush(self.left, -heapq.heappop(self.right))
                heapq.heappush(self.right, num)
        else:
            # 右堆需要增加一个元素
            # 根据插入逻辑, 此时左堆至少有一个元素, 可以直接拿到左堆顶
            if num >= -self.left[0]:
                # 新元素不小于左堆顶, 直接插到右边小顶堆即可
                heapq.heappush(self.right, num)
            else:
                # 否则先把左堆顶插到右边, 然后左堆再插入新元素
                heapq.heappush(self.right, -heapq.heappop(self.left))
                heapq.heappush(self.left, -num)

    def findMedian(self) -> float:
        if not self.left:
            return 0
        if len(self.left) == len(self.right):
            # 偶数个元素, 取两个堆顶的平均值
            res = (-self.left[0] + self.right[0]) / 2
        else:
            # 奇数个元素, 左堆个数至少为1, 取左堆顶
            res = -self.left[0]
        return res
```

#### 方法 2 - 自定义最大最小堆

##### Python

```python
class Heap:
    def __init__(self, heapType):
        # smallHeap用于标记这是一个小顶堆还是大顶堆
        self.smallHeap = True if heapType == 'SmallHeap' else False
        # 注意下标为0的元素不会被用, 此处作为占位符
        self.heap = [0]

    def __len__(self):
        return len(self.heap)

    def empty(self):
        return len(self.heap) == 1

    def push(self, v):
        # 先在堆末尾加入新元素
        self.heap.append(v)
        # 然后进行上浮操作
        cur = len(self.heap) - 1
        parent = cur >> 1
        while parent >= 1:
            if self.smallHeap and self.heap[parent] > self.heap[
                    cur] or not self.smallHeap and self.heap[
                        parent] < self.heap[cur]:
                self.heap[cur], self.heap[parent] = self.heap[
                    parent], self.heap[cur]
                cur, parent = parent, parent >> 1
            else:
                break

    def pop(self):
        res = self.heap[1]
        tail = self.heap.pop()
        if self.empty():
            return res
        # 将堆末尾元素弹出并放到堆顶
        self.heap[1] = tail
        # 然后进行下沉操作
        cur = 1
        child = 2
        while child < len(self.heap):
            if child + 1 < len(self.heap):
                if self.smallHeap and self.heap[child + 1] < self.heap[
                        child] or not self.smallHeap and self.heap[
                            child + 1] > self.heap[child]:
                    child += 1
            if self.smallHeap and self.heap[child] < self.heap[
                    cur] or not self.smallHeap and self.heap[
                        child] > self.heap[cur]:
                self.heap[cur], self.heap[child] = self.heap[child], self.heap[
                    cur]
                cur, child = child, child << 1
            else:
                break
        return res


class MedianFinder:
    def __init__(self):
        """
        initialize your data structure here.
        """
        # 使用两个堆, 左边大顶堆, 右边小顶堆
        self.left = Heap('BigHeap')
        self.right = Heap('SmallHeap')

    def addNum(self, num: int) -> None:
        if len(self.left) == len(self.right):
            # 左堆需要增加一个元素
            if self.left.empty() or num <= self.right.heap[1]:
                # 左堆不存在, 或者新元素不大于右堆顶, 插到左边大顶堆
                self.left.push(num)
            else:
                # 否则先把右堆顶插到左边, 然后右堆再插入新元素
                self.left.push(self.right.pop())
                self.right.push(num)
        else:
            # 右堆需要增加一个元素
            # 根据插入逻辑, 此时左堆至少有一个元素, 可以直接拿到左堆顶
            if num >= self.left.heap[1]:
                # 新元素不小于左堆顶, 直接插到右边小顶堆即可
                self.right.push(num)
            else:
                # 否则先把左堆顶插到右边, 然后左堆再插入新元素
                self.right.push(self.left.pop())
                self.left.push(num)

    def findMedian(self) -> float:
        if self.left.empty():
            return 0
        if len(self.left) == len(self.right):
            # 偶数个元素, 取两个堆顶的平均值
            res = (self.left.heap[1] + self.right.heap[1]) / 2
        else:
            # 奇数个元素, 左堆个数至少为1, 取左堆顶
            res = self.left.heap[1]
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
