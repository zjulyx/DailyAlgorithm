> [竞赛链接](https://leetcode-cn.com/contest/weekly-contest-188/)

> 新鲜出炉今天的周赛, 这场发挥了平均水平, 最终排名 55 (木有进前 50😭)

> 总结来说这场周赛题目质量不错, 前面两题没有特别简单, 后面两题也没有特别难, 相当推荐

# [1441] 用栈操作构建数组

> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-188/problems/build-an-array-with-stack-operations/)

## 题目描述

给你一个目标数组 target 和一个整数 n。每次迭代，需要从 list = {1,2,3..., n} 中依序读取一个数字。

请使用下述操作来构建目标数组 target ：

- Push：从 list 中读取一个新元素， 并将其推入数组中。
- Pop：删除数组中的最后一个元素。
- 如果目标数组构建完成，就停止读取更多元素。

题目数据保证目标数组严格递增，并且只包含 1 到 n 之间的数字。

请返回构建目标数组所用的操作序列。

题目数据保证答案是唯一的。

## 题目样例

### 示例 1

#### 输入

target = [1,3], n = 3

#### 输出

["Push","Push","Pop","Push"]

#### 解释

- 读取 1 并自动推入数组 -> [1]
- 读取 2 并自动推入数组，然后删除它 -> [1]
- 读取 3 并自动推入数组 -> [1,3]

### 示例 2

#### 输入

target = [1,2], n = 4

#### 输出

["Push","Push"]

#### 解释

只需要读取前 2 个数字就可以停止。

### 示例 3

#### 输入

target = [1,2,3], n = 3

#### 输出

["Push","Push","Push"]

## 题目思考

1. 这道题反其道行之, 给了目标数组, 让推导出操作序列
2. 看上去只有两种情况: 在目标数组, 和不在目标数组
3. 目标数组是递增的, 是否可以利用这一点?

## 解决方案

### 思路

- 存当前应该 push 的下标 cur
- 遍历目标数组
  - 如果当前值 t 恰好等于 cur, 那么直接加个 push 操作, 同时更新下标为 t+1
  - 否则对于[cur,t)之间的元素, 都应该一个 push 一个 pop 让它们进下 stack 就走..之后也不要忘了 push t, 以及更新下标

### 复杂度

- 时间复杂度 O(N): 需要遍历 target 数组
- 空间复杂度 O(1): 只使用了几个变量

### 代码

#### Python 3

```python
class Solution:
    def buildArray(self, target: List[int], n: int) -> List[str]:
        res = []
        cur = 1
        for t in target:
            if t != cur:
                # t一定大于cur
                res.extend(['Push', 'Pop'] * (t - cur))
            res.append('Push')
            cur = t + 1
        return res
```

#### C++

```cpp
class Solution {
public:
    vector<string> buildArray(vector<int>& target, int n) {
        vector<string> res;

        int curr = 1;
        for (auto t : target) {
            if (t != curr) {
                for (int i = 0; i < t - curr; ++i) {
                    res.push_back("Push");
                    res.push_back("Pop");
                }
            }
            res.push_back("Push");
            curr = t + 1;
        }

        return res;
    }
};
```

---

# [1442] 形成两个异或相等数组的三元组数目

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-188/problems/count-triplets-that-can-form-two-arrays-of-equal-xor/)

## 题目描述

给你一个整数数组 arr 。

现需要从数组中取三个下标 i、j 和 k ，其中 (0 <= i < j <= k < arr.length) 。

a 和 b 定义如下：

- a = arr[i] ^ arr[i + 1] ^ ... ^ arr[j - 1]
- b = arr[j] ^ arr[j + 1] ^ ... ^ arr[k]

注意：^ 表示 按位异或 操作。

请返回能够令 a == b 成立的三元组 (i, j , k) 的数目。

## 题目样例

### 示例 1

#### 输入

arr = [2,3,1,6,7]

#### 输出

4

#### 解释

满足题意的三元组分别是 (0,1,2), (0,2,2), (2,3,4) 以及 (2,4,4)

### 示例 2

#### 输入

arr = [2,3]

#### 输出

0

#### 解释

没有满足条件的 a 和 b

### 示例 3

#### 输入

arr = [1,1,1,1,1]

#### 输出

10

## 题目思考

1. 题目很直白, 直接暴力三重循环可以吗?
2. 是否可以通过预处理进行优化?

## 解决方案

### 思路

- 直接三重循环的做法很直白, 但其复杂度过高 (python 3 需要 5000ms...), 这里就忽略了
- 这里提供一种预处理的方法 (python 3 使用这种方法只需要 196ms):
  1. 先拿到所有区间的异或结果
  2. 将结果存入字典中作为 key
  3. 字典的 value 也是一个字典, key 是区间起始下标, value 是区间结束下标集合
- 这样可以只需遍历每个起始下标 s 对应的终点下标 e 集合, 如果 e+1 也在当前字典中, 那么 res 就加上 e+1 对应的终点下标集合长度

### 复杂度

- 时间复杂度 O(N^2): 预处理需要 O(N^2), 求结果也是 O(N^2)
- 空间复杂度 O(N^2): 字典中存了 O(N^2)的下标元素

### 代码

#### Python 3

```python
class Solution:
    def countTriplets(self, arr: List[int]) -> int:
        n = len(arr)
        d = collections.defaultdict(dict)
        # 预处理字典 {a:{s:[e1,e2,...]}}
        for i in range(n):
            a = 0
            for j in range(i, n):
                a^=arr[j]
                if i not in d[a]:
                    d[a][i] = []
                d[a][i].append(j)
        res = 0
        # 这里虽然表面是三重循环
        # 但实际k*s才相当于原先的数组元素数目N
        # 所以复杂度还是O(N^2)
        for k in d:
            for s in d[k]:
                for e in d[k][s]:
                    if e+1 in d[k]:
                        res+=len(d[k][e+1])

        return res
```

#### C++

```cpp
class Solution {
public:
    int countTriplets(vector<int>& arr) {
        unordered_map<int, unordered_map<int, unordered_set<int>>> d;

        for (int i = 0; i < arr.size(); ++i) {
            int a = 0;
            for (int j = i; j < arr.size(); ++j) {
                a ^= arr[j];
                d[a][i].insert(j);
            }
        }

        int res = 0;

        for (auto& k : d) {
            for (auto& s : k.second) {
                for (auto e : s.second) {
                    if (k.second.find(e+1) != k.second.end()) {
                        res += d[k.first][e+1].size();
                    }
                }
            }
        }

        return res;
    }
};
```

---

# [1443] 收集树上所有苹果的最少时间

> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-188/problems/minimum-time-to-collect-all-apples-in-a-tree/)

## 题目描述

给你一棵有 n 个节点的无向树，节点编号为 0 到 n-1 ，它们中有一些节点有苹果。通过树上的一条边，需要花费 1 秒钟。你从 节点 0 出发，请你返回最少需要多少秒，可以收集到所有苹果，并回到节点 0 。

无向树的边由 edges 给出，其中 edges[i] = [fromi, toi] ，表示有一条边连接 from 和 toi 。除此以外，还有一个布尔数组 hasApple ，其中 hasApple[i] = true 代表节点 i 有一个苹果，否则，节点 i 没有苹果。

## 题目样例

### 示例 1

#### 输入

n = 7, edges = [[0,1],[0,2],[1,4],[1,5],[2,3],[2,6]], hasApple = [false,false,true,false,true,true,false]

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/10/min_time_collect_apple_1.png)

#### 输出

8

#### 解释

上图展示了给定的树，其中红色节点表示有苹果。一个能收集到所有苹果的最优方案由绿色箭头表示。

### 示例 2

#### 输入

n = 7, edges = [[0,1],[0,2],[1,4],[1,5],[2,3],[2,6]], hasApple = [false,false,true,false,false,true,false]

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/10/min_time_collect_apple_2.png)

#### 输出

6

#### 解释

上图展示了给定的树，其中红色节点表示有苹果。一个能收集到所有苹果的最优方案由绿色箭头表示。

### 示例 3

#### 输入

n = 7, edges = [[0,1],[0,2],[1,4],[1,5],[2,3],[2,6]], hasApple = [false,false,false,false,false,false,false]

#### 输出

0

## 题目思考

1. 这道题要用什么思路呢? 贪心? DFS? BFS?
2. 仔细观察示例图和结果, 是否有什么发现?

## 解决方案

### 思路

- 要收集到所有苹果, 充要条件是每个有苹果的节点都要走一遍
- 对于那些本身没有苹果, 且子树没有苹果的节点, 完全不需要走到它们
- 所以如果能够统计出所有自身或者子树有苹果的节点, 然后把这些节点都走一遍, 就是最优方案
  - 假设节点数为 n, 由于题目是个树, 所以这些节点构成的路径数目是 n-1, 最优情况就是每个路径走 2 次, 一来一回, 结果就是 2\*(n-1)
- 如何求自身或者子树有苹果的节点呢? 可以想到使用递归, 结果返回当前节点及子树是否有苹果, 然后逐层上去即可

### 复杂度

- 时间复杂度 O(N): 初始化路径和递归各需要遍历每个节点一次
- 空间复杂度 O(N): 使用了一个字典, 字典元素总数为节点数目, 还有递归的栈的消耗

### 代码

#### Python 3

```python
class Solution:
    def minTime(self, n: int, edges: List[List[int]],
                hasApple: List[bool]) -> int:
        # 初始化路径
        maps = collections.defaultdict(list)
        for e in edges:
            maps[e[0]].append(e[1])

        def dfs(i):
            selfOrChildHasApple = hasApple[i]
            for nex in maps[i]:
                selfOrChildHasApple |= dfs(nex)
            if not selfOrChildHasApple:
                # 从字典中移除自身或子树都没有苹果的节点
                del maps[i]
            return selfOrChildHasApple

        dfs(0)
        # 字典个数即为最终有效节点的个数
        # 但是有可能有效节点为0, 所以需要max一下
        return max(0, 2 * (len(maps) - 1))
```

#### C++

```cpp
class Solution {
public:
    int minTime(int n, vector<vector<int>>& edges, vector<bool>& hasApple) {
        for (auto& entry : edges) {
            maps[entry[0]].push_back(entry[1]);
        }

        hasAppleHelper(0, hasApple);

        return maps.empty() ? 0 : 2 * (maps.size() - 1);
    }

private:
    bool hasAppleHelper(int idx, vector<bool>& hasApple) {
        bool selfOrChildHasApple = hasApple[idx];
        for (auto i : maps[idx]) {
            selfOrChildHasApple = hasAppleHelper(i, hasApple) || selfOrChildHasApple;
        }
        if (!selfOrChildHasApple) {
            maps.erase(idx);
        }
        return selfOrChildHasApple;
    }

private:
    unordered_map<int, vector<int>> maps;
};
```

---

# [1444] 切披萨的方案数

> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/contest/weekly-contest-188/problems/number-of-ways-of-cutting-a-pizza/)

## 题目描述

给你一个 rows x cols 大小的矩形披萨和一个整数 k ，矩形包含两种字符： 'A' （表示苹果）和 '.' （表示空白格子）。你需要切披萨 k-1 次，得到 k 块披萨并送给别人。

切披萨的每一刀，先要选择是向垂直还是水平方向切，再在矩形的边界上选一个切的位置，将披萨一分为二。如果垂直地切披萨，那么需要把左边的部分送给一个人，如果水平地切，那么需要把上面的部分送给一个人。在切完最后一刀后，需要把剩下来的一块送给最后一个人。

请你返回确保每一块披萨包含 至少 一个苹果的切披萨方案数。由于答案可能是个很大的数字，请你返回它对 10^9 + 7 取余的结果。

- 1 <= rows, cols <= 50
- rows == pizza.length
- cols == pizza[i].length
- 1 <= k <= 10
- pizza 只包含字符 'A' 和 '.' 。

## 题目样例

### 示例 1

#### 输入

pizza = ["A..","AAA","..."], k = 3

![示例图](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/05/10/ways_to_cut_apple_1.png)

#### 输出

3

#### 解释

上图展示了三种切披萨的方案。注意每一块披萨都至少包含一个苹果。

### 示例 2

#### 输入

pizza = ["A..","AA.","..."], k = 3

#### 输出

1

#### 解释

只能横切一刀, 竖切一刀, 再横切一刀

### 示例 3

#### 输入

pizza = ["A..","A..","..."], k = 1

#### 输出

1

## 题目思考

1. 注意到这道题的数据规模很小, 是不是可以利用多个状态记忆化搜索或动态规划?
2. 状态的选择: 每次分出去的都是上面或者左边的, 是否可以利用这一点?
3. 可否通过一些预处理来加速运算呢?

## 解决方案

### 思路

- 记忆化搜索/动态规划
- memo[r,c,p]表示矩形[(r,c), (rows-1, cols-1)]分给 p 个人的方案数
- 那么 memo[r,c,p] = sum(memo[nexr, c, p-1]) + sum(memo[r, nexc, p-1])
  - r+1<=nexr<rows
  - c+1<=nexc<cols
  - **且[r, nexr)以及[c, nexc]之间的部分必须要有苹果分给当前的人, 否则当前的人就拿不到苹果了**
- 至于怎么求[r, nexr)以及[c, nexc]之间的苹果数, 如果每次递归的时候都重新计算, 那太慢了大概率会超时吧..这部分完全可以通过事先预处理求得
- 所以额外定义几个字典, rightcnt/downcnt/cnt 分别表示当前坐标右边一行, 下边一列, 以及右下矩形的苹果数目, 右下矩形的苹果数目可以用于剪枝, 当数目<所需人数时直接返回 0 即可

### 复杂度

- 时间复杂度 O(rows\*cols\*k\*(rows+cols))): 需要搜索 rows\*cols\*k 个状态, 而且搜索时要对接下来的 r 或者 c 求和, 根据本题数据量, 就是 50\*50\*10\*100, 还算可以接受(如果有更优解欢迎指出 🤩)
- 空间复杂度 O(rows\*cols\*k): memo 的元素个数

### 代码

#### Python 3

```python
class Solution:
    def ways(self, pizza: List[str], k: int) -> int:
        mod = 10**9 + 7
        memo = {}
        rightdowncnt = collections.defaultdict(int)
        downcnt = collections.defaultdict(int)
        rightcnt = collections.defaultdict(int)
        rows, cols = len(pizza), len(pizza[0])
        # 预处理, 求三种cnt字典
        for c in range(cols):
            for r in range(rows)[::-1]:
                downcnt[r, c] = 1 if pizza[r][c] == 'A' else 0
                downcnt[r, c] += downcnt[r + 1, c]
        for r in range(rows):
            for c in range(cols)[::-1]:
                rightcnt[r, c] = 1 if pizza[r][c] == 'A' else 0
                rightcnt[r, c] += rightcnt[r, c + 1]
        for r in range(rows)[::-1]:
            for c in range(cols)[::-1]:
                rightdowncnt[r, c] = 1 if pizza[r][c] == 'A' else 0
                rightdowncnt[r,
                             c] += rightdowncnt[r + 1, c] + rightcnt[r, c + 1]

        def dfs(r, c, p):
            # 递归出口
            if r == rows or c == cols:
                return 0
            if (r, c, p) not in memo:
                if rightdowncnt[r, c] < p:
                    # 剪枝
                    memo[r, c, p] = 0
                elif p == 1:
                    # 只有1人时方案数只能为1
                    memo[r, c, p] = 1
                else:
                    sm = 0
                    cnt = 0
                    # 状态转移, 求接下来所有可能的方案数之和
                    # 注意取模
                    for nexr in range(r+1, rows):
                        cnt += rightcnt[nexr-1, c]
                        if cnt > 0:
                            sm = (sm + dfs(nexr, c, p - 1)) % mod
                    cnt = 0
                    for nexc in range(c+1, cols):
                        cnt += downcnt[r, nexc-1]
                        if cnt > 0:
                            sm = (sm + dfs(r, nexc, p - 1)) % mod
                    memo[r, c, p] = sm
            return memo[r, c, p]

        res = dfs(0, 0, k)
        return res
```

#### C++

```cpp
class Solution {
public:
    int ways(vector<string>& pizza, int k) {
        int rows = pizza.size();
        int cols = pizza[0].size();
        vector<vector<int>> downcnt = vector<vector<int>>(rows+1, vector<int>(cols+1, 0));
        vector<vector<int>> rightcnt = vector<vector<int>>(rows+1, vector<int>(cols+1, 0));
        vector<vector<int>> rightdowncnt = vector<vector<int>>(rows+1, vector<int>(cols+1, 0));
        vector<vector<vector<int>>> memo = vector<vector<vector<int>>>(rows+1, vector<vector<int>>(cols+1, vector<int>(k+1, -1)));

        for (int c = 0; c < cols; ++c) {
            for (int r = rows - 1; r >= 0; --r) {
                downcnt[r][c] = downcnt[r+1][c] + (pizza[r][c] == 'A' ? 1 : 0);
            }
        }

        for (int r = 0; r < rows; ++r) {
            for (int c = cols - 1; c >= 0; --c) {
                rightcnt[r][c] = rightcnt[r][c+1] + (pizza[r][c] == 'A' ? 1 : 0);
            }
        }

        for (int r = rows - 1; r >= 0; --r) {
            for (int c = cols - 1; c >= 0; --c) {
                rightdowncnt[r][c] = rightdowncnt[r+1][c] + rightcnt[r][c];
            }
        }

        long long mod = 1e9+7;
        function<int(int, int, int)> dfs = [&](int r, int c, int p) {
            if (r == rows || c == cols) {
                return 0;
            }

            if (memo[r][c][p] == -1) {
                if (rightdowncnt[r][c] < p) {
                    memo[r][c][p] = 0;
                } else if (p == 1) {
                    memo[r][c][p] = 1;
                } else {
                    int sm = 0;
                    int cnt = 0;
                    for (int i = r+1; i < rows; ++i) {
                        cnt += rightcnt[i-1][c];
                        if (cnt > 0) {
                            sm = (sm + dfs(i, c, p-1)) % mod;
                        }
                    }
                    cnt = 0;
                    for (int i = c+1; i < cols; ++i) {
                        cnt += downcnt[r][i-1];
                        if (cnt > 0) {
                            sm = (sm + dfs(r, i, p-1)) % mod;
                        }
                    }
                    memo[r][c][p] = sm;
                }
            }
            return memo[r][c][p];
        };

        return dfs(0, 0, k);
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
