> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

> 今天继续更新剑指 offer 系列, 个人感觉这道题出现频率极高, 而且其中的思路也可以用在很多其他题目中, 所以今天足足提供 **4** 种方案供大家参考

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入整数数组 arr ，找出其中最小的 k 个数。例如，输入 4、5、1、6、2、7、3、8 这 8 个数字，则最小的 4 个数字是 1、2、3、4。

- 0 <= k <= arr.length <= 10000
- 0 <= arr[i] <= 10000

## 题目样例

### 示例

输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]

## 题目思考

1. 最简单的方案是什么?
2. 如何一步步优化?

## 解决方案

### 方案 1

#### 思路

- 一个最简单的思路相信大家都很容易想到, 那就是排序然后取前 k 个, 一行代码完事..
- 但这种方案时间复杂度比较差, 特别是当 N 特别大的时候, 所以还是有很多可以优化的地方

#### 复杂度

- 时间复杂度 `O(NlogN)`
  - 需要排序
- 空间复杂度 `O(1)`
  - 不需要额外空间(原地排序时)

#### 代码

```python
class Solution:
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        return sorted(arr)[:k]
```

### 方案 2

#### 思路

- 维护一个大小为 k 的最大堆, 然后从头开始遍历:
  - 当堆中元素数目不足 k 的时候, 直接加入堆中
  - 否则要判断当前元素与堆顶大小, 如果当前元素更小的话, 就要拿它替代堆顶元素
- 这样最终堆中的元素就是最小的 k 个元素, **因为更大的元素在之前的遍历中就被替换出去了, 或者根本不会被加入堆中**
- 这里先使用语言自带的优先队列来实现, 相比自己实现的版本会有所优化, 且代码更为精简
- python 对应的就是 heapq, 不过需要注意 heapq 是最小堆, 所以我们需要取相反数人为将其变成最大堆..
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(NlogK)`
  - 只需要遍历数组一遍, 遍历的时候需要插入堆, 插入的时间复杂度为 `O(logK)`
- 空间复杂度 `O(K)`
  - 堆空间的消耗

#### 代码

```python
import heapq


class Solution:
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        # 注意k为0的特殊情况, 返回空数组
        if k == 0:
            return []
        maxheap = []
        for a in arr:
            # 关键判断条件: 堆元素数目小于k/当前元素小于堆顶
            if len(maxheap) < k or -maxheap[0] > a:
                # 注意取相反数转成最大堆
                heapq.heappush(maxheap, -a)
                # 如果堆元素超过k的话, 弹出堆顶
                if len(maxheap) > k:
                    heapq.heappop(maxheap)
        # 注意转回原来的数
        return [-x for x in maxheap]
```

### 方案 3

#### 思路

- 和方案 2 一样, 但是面试官可能会要求你自己实现一个最大堆, 那就自己实现吧..
- 可以使用一个数组来模拟堆, 然后根据下标关系模拟堆的上浮和下沉操作
- 注意这里有所简化, 因为不需要每次追加元素, 而是说每次用新元素替换堆顶, 所以只需要下沉操作即可
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(NlogK)`
  - 只需要遍历数组一遍, 遍历的时候需要插入堆, 插入的时间复杂度为 `O(logK)`
- 空间复杂度 `O(K)`
  - 堆空间的消耗

#### 代码

```python
class MaxHeap:
    def __init__(self, arr):
        self.heap = arr
        for i in range(len(arr) // 2 + 1)[::-1]:
            # 这里使用快速建堆法构造出大小为k的堆, 即从n/2到0依次做下沉操作
            # 也可以用插入法建堆, 那样可以初始化堆为空, 但需要额外实现上浮操作
            self.sink(i)

    def add(self, v):
        if v < self.heap[0]:
            # 只有当前元素小于堆顶时才替换并下沉
            self.heap[0] = v
            self.sink(0)

    def sink(self, i):
        # 下标从0开始, 所以左子节点下标是2i+1
        child = 2 * i + 1
        while child < len(self.heap):
            if child + 1 < len(
                    self.heap) and self.heap[child + 1] > self.heap[child]:
                # 右子节点存在且更大, 使用它
                child += 1
            if self.heap[child] > self.heap[i]:
                # 只有当子节点比当前节点大的时候才交换
                self.heap[i], self.heap[child] = self.heap[child], self.heap[i]
                i = child
                child = 2 * i + 1
            else:
                # 否则说明不需要继续下沉了, 直接退出
                break

class Solution:
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        if k == 0:
            return []
        # 初始化为前k个元素, 一次性下沉建堆
        maxheap = MaxHeap(arr[:k])
        for a in arr[k:]:
            # 此时堆中已有k个元素, 只需要比较堆顶和当前元素即可, 将相关逻辑封装在add方法中
            maxheap.add(a)
        return maxheap.heap
```

### 方案 4

#### 思路

- 回忆快速排序, 它的做法是根据 pivot 将数组分成两部分, 很契合这里的要求
- 所以我们可以直接利用它的思路, 不同的是划分之后这里只需要往一边递归, 类似二分查找的过程, 而不需要两边都排序
- 举个例子, 比如 n 是 10, k 是 3, 第一次划分之后的下标为 5, 那么我们只需要再考虑[0,4]的部分即可
- 下面代码有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度平均 `O(N)`, 最差`O(N^2)`
  - 如果每次划分都平分的话, 就是`N+N/2+N/4+...`的等比序列, 总和为 2N, 所以是 `O(N)`
  - 而最差情况是每次都只划分走了一个元素, 这样就是`N+(N-1)+(N-2)+...`, 就是 `O(N^2)`
- 空间复杂度 `O(logN)`
  - 递归的栈的平均深度

#### 代码

```python
class Solution:
    def getLeastNumbers(self, arr: List[int], k: int) -> List[int]:
        if k == 0:
            return []

        def partition(s, e):
            if s >= e:
                # 递归出口, 此时不需要额外划分
                return
            pivot = arr[s]
            i, j = s, e
            # 经典快速排序过程, 只是不需要像排序那样找到划分点后同时递归左右两个区间
            while i < j:
                # 先从后向前找第一个<pivot的数, 并放在上一个空出来的坑中(最开始空出来的坑就是左边界s, 它的值已经被存储下来了, 所以必须先从后向前找)
                while i < j and arr[j] >= pivot:
                    j -= 1
                arr[i] = arr[j]
                # 再从前向后找第一个>pivot的数, 并放在上一个空出来的坑中(下标j, 因为它已经被放入上一个下标为i的坑中了)
                while i < j and arr[i] <= pivot:
                    i += 1
                arr[j] = arr[i]
            arr[i] = pivot
            if i == k - 1:
                # 当前划分点恰好划分出前k个最小数(包含i元素本身), 直接返回即可
                # 当然这里用k来判断也行, 那下面的判断条件也要把k-1改成k, 就是说i左边的肯定是最小的k个
                return
            elif i < k - 1:
                # 当前划分点不能划分出前k个最小数, 需要往右继续找
                partition(i + 1, e)
            else:
                # 当前划分点划分了多于k个最小数, 需要往左继续找
                partition(s, i - 1)

        partition(0, len(arr) - 1)
        return arr[:k]
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
