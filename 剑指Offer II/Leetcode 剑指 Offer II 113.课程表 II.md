> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/QA2IGt/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

现在总共有 numCourses 门课需要选，记为 0 到 numCourses-1。

给定一个数组 prerequisites ，它的每一个元素 prerequisites[i] 表示两门课程之间的先修顺序。 例如 prerequisites[i] = [ai, bi] 表示想要学习课程 ai ，需要先完成课程 bi 。

请根据给出的总课程数 numCourses 和表示先修顺序的 prerequisites 得出一个可行的修课序列。

可能会有多个正确的顺序，只要任意返回一种就可以了。如果不可能完成所有课程，返回一个空数组。

### 示例 1：

- 输入: numCourses = 2, prerequisites = [[1,0]]
- 输出: [0,1]
- 解释: 总共有 2 门课程。要学习课程 1，你需要先完成课程 0。因此，正确的课程顺序为 [0,1]。

### 示例 2：

- 输入: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
- 输出: [0,1,2,3] or [0,2,1,3]
- 解释: 总共有 4 门课程。要学习课程 3，你应该先完成课程 1 和课程 2。并且课程 1 和课程 2 都应该排在课程 0 之后。因此，一个正确的课程顺序是 [0,1,2,3]。另一个正确的排序是 [0,2,1,3]。

### 示例 3：

- 输入: numCourses = 1, prerequisites = []
- 输出: [0]
- 解释: 总共 1 门课，直接修第一门课就可。

### 提示：

- 1 <= numCourses <= 2000
- 0 <= prerequisites.length <= numCourses \* (numCourses - 1)
- prerequisites[i].length == 2
- 0 <= ai, bi < numCourses
- ai != bi
- prerequisites 中不存在重复元素

## 题目思考

1. 需要使用什么算法和数据结构?

## 解决方案

- 分析题目, 一个直观的想法就是可以先修那些没有前置条件的课程, 然后可以修只依赖它们的课程, 以此类推, 直到能修的课程都修完为止, 此时如果还有一些课程修不了, 则返回空数组
- 那如何知道什么时候一个课程可以修了呢? 我们可以将课程看做节点, 并将先修顺序看做是有向边, 然后维护每个节点的入度, 如果某个节点入度为 0, 则说明没有前置课程或前置课程都修完了, 可以修了
- 举个例子, 对于 prerequisites[i] = [ai, bi], 想要学习课程 ai ，需要先完成课程 bi, 也就是说 bi 有一条指向 ai 的边. bi 的入度为 0, 可以直接修, 而 ai 的入度为 1, 当 bi 修完时, ai 的入度减 1 变成 0, 也可以修了
- 以上就是典型的拓扑排序的思路, 具体代码实现时, 分为以下四步:
  1. 首先遍历所有先修关系, 构造邻接表和入度表
  2. 然后遍历所有节点, 找出那些入度为 0 的节点, 它们可以无条件直接修, 所以将它们直接加入最终结果中
  3. 接下来遍历当前可以修的节点, 利用邻接表找出这些节点指向的节点, 将它们入度减 1, 如果某个节点入度变成了 0, 则代表它可以修了, 将其加入最终结果中, 并等待后续遍历处理
  4. 最后遍历结束时, 如果最终结果长度等于课程数, 说明所有课程都修完了, 否则说明有些课程无法修, 返回空数组
- 下面的代码对必要步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(M+N)`: 课程数(节点数)是 N, 先修数(边数)是 M, 构建邻接表的复杂度是`O(M)`, 初始化可修课程的复杂度是`O(N)`, 处理后续边和节点是`O(M)`, 所以总时间复杂度就是`O(M+N)`
- 空间复杂度 `O(M+N)`: 邻接表和入度表需要存储所有点和边

#### 代码

```python
class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        ### 拓扑排序+入度表+邻接表
        # 先构造入度表和邻接表
        indegree = [0] * numCourses
        maps = [[] for _ in range(numCourses)]
        for a, b in prerequisites:
            indegree[a] += 1
            maps[b].append(a)

        res = []
        for i in range(numCourses):
            if indegree[i] == 0:
                # 初始加入入度为0的课程, 这些可以直接修
                res.append(i)
        for cur in res:
            for nex in maps[cur]:
                indegree[nex] -= 1
                if indegree[nex] == 0:
                    # nex的入度变成了0, 可以修了, 将其加入结果中
                    res.append(nex)
        # 如果最终结果长度等于课程数, 说明所有课程都修完了, 否则说明有些课程无法修, 返回空数组
        return res if len(res) == numCourses else []
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
