> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个二维数组 array，请返回「螺旋遍历」该数组的结果。

螺旋遍历：从左上角开始，按照 向右、向下、向左、向上 的顺序 依次 提取元素，然后再进入内部一层重复相同的步骤，直到提取完所有元素。

### 示例 1：

- 输入：array = [[1,2,3],[8,9,4],[7,6,5]]
- 输出：[1,2,3,4,5,6,7,8,9]

### 示例 2：

- 输入：array = [[1,2,3,4],[12,13,14,5],[11,16,15,6],[10,9,8,7]]
- 输出：[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16]

### 限制：

- 0 <= array.length <= 100
- 0 <= array[i].length <= 100

## 题目思考

1. 可以有哪些遍历方式?

## 解决方案

### 方案 1

#### 思路

- 首先一个很自然的想法就是模拟整个顺时针的过程, 逐个点遍历, 当遇到边界或者已经遍历过的点就按照顺时针方向转向即可
- 所以需要额外一个 visit 集合存储当前访问过的点
- **如何转向以及下一个点如何得到则可以通过预先定义对应关系字典得到**, 也就是所谓的表驱动法. 这样下面的逻辑判断中就直接查表即可, 无需用多个 if else 条件来判断各个方向了, 减少代码冗余
- 最后当最终数组数目正好等于矩阵元素个数时跳出循环即可
- 下面代码中针对关键部分有详细的注释

#### 复杂度

- 时间复杂度 `O(RC)`
  - 所有点只需要遍历一遍
- 空间复杂度 `O(RC)`
  - 需要一个额外的集合存储所有点

#### 代码

```python
class Solution:
    def spiralArray(self, array: List[List[int]]) -> List[int]:
        # 方法1: 模拟整个过程, 逐个点遍历
        if not array:
            return []
        # 存储转向关系
        directions = {
            # 向上, 那么顺时针转向后应该向右
            'u': 'r',
            'd': 'l',
            'l': 'u',
            'r': 'd',
        }
        # 存储当前方向的下个点与当前点的关系
        delta = {
            # 向上, 那么下个点的行号要-1, 列号不变, 下面几个类似
            'u': (-1, 0),
            'd': (1, 0),
            'l': (0, -1),
            'r': (0, 1),
        }
        # 初始向右
        curDirection = 'r'
        rows, cols = len(array), len(array[0])
        n = rows * cols
        r, c = 0, 0
        v = set()
        res = []
        while len(res) < n:
            # 将当前点加入结果中
            res.append(array[r][c])
            v.add((r, c))
            # 尝试按照当前方向获取下个点
            rr, cc = r + delta[curDirection][0], c + delta[curDirection][1]
            if (rr, cc) not in v and 0 <= rr < rows and 0 <= cc < cols:
                # 下个点有效且未被遍历, 直接用它
                r, c = rr, cc
            else:
                # 否则需要转向, 下个点也要按照新的方向获得
                curDirection = directions[curDirection]
                r += delta[curDirection][0]
                c += delta[curDirection][1]
        return res
```

### 方案 2

#### 思路

- 回顾方案 1, 它需要逐个遍历每个点, 且需要额外空间来存储访问过的点, 有没有方法可以一次性得到当前方向上的所有的有效点, 批量加入结果中呢?
- 答案也是肯定的, 我们重新思考题目, 这次尝试从整体上看遍历过程
- 考虑`右-下-左-上`这一轮遍历, 结束后应该就把所有最外层的元素都打印出了, 接下来只需要从第二层起点出发, 再按照这一顺序遍历即可, 是不是很类似**剥洋葱**?
- 这也就是方案 2 的思路, 将每一轮 4 个方向遍历看作一个整体, 每次都找遍历起点, 一口气把该轮的都加入结果中即可
- 遍历起点`(sr,sc)`很显然就是(0,0)->(1,1)->(2,2)->..., 需要注意的是最后一轮的遍历的起点(i, i)要满足`2*i+1<=min(rows,cols)`, 这是因为每轮遍历都会用两行和两列, 而最后一轮可能只有单独一行或一列(对应行或列数为奇数的情况), 总共遍历的行数/列数都要不大于总行数/总列数
- 遍历终点`(er,ec)`就是未被遍历过的最下边的行和最右边的列, 它可以根据遍历起点`(sr,sc)`求出, 根据上一步的分析, `er = rows - sr - 1`, `ec = cols - sc - 1`
- 另外需要注意的是, 最后一轮可能只有一行或者只有一列, 这种情况下就不能沿着返回方向遍历了, 否则就会重复
- 下面代码中针对关键部分有详细的注释, 这里利用了 python 的生成器表达式以及 extend, 也可以改用传统的 for 循环

#### 复杂度

- 时间复杂度 `O(RC)`
  - 所有点只需要遍历一遍
- 空间复杂度 `O(1)`
  - 只需要常数个变量, 因为生成器表达式不使用额外空间

#### 代码

```python
class Solution:
    def spiralArray(self, array: List[List[int]]) -> List[int]:
        # 方法2: 按层遍历, 剥洋葱
        res = []
        if not array:
            return res
        rows, cols = len(array), len(array[0])
        i = 0
        while 2 * i + 1 <= min(rows, cols):
            sr = sc = i
            er = rows - sr - 1
            ec = cols - sc - 1
            # 向右
            res.extend(array[sr][sc:ec + 1])
            # 向下, 注意此时右边端点(sr, ec)已经用过了, 所以从(sr+1, ec)开始
            res.extend(array[r][ec] for r in range(sr + 1, er + 1))
            if sr != er:
                # 向左, 只有终点行和起点行不同时才进行, 否则就重复了
                res.extend(array[er][sc:ec][::-1])
            if sc != ec:
                # 向上, 只有终点列和起点列不同时才进行, 否则就重复了
                res.extend(array[r][sc] for r in range(er - 1, sr, -1))
            i += 1
        return res
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
