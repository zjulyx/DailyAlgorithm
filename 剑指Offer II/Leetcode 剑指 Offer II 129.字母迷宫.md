> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/ju-zhen-zhong-de-lu-jing-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

字母迷宫游戏初始界面记作 m x n 二维字符串数组 grid，请判断玩家是否能在 grid 中找到目标单词 target。
注意：寻找单词时 必须 按照字母顺序，通过水平或垂直方向相邻的单元格内的字母构成，同时，同一个单元格内的字母 不允许被重复使用 。

![](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

### 示例 1：

- 输入：grid = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], target = "ABCCED"
- 输出：true

### 示例 2：

- 输入：grid = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], target = "SEE"
- 输出：true

### 示例 3：

- 输入：grid = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], target = "ABCB"
- 输出：false

### 提示：

- m == grid.length
- n = grid[i].length
- 1 <= m, n <= 6
- 1 <= target.length <= 15
- grid 和 target 仅由大小写英文字母组成

## 题目思考

1. 有没有什么直观思路?
2. 如何判断某个字符是否已经被访问?

## 解决方案

### 思路

#### 分析

- 分析题意, 这道题需要求目标字符串的路径, 且只要找到一个满足条件的即可, 所以特别适合用 DFS 来做. 因为 DFS 比较方便记录每条遍历过的路径, 正好可以用在这里
- 所以我们可以从任意一个字符开始, 依次比较它和目标字符串当前下标对应的字符, 如果相等的话, 就可以继续向四周遍历, 同时字符串下标加 1
- 直到字符串下标达到字符串长度, 这个时候就说明我们找到了一个满足要求的路径, 直接返回 true 即可
- 而如果遍历过程中某个字符并不匹配, 我们就不要继续往下遍历下去了, 因为肯定不满足条件
- 另外注意需要判断某个字符是否已经访问过, 所以我们需要额外定义一个 visit 集合, 用于存储已经访问的元素行列下标: 在递归调用某个字符之前将其加入行列下标加入集合中, 调用结束后还要将其移除, 恢复现场, 避免这个下标之后在其他路径中不能被用到

#### 实现

- 经典的 DFS, 需要额外 visit 集合, 以及当前目标字符串的下标
- 依次遍历矩阵的每个字符, 如果当前字符与目标字符串的开头相同, 就以它为起点进行 DFS, 如果发现一条满足条件的路径就直接返回 true, 否则继续遍历
- **优化**
  - visit 集合可以利用对原始矩阵的字符替换为空字符或其他不存在的字符代替, 而恢复时再将其替换回来即可, 这样可以节省一些空间
  - 如果某个方向已经找到路径了, 就没必要继续遍历下去, 直接返回即可, 这样剪枝可以节省一些时间
- 下面代码对各个步骤都有详细解释, 特别是时间和空间优化的部分, 方便大家理解

### 复杂度

- 时间复杂度 `O((3^L)*RC)`
  - 假设 L 是目标字符串长度, RC 是矩阵行和列数
  - 每次递归调用都需要考虑 3 个方向(不可能返回上一个字符, 因为它已经被访问了, 不会重复被访问, 所以是 3 个方向), 最多要调用到长度为 L 的路径, 这部分就是 `O(3^L)`
  - 而一共需要遍历 RC 个字符, 这部分是`O(RC)`
  - 所以时间复杂度是`O((3^L)*RC)`
- 空间复杂度 `O(L)`
  - 优化掉 visit 集合后, 只需要考虑递归的栈的空间, 而递归深度最大为 L(当路径长度超过 L 后没必要继续下去), 所以空间复杂度就是`O(L)`

### 代码

```python
class Solution:
    def wordPuzzle(self, grid: List[List[str]], target: str) -> bool:
        ### DFS + 回溯
        # 针对每一个首字符, 找所有可能的字符串, 剪枝
        rows, cols = len(grid), len(grid[0])

        def dfs(r, c, i):
            # i表示当前的目标字符串下标
            if i == len(target):
                # 如果已经遍历完整个目标字符串, 则说明找到一条有效路径, 直接返回True
                return True
            # 4个方向遍历 (注意实际只会有3个方向继续下去, 因为上一个节点已经被访问过, 不会再访问它)
            for rr, cc in ((r + 1, c), (r - 1, c), (r, c + 1), (r, c - 1)):
                if 0 <= rr < rows and 0 <= cc < cols and grid[rr][cc] == target[i]:
                    # 优化1: 将visit集合优化为直接标记原矩阵, 节省空间
                    # 注意上面不需要再判断board[rr][cc]是否是空字符 (是否被访问过), 因为如果是空字符的话肯定不可能等于word[i]
                    grid[rr][cc] = ""  # 标记为访问过
                    if dfs(rr, cc, i + 1):
                        # 优化2: 剪枝, 找到一条路径直接返回True, 节省时间
                        return True
                    grid[rr][cc] = target[i]
            return False

        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == target[0]:
                    # 只有当前字符与目标字符串开头相同时才开始DFS, 其他字符开头就不相同
                    # 注意需要先将起始字符标记成访问过, 避免重复再访问它
                    grid[r][c] = ""
                    if dfs(r, c, 1):
                        return True
                    grid[r][c] = target[0]
        # 没找到有效路径, 返回False
        return False
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
