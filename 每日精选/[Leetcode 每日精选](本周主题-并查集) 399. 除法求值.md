> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/evaluate-division/)

> 今天继续来做并查集的问题, 这道题虽然和之前几道题看上去差别很大, 但其核心思想仍然是并查集的一个变种. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给出方程式  A / B = k, 其中  A 和  B 均为用字符串表示的变量， k 是一个浮点型数字。根据已知方程式求解问题，并返回计算结果。如果结果不存在，则返回  -1.0。

输入为: `vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries`(方程式，方程式结果，问题方程式)，  其中  `equations.size() == values.size()`，即方程式的长度与方程式结果长度相等（程式与结果一一对应），并且结果值均为正数。以上为方程式的描述。  返回`vector<double>`类型。

输入总是有效的。你可以假设除法运算中不会出现除数为 0 的情况，且不存在任何矛盾的结果。

## 题目样例

### 示例

#### 输入

- equations(方程式) = [ ["a", "b"], ["b", "c"] ],
- values(方程式结果) = [2.0, 3.0],
- queries(问题方程式) = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ].
- 即给定  a / b = 2.0, b / c = 3.0
- 问题: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ?

#### 输出

[6.0, 0.5, -1.0, 1.0, -1.0 ]

#### 解释

- 将 a 和 b 都转换成 c 即可求得
- 对应表达式中不存在的变量, 一律返回-1.0

## 题目思考

1. 根据题目描述, 我们如何用到并查集这一思想?
2. 需要对并查集的数据结构进行哪些改变?

## 解决方案

### 思路

- **分析**
  - 观察题目, 我们如果能够利用已知条件, 将有关系的变量都转换成同一个变量的若干倍, 这样在计算的时候直接将倍数相除即可
  - 我们可以同样利用并查集的思路, 将已知的方程式的两个变量合并起来, 这样如果它们有关系的话, 它们一定会有共同祖先
  - 但是如果只保存当前变量的祖先变量还是不够的, 因为变量之间存在倍数关系, 只保存变量名就会把这个信息丢掉. 所以我们需要把 pre 字典进行改造, key 还是存变量名, 但是 value 就要转变成`(祖先变量名, 当前变量相比祖先变量的倍数)`了, 这样才能正确保存倍数关系, 用于最终的方程式的求值
  - 特别注意的是, 加了倍数之后, 在路径压缩和合并的时候, 都要计算当前变量相对新的祖先的新倍数
    - 路径压缩
      - 先拿到当前祖先 pre[x] 的祖先 px
      - 然后将当前变量的祖先设为新的祖先, 这样倍数自然要为两个倍数的乘积了
      - 举个例子(这里只关心倍数): 例如 `x=5pre[x]`, `pre[x] = 5px`, 那么 `x=(5*5)px=25px`
    - 合并
      - 拿到当前 x 和 y 的祖先 px 和 py
      - 将 px 的祖先设为 py, 倍数也要随之变化
      - 举个例子(这里只关心倍数): 假设当前倍数(即 x/y) 是 5, 即 `x=5y`, 然后 `x=4px`, `y=3py`, 那么要求 px/py 的话, 代入即可得 `px = 5*3/4 py`, 5 是当前倍数 multiple, 3 是 py[1], 4 是 px[1], 这就得到了新的倍数
- **实现**
  - 遍历已知方程式, 对变量进行合并
  - 遍历要查询的方程式, 根据两个变量之间的关系得出其相除后的值
  - 下面的代码中对每个步骤都有注释, 方便大家理解

### 复杂度

- 时间复杂度 O(NlogM): 假设 N 为总的方程式个数, M 为变量数目, 那么总共需要遍历 N 个方程, 对变量进行合并和查询操作需要 logM 的时间, 所以总时间复杂度就是 O(NlogM)
- 空间复杂度 O(M): pre 字典中存 M 个变量

### 代码

```python
class Solution:
    def calcEquation(self, equations: List[List[str]], values: List[float],
                     queries: List[List[str]]) -> List[float]:
        # 改造版并查集, 加入一个倍数, pre[x] = (祖先, x相对于祖先的倍数)
        # 注意在query的时候要判断分子分母的祖先是否是一个, 如果不是, 则直接返回-1.0
        pre = {}

        def find(x):
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
            px = find(x)
            py = find(y)
            # 老方法, 将一个的祖先设为另一个的祖先, 注意倍数的变化
            # 举个例子(这里只关心倍数): 假设当前multiple是5, 即x=5y, 然后x=4px, y=3py, 那么要求px/py的话, 代入即可得px = 5*3/4 py, 5是multiple, 3是py[1], 4是px[1], 这就是下面的倍数由来
            pre[px[0]] = (py[0], multiple * py[1] / px[1])

        for i in range(len(values)):
            # 合并已知的方程式中的变量以及对应的倍数
            x, y, multiple = equations[i][0], equations[i][1], values[i]
            union(x, y, multiple)
        res = []
        for q in queries:
            if q[0] not in pre or q[1] not in pre:
                # 某个变量不存在在上面已知的方程式中, 直接返回-1.0
                res.append(-1.0)
            else:
                p0, p1 = find(q[0]), find(q[1])
                if p0[0] != p1[0]:
                    # 如果两个变量没有共同祖先, 即没有已知方程式将它们关联起来, 那么它们相除的结果也就无法确定了, 返回-1.0
                    res.append(-1.0)
                else:
                    # 两个变量可以有共同祖先表示, 那么其倍数的商即为当前除法表达式的值
                    res.append(p0[1] / p1[1])
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
