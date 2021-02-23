> [竞赛链接](https://leetcode-cn.com/contest/weekly-contest-187/)

> 今天更新一场周赛, 信息量有点大, 大家可以收藏起来慢慢看 🤣

> 这场周赛总体难度适中, 难度上升曲线比较平滑, 值得一试, 同学们可以根据自己实际情况阅读~

# [1436] 旅行终点站

> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/destination-city)

## 题目描述

给你一份旅游线路图，该线路图中的旅行线路用数组 paths 表示，其中 paths[i] = [cityAi, cityBi] 表示该线路将会从 cityAi 直接前往 cityBi 。请你找出这次旅行的终点站，即没有任何可以通往其他城市的线路的城市。

题目数据保证线路图会形成一条不存在循环的线路，因此只会有一个旅行终点站。

## 题目样例

### 示例 1

#### 输入

paths = [["London","New York"],["New York","Lima"],["Lima","Sao Paulo"]]

#### 输出

"Sao Paulo"

#### 解释

从 "London" 出发，最后抵达终点站 "Sao Paulo" 。本次旅行的路线是 "London" -> "New York" -> "Lima" -> "Sao Paulo" 。

### 示例 2

#### 输入

paths = [["B","C"],["D","B"],["C","A"]]

#### 输出

"A"

#### 解释

所有可能的线路是：

- "D" -> "B" -> "C" -> "A".
- "B" -> "C" -> "A".
- "C" -> "A".
- "A".

显然，旅行终点站是 "A" 。

### 示例 3

#### 输入

paths = [["A","Z"]]

#### 输出

"Z"

## 题目思考

1. 题目虽然花里胡哨, 但似乎跟图没有关系?
2. 是不是可以单纯的统计下城市?

## 解决方案

### 思路

- 使用两个集合, 表示所有城市和是起点的城市, 因为题目保证只有一个终点站, 所以其差集一定就是那个城市

### 复杂度

- 时间复杂度 O(N): 需要遍历所有城市
- 空间复杂度 O(N): 需要使用两个集合

### 代码

#### Python 3

```python

class Solution:
    def destCity(self, paths: List[List[str]]) -> str:
        # 两个集合求差集, 一个是全部节点, 一个是有出度的节点
        al, out = set(), set()
        for p in paths:
            al.add(p[0])
            al.add(p[1])
            out.add(p[0])
        return (al - out).pop()
```

#### C++

```cpp
class Solution {
public:
    string destCity(vector<vector<string>>& paths) {
        unordered_set<string> all, out;

        for (auto& entry : paths) {
            all.insert(entry[0]);
            all.insert(entry[1]);
            out.insert(entry[0]);
        }

        for (auto& s : all) {
            if (out.find(s) == out.end()) {
                return s;
            }
        }

        return {};
    }
};
```

---

# [1437] 是否所有 1 都至少相隔 k 个元素

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/check-if-all-1s-are-at-least-length-k-places-away/description/)

## 题目描述

给你一个由若干 0 和 1 组成的数组 nums 以及整数 k。如果所有 1 都至少相隔 k 个元素，则返回 True ；否则，返回 False 。

## 题目样例

### 示例 1

#### 输入

nums = [1,0,0,0,1,0,0,1], k = 2

#### 输出

true

#### 解释

每个 1 都至少相隔 2 个元素。

### 示例 2

#### 输入

nums = [1,0,0,1,0,1], k = 2

#### 输出

false

#### 解释

第二个 1 和第三个 1 之间只隔了 1 个元素。

### 示例 3

#### 输入

nums = [1,1,1,1,1], k = 0

#### 输出

true

## 题目思考

1. 题目很直白, 依次遍历可行吗?

## 解决方案

### 思路

- 记录上一个 1 的下标, 然后逐个遍历, 遇到 1 之后检查间隔, 同时更新 1 的下标

### 复杂度

- 时间复杂度 O(N): 需要遍历所有元素
- 空间复杂度 O(1): 只使用了几个变量

### 代码

#### Python 3

```python
class Solution:
    def kLengthApart(self, nums: List[int], k: int) -> bool:
        # 记录上一个1的下标即可, 注意下标之差要大于k+1而不是k, 因为是间隔k
        last1 = None
        for i, n in enumerate(nums):
            if n == 1:
                if last1 is None or i - last1 >= k + 1:
                    last1 = i
                else:
                    return False
        return True
```

#### C++

```cpp
class Solution {
public:
    bool kLengthApart(vector<int>& nums, int k) {
        int last1 = -1;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i]) {
                if (last1 >= 0 && i - last1 < k + 1) {
                    return false;
                }
                last1 = i;
            }
        }
        return true;
    }
};
```

---

# [1438] 绝对差不超过限制的最长连续子数组

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/description/)

## 题目描述

给你一个整数数组 nums ，和一个表示限制的整数 limit，请你返回最长连续子数组的长度，该子数组中的任意两个元素之间的绝对差必须小于或者等于 limit 。

如果不存在满足条件的子数组，则返回 0 。

## 题目样例

### 示例 1

#### 输入

nums = [8,2,4,7], limit = 4

#### 输出

2

#### 解释

所有子数组如下：

- [8] 最大绝对差 |8-8| = 0 <= 4.
- [8,2] 最大绝对差 |8-2| = 6 > 4.
- [8,2,4] 最大绝对差 |8-2| = 6 > 4.
- [8,2,4,7] 最大绝对差 |8-2| = 6 > 4.
- [2] 最大绝对差 |2-2| = 0 <= 4.
- [2,4] 最大绝对差 |2-4| = 2 <= 4.
- [2,4,7] 最大绝对差 |2-7| = 5 > 4.
- [4] 最大绝对差 |4-4| = 0 <= 4.
- [4,7] 最大绝对差 |4-7| = 3 <= 4.
- [7] 最大绝对差 |7-7| = 0 <= 4.

因此，满足题意的最长子数组的长度为 2 。

### 示例 2

#### 输入

nums = [10,1,2,4,7,2], limit = 5

#### 输出

4

#### 解释

满足题意的最长子数组是 [2,4,7,2]，其最大绝对差 |2-7| = 5 <= 5 。

### 示例 3

#### 输入

nums = [4,2,2,2,4,4,2,2], limit = 0

#### 输出

3

## 题目思考

1. 连续子数组即区间, 是否可以用滑动窗口?
2. 任意两个元素之间的绝对差最大值是否存在另外一种表示?
3. 能否利用某些数据结构的特性?

## 解决方案

### 方案 1

#### 思路

- 滑动窗口+优先队列(最大最小堆)
- 某区间任意两个元素之间的绝对差最大值等价于该区间最大值-最小值
- 所以只需要维护两个优先队列, 一个存当前区间的最大值+下标(最大堆), 一个存最小值+下标(最小堆)即可
- 存下标的目的是窗口左边界往右滑动时移除无效元素

#### 复杂度

- 时间复杂度 O(N\*log(N)): 每个元素只会遍历两遍, 然后堆本身 push 和 pop 需要 logN 的复杂度
- 空间复杂度 O(N): 使用了两个堆

#### 代码

##### Python 3

```python
import heapq

class Solution:
    def longestSubarray(self, nums: List[int], limit: int) -> int:
        l, r = 0, 0
        mx, mn = [], []
        res = 0
        while r < len(nums):
            heapq.heappush(mx, (-nums[r], r))
            heapq.heappush(mn, (nums[r], r))
            while -mx[0][0] - mn[0][0] > limit:
                # 加上右边界后不满足要求了, 左边界一直往右移动, 直到继续满足要求
                # 同时需要更新两个堆, 如果堆顶元素的下标小于等于当前左边界的话
                while mx and mx[0][1] <= l:
                    heapq.heappop(mx)
                while mn and mn[0][1] <= l:
                    heapq.heappop(mn)
                l += 1
            res = max(res, r - l + 1)
            r += 1
        return res
```

##### C++

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        int res = 0;
        multimap<int, int, less<int>> incr;
        multimap<int, int, greater<int>> desc;

        for (int l = 0, r = 0; r < nums.size(); ++r) {
            incr.emplace(nums[r], r);
            desc.emplace(nums[r], r);
            while(desc.begin()->first - incr.begin()->first > limit) {
                while (!incr.empty() && incr.begin()->second <= l) {
                    incr.erase(incr.begin());
                }
                while (!desc.empty() && desc.begin()->second <= l) {
                    desc.erase(desc.begin());
                }
                ++l;
            }
            res = max(res, r - l + 1);
        }

        return res;
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 两个堆存在的意义只是为了拿到它们的最大值和最小值, 那么是不是可以用单调双向队列来替代它呢, 这样入队出队的复杂度就变成 O(1)了, 相比方案 1 会快不少
- 在滑动窗口的基础上, 只需要维护两个双端队列, 一个递增一个递减
- 新的元素放在队列末尾, 然后需要左边界移动的时候只用检查队列开头即可
- 这里额外带来一个好处是不再需要存下标了, 因为队列最左边的就是最老的, 移动左边界时直接比较左边的值即可

#### 复杂度

- 时间复杂度 O(N): 每个元素只需要遍历四次, 两次入队两次出队
- 空间复杂度 O(N): 两个双端队列

#### 代码

##### Python 3

```python
from collections import deque

class Solution:
    def longestSubarray(self, nums: List[int], limit: int) -> int:
        l, r = 0, 0
        inc, desc = deque(), deque()
        res = 0
        while r < len(nums):
            while inc and inc[-1] > nums[r]:
                inc.pop()
            inc.append(nums[r])
            while desc and desc[-1] < nums[r]:
                desc.pop()
            desc.append(nums[r])
            while desc[0] - inc[0] > limit:
                # 加上当前右边界后不满足要求了, 左边界一直往右移动, 直到继续满足要求
                # 同时需要更新两个队列, 如果队列头恰好是当前左边界的话
                if inc[0] == nums[l]:
                    inc.popleft()
                if desc[0] == nums[l]:
                    desc.popleft()
                l += 1
            res = max(res, r - l + 1)
            r += 1
        return res
```

##### C++

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        int res = 0;
        deque<int> incr, desc;

        for (int l = 0, r = 0; r < nums.size(); ++r) {
            while (!incr.empty() && incr.back() > nums[r]) {
                incr.pop_back();
            }
            incr.push_back(nums[r]);
            while (!desc.empty() && desc.back() < nums[r]) {
                desc.pop_back();
            }
            desc.push_back(nums[r]);
            while (desc.front() - incr.front() > limit) {
                if (incr.front() == nums[l]) {
                    incr.pop_front();
                }
                if (desc.front() == nums[l]) {
                    desc.pop_front();
                }
                ++l;
            }
            res = max(res, r - l + 1);
        }
        return res;
    }
};
```

---

# [1439] 有序矩阵中的第 k 个最小数组和

> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/find-the-kth-smallest-sum-of-a-matrix-with-sorted-rows/description/)

> 这道题的基础版本是[[373] 查找和最小的 K 对数字](https://leetcode-cn.com/problems/find-k-pairs-with-smallest-sums/description/), 如果感觉这道题有难度的话可以先从那道题入手

## 题目描述

给你一个 m \* n 的矩阵 mat，以及一个整数 k ，矩阵中的每一行都以非递减的顺序排列。

你可以从每一行中选出 1 个元素形成一个数组。返回所有可能数组中的第 k 个 最小 数组和。

- m == mat.length
- n == mat.length[i]
- 1 <= m, n <= 40
- 1 <= k <= min(200, n ^ m)
- 1 <= mat[i][j] <= 5000
- mat[i] 是一个非递减数组

## 题目样例

### 示例 1

#### 输入

mat = [[1,3,11],[2,4,6]], k = 5

#### 输出

7

#### 解释

从每一行中选出一个元素，前 k 个和最小的数组分别是：
[1,2], [1,4], [3,2], [3,4], [1,6]。其中第 5 个的和是 7 。

### 示例 2

#### 输入

mat = [[1,10,10],[1,4,5],[2,3,6]], k = 7

#### 输出

9

#### 解释

从每一行中选出一个元素，前 k 个和最小的数组分别是：
[1,1,2], [1,1,3], [1,4,2], [1,4,3], [1,1,6], [1,5,2], [1,5,3]。其中第 7 个的和是 9 。

### 示例 3

#### 输入

mat = [[1,3,11],[2,4,6]], k = 9

#### 输出

17

## 题目思考

1. 第一个和毫无疑问是每一行的第一个元素相加所得
2. 那第二个和呢, 它的候选项有哪些?
3. 是否可以将矩阵看成一个图来处理, 使用一些图的算法?

## 解决方案

### 思路

- BFS+优先队列/小顶堆存(sm, 下标元组)元组
- BFS 是为了遍历下个可能的候选项, 需要配合 visit 集合使用
- 使用一个下标元组, 初始化每一行的列下标都为 0
- 然后每次循环时遍历所有的行, 检查该行的列号+1 后的下标元组是否已经在 visit 集合中
  - 没在的话将其新的和(可以根据当前和+新加入的元素-移除的元素得到), 以及下标元组加入优先队列中
  - 在的话就说明它已经被加入优先队列了, 忽略它避免重复计算
- 循环 k-1 次后, 此时堆顶的 sm 值即为所求

### 复杂度

- 时间复杂度 O(kmlog(km)): 总共 k 次遍历, 然后每次遍历需要遍历所有行(m), 堆操作的复杂度为 log(km)
- 空间复杂度 O(km^2): 堆中每个元素大小为 m, 然后堆元素的个数为 km

### 代码

#### Python 3

```python
class Solution:
    def kthSmallest(self, mat: List[List[int]], k: int) -> int:
        rows, cols = len(mat), len(mat[0])
        sm = sum(mat[r][0] for r in range(rows))
        startIndices = (0, ) * rows
        q = [(sm, startIndices)]
        v = {startIndices}
        for i in range(2, k + 1):
            cursm, indices = heapq.heappop(q)
            for r in range(rows):
                if indices[r] + 1 < len(mat[r]):
                    newCol = indices[r] + 1
                    newIndices = indices[:r] + (newCol, ) + indices[r + 1:]
                    if newIndices not in v:
                        newsm = cursm + mat[r][newCol] - mat[r][indices[r]]
                        v.add(newIndices)
                        heapq.heappush(q, (newsm, newIndices))
        return q[0][0]
```

#### C++

```cpp
class Solution {
public:
    int kthSmallest(const vector<vector<int>>& mat, int k) {
        int m = mat.size();
        int n = mat[0].size();

        multimap<int, vector<int>> q;
        set<vector<int>> visited;

        int sum = 0;
        for (int i = 0; i < m; ++i) {
            sum += mat[i][0];
        }

        q.emplace(sum, vector<int>(m, 0));
        visited.insert(vector<int>(m, 0));

        for (int i = 2; i <= k; ++i) {
            auto sum = q.begin()->first;
            auto indices = q.begin()->second;
            q.erase(q.begin());

            for (int r = 0; r < m; ++r) {
                if (++indices[r] < n) {
                    if (visited.find(indices) == visited.end()) {
                        int newSum = sum + mat[r][indices[r]] - mat[r][indices[r]-1];
                        q.emplace(newSum, indices);
                        visited.insert(indices);
                    }
                }
                --indices[r];
            }
        }
        return q.begin()->first;
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
