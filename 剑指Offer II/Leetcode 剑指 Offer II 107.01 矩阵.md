> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/2bCMpM/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个由 0 和 1 组成的矩阵 mat ，请输出一个大小相同的矩阵，其中每一个格子是 mat 中对应位置元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。

### 示例 1：

![](https://pic.leetcode-cn.com/1626667201-NCWmuP-image.png)

- 输入：mat = [[0,0,0],[0,1,0],[0,0,0]]
- 输出：[[0,0,0],[0,1,0],[0,0,0]]

### 示例 2：

![](https://pic.leetcode-cn.com/1626667205-xFxIeK-image.png)

- 输入：mat = [[0,0,0],[0,1,0],[1,1,1]]
- 输出：[[0,0,0],[0,1,0],[1,2,1]]

### 提示：

- m == mat.length
- n == mat[i].length
- 1 <= m, n <= 10^4
- 1 <= m \* n <= 10^4
- mat[i][j] is either 0 or 1.
- mat 中至少有一个 0

## 题目思考

1. 如何尽可能优化时间空间复杂度?

## 解决方案

- 分析题目, 最直接可以想到的方式就是暴力法: 找到每个值为 0 的格子, 然后以它们为锚点, 遍历整个矩阵, 计算所有格子到它的距离, 更新各个格子的距离最小值
- 不过这种做法的时间复杂度达到了 `O(RCRC)`, 因为外层循环找 0 需要 `O(RC)` 时间, 内层循环更新距离又需要 `O(RC)` 时间, 如何优化呢?
- 一般来说, 非加权图求最短距离的问题都可以先尝试 BFS, 这道题也不例外
- 我们可以维护一个大小相同的距离矩阵作为最终结果, 并使用一个集合记录已经遍历过的格子
- 然后遍历整个矩阵, 找出所有值为 0 的格子对应的行列下标, 将对应距离矩阵的值更新为 0, 并将其加入队列和集合中
- 对于队列中的每个格子, 找出其相邻的四个格子, 如果对应格子尚未在集合中, 说明它还没被遍历, 将其加入队列和集合中, 并更新对应距离矩阵的值为原格子加 1
- 最终当所有格子都遍历完毕时, 距离矩阵的值即为最终结果
- 这样我们可以保证每个格子都只被遍历常数遍, 将时间复杂度优化到了 `O(RC)`
- 我们还可以继续优化, 初始化距离矩阵的所有值为-1, 代表尚未遍历的情况, 这样就无需使用集合, 只需要通过判断对应格子的距离是否为-1, 就可以知道该格子是否被遍历了, 从而将空间复杂度优化到`O(1)`
- 下面的代码对必要步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(RC)`: RC 分别是矩阵行列长度, 每个格子只需要遍历 2 遍
- 空间复杂度 `O(1)`: 除了必要的返回值矩阵, 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:
        rows, cols = len(mat), len(mat[0])
        # 初始化距离矩阵所有元素为-1, 代表尚未遍历的情况
        dist = [[-1] * cols for _ in range(rows)]
        q = []
        for r in range(rows):
            for c in range(cols):
                if mat[r][c] == 0:
                    # 将所有0加入队列中, 并更新其距离为0
                    q.append((r, c))
                    dist[r][c] = 0
        for r, c in q:
            for rr, cc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
                # 遍历当前格子的邻居格子
                if 0 <= rr < rows and 0 <= cc < cols and dist[rr][cc] == -1:
                    # 邻居格子尚未遍历, 更新它为当前格子距离加1
                    # 因为是BFS, 所以该距离一定是邻居格子到最近的0的距离
                    dist[rr][cc] = dist[r][c] + 1
                    # 将邻居格子加入队列中, 等待后续处理
                    q.append((rr, cc))
        return dist
```

---

> 大家可以在下面这些地方找到我~😊

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
