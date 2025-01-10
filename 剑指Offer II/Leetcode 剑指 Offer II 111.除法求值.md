> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/vlzXQL/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个变量对数组 equations 和一个实数值数组 values 作为已知条件，其中 equations[i] = [Ai, Bi] 和 values[i] 共同表示等式 Ai / Bi = values[i] 。每个 Ai 或 Bi 是一个表示单个变量的字符串。

另有一些以数组 queries 表示的问题，其中 queries[j] = [Cj, Dj] 表示第 j 个问题，请你根据已知条件找出 Cj / Dj = ? 的结果作为答案。

返回 所有问题的答案 。如果存在某个无法确定的答案，则用 -1.0 替代这个答案。如果问题中出现了给定的已知条件中没有出现的字符串，也需要用 -1.0 替代这个答案。

注意：输入总是有效的。可以假设除法运算中不会出现除数为 0 的情况，且不存在任何矛盾的结果。

### 示例 1：

- 输入：equations = [["a","b"],["b","c"]], values = [2.0,3.0], queries = [["a","c"],["b","a"],["a","e"],["a","a"],["x","x"]]
- 输出：[6.00000,0.50000,-1.00000,1.00000,-1.00000]
- 解释：
  - 条件：a / b = 2.0, b / c = 3.0
  - 问题：a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ?
  - 结果：[6.0, 0.5, -1.0, 1.0, -1.0 ]

### 示例 2：

- 输入：equations = [["a","b"],["b","c"],["bc","cd"]], values = [1.5,2.5,5.0], queries = [["a","c"],["c","b"],["bc","cd"],["cd","bc"]]
- 输出：[3.75000,0.40000,5.00000,0.20000]

### 示例 3：

- 输入：equations = [["a","b"]], values = [0.5], queries = [["a","b"],["b","a"],["a","c"],["x","y"]]
- 输出：[0.50000,2.00000,-1.00000,-1.00000]

### 提示：

- 1 <= equations.length <= 20
- equations[i].length == 2
- 1 <= Ai.length, Bi.length <= 5
- values.length == equations.length
- 0.0 < values[i] <= 20.0
- 1 <= queries.length <= 20
- queries[i].length == 2
- 1 <= Cj.length, Dj.length <= 5
- Ai, Bi, Cj, Dj 由小写英文字母与数字组成

## 题目思考

1. 可以使用什么数据结构或算法来解决?

## 解决方案

- 观察题目, 我们如果能够利用已知条件, 将有关系的变量都转换成同一个变量的若干倍, 这样在计算的时候直接将倍数相除即可
- 这个就是经典的[并查集](https://zh.wikipedia.org/wiki/%E5%B9%B6%E6%9F%A5%E9%9B%86)的思路, 就是将元素进行分类, 相关的放在同一个集合中, 从而实现快速合并和查询, 具体步骤如下:
  1. 首先我们需要定义一个字典 pre, `pre[x]`表示 x 的祖先, 如果两个元素具有相同祖先, 就表示它们在同一个集合中. 可以把祖先 `pre[x]` 想象成一个树的根节点, 那么 x 就是树中的一个节点(可能是根节点本身)
  2. 然后定义一个 find 方法, 查找当前元素的祖先, 如果祖先不存在的话就把自身当做祖先. 这里用到了路径压缩的优化, 就是说当发现自己的祖先不是自身的时候, 就尝试把自己的祖先设置为自己的当前祖先的祖先, 从而降低树的高度, 加快之后的查找过程.
  3. 最后定义一个 union 方法, 用于合并两个元素. 这里的思路也很简单, 就是找到各自的祖先, 然后将其中一个的祖先的祖先设置为另外一个祖先即可, 等于就把两个树合并在了一起.
- 不过对于这道题而言, 只保存当前变量的祖先变量还是不够的, 因为变量之间存在倍数关系, 只保存变量名就会把这个信息丢掉
- 所以我们需要把 pre 字典进行改造, key 还是存变量名, 但是 value 就要转变成`(祖先变量名, 当前变量相比祖先变量的倍数)`了, 这样才能正确保存倍数关系, 用于最终的方程式的求值
- 加了倍数之后, 在 find 和 union 的时候, 都要计算当前变量相对新的祖先的新倍数
  - find
    - 先拿到当前祖先 pre[x] 的祖先 px
    - 然后将当前变量的祖先设为新的祖先, 这样倍数自然要为两个倍数的乘积了
    - 举个例子(这里只关心倍数): 例如 `x=5pre[x]`, `pre[x] = 5px`, 那么 `x=(5*5)px=25px`
  - union
    - 拿到当前 x 和 y 的祖先 px 和 py
    - 将 px 的祖先设为 py, 倍数也要随之变化
    - 举个例子(这里只关心倍数): 假设当前倍数(即 x/y) 是 5, 即 `x=5y`, 然后 `x=4px`, `y=3py`, 那么要求 px/py 的话, 代入即可得 `px = 5*3/4 py`, 5 是当前倍数 multiple, 3 是 py[1], 4 是 px[1], 这就得到了新的倍数
- 在具体实现时, 我们需要先遍历给出的 equations, 对两个变量及其系数进行合并(union), 然后再遍历 queries, 查找(find)两个变量各自的祖先和系数, 祖先相同时就可以得到两个变量之间的倍数了, 祖先不同则返回-1
- 另外特别注意如果 queries 中任一变量不在 equations 时, 也即不在 pre 字典时, 都直接返回-1, 对应于题目的要求: 如果问题中出现了给定的已知条件中没有出现的字符串，需要用 -1.0 替代这个答案
- 下面的代码对必要步骤有详细的解释, 方便大家理解
- 另外我之前还写过一个并查集的系列, 感兴趣的同学在我的公众号聊天框中回复 **并查集** 就能看到了

#### 复杂度

- 时间复杂度 `O(NlogM)`: 假设 N 为总的方程式个数, M 为变量数目, 那么总共需要遍历 N 个方程, 对变量进行合并和查询操作需要 logM 的时间, 所以总时间复杂度就是 `O(NlogM)`
- 空间复杂度 `O(M)`: pre 字典中存 M 个变量

#### 代码

```python
class Solution:
    def calcEquation(self, equations: List[List[str]], values: List[float], queries: List[List[str]]) -> List[float]:
        # 改造版并查集, 加入一个倍数, pre[x] = (祖先, x相对于祖先的倍数)
        # 注意在query的时候要判断分子分母的祖先是否是一个, 如果不是, 则直接返回-1.0
        pre = {}

        def find(x):
            # 查找x的祖先, 顺便进行路径压缩
            if x not in pre:
                # 当前变量还不在pre字典中, 就初始化其祖先为变量本身, 倍数自然是1
                pre[x] = (x, 1)
            elif x != pre[x][0]:
                # 当前变量和祖先变量名不一样, 尝试路径压缩
                # 先拿到祖先pre[x]的祖先px
                px = find(pre[x][0])
                # 然后将当前变量的祖先设为新的祖先, 这样倍数自然要为两个倍数的乘积了
                # 举个例子(这里只关心倍数): 例如x=5pre[x], pre[x] = 5px, 那么x=(5*5)px=25px
                pre[x] = (px[0], px[1] * pre[x][1])
            return pre[x]

        def union(x, y, multiple):
            # 将一个的祖先设为另一个的祖先, 注意倍数的变化
            px = find(x)
            py = find(y)
            # 举个例子(这里只关心倍数): 假设当前multiple是5, 即x=5y, 然后x=4px, y=3py, 那么要求px/py的话, 代入即可得px = 5*3/4 py, 5是multiple, 3是py[1], 4是px[1], 这就是下面的倍数由来
            pre[px[0]] = (py[0], multiple * py[1] / px[1])

        for (x, y), multiple in zip(equations, values):
            # 合并已知的方程式中的变量以及对应的倍数
            union(x, y, multiple)
        res = []
        for x, y in queries:
            if x not in pre or y not in pre:
                # 某个变量不存在在上面已知的方程式中, 直接返回-1.0
                res.append(-1.0)
            else:
                px, py = find(x), find(y)
                if px[0] != py[0]:
                    # 如果两个变量没有共同祖先, 即没有已知方程式将它们关联起来, 那么它们相除的结果也就无法确定了, 返回-1.0
                    res.append(-1.0)
                else:
                    # 两个变量可以有共同祖先表示, 那么其倍数的商即为当前除法表达式的值
                    res.append(px[1] / py[1])
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
