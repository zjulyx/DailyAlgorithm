> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/baby-names-lcci/)

> 今天继续来做并查集的问题, 这道题多了一些变化, 但核心仍然是并查集. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

每年，政府都会公布一万个最常见的婴儿名字和它们出现的频率，也就是同名婴儿的数量。有些名字有多种拼法，例如，John 和 Jon 本质上是相同的名字，但被当成了两个名字公布出来。给定两个列表，一个是名字及对应的频率，另一个是本质相同的名字对。设计一个算法打印出每个真实名字的实际频率。注意，如果 John 和 Jon 是相同的，并且 Jon 和 Johnny 相同，则 John 与 Johnny 也相同，即它们有传递和对称性。

在结果列表中，选择字典序最小的名字作为真实名字。

- names.length <= 100000

## 题目样例

### 示例

#### 输入

`names = ["John(15)","Jon(12)","Chris(13)","Kris(4)","Christopher(19)"], synonyms = ["(Jon,John)","(John,Johnny)","(Chris,Kris)","(Chris,Christopher)"]`

#### 输出

`["John(27)","Chris(36)"]`

#### 解释

- 根据同名关系可以合并成只有两个名字, 其频率为转换而来的名字的频率之和

## 题目思考

1. 相比传统并查集, 需要做哪些改变?
2. 需要额外记录什么信息?

## 解决方案

### 思路

- **分析**
  - 相比前面两题, 该题多了两个条件, 一是要求祖先最小, 二是需要求频率之和
  - 针对第一个需求, 我们可以更改 union 方法, 将字典序较大的祖先指向字典序较小的祖先, 这样就能保证最终的祖先一定是字典序最小的
  - 针对第二个需求, 我们可以额外引入一个计数字典, 记录祖先的频率, 每次遍历到一个新名字, 其对应的祖先的频率就加上当前的频率
  - 然后最终再遍历计数字典, 将 kv 转换成结果的格式即可
- **实现**
  - 下面的代码中对每个步骤都有注释, 方便大家理解

### 复杂度

- 时间复杂度 O((N+M)logN): 假设 N 为名字个数, M 为名字对个数, 那么需要分别循环 M 和 N 合并和统计频率, 每次 find/union 操作需要 logN 时间, 所以总共复杂度就是 O((N+M)logN)
- 空间复杂度 O(N): pre 字典中存 N 个名字

### 代码

```python
class Solution:
    def trulyMostPopular(self, names: List[str],
                         synonyms: List[str]) -> List[str]:
        # 并查集变种, 先找到所有相同的名字, 然后再统计数字
        # 注意祖先需要是字典序最小的, 所以需要稍微改动union逻辑
        pre = {}

        def find(x):
            if x not in pre:
                pre[x] = x
            elif pre[x] != x:
                pre[x] = find(pre[x])
            return pre[x]

        def union(x, y):
            px = find(x)
            py = find(y)
            # 保证祖先的字典序更小
            if px > py:
                pre[px] = py
            else:
                pre[py] = px

        for s in synonyms:
            # parse两个名字, 并合并
            x, y = s[1:-1].split(',')
            union(x, y)
        cnts = defaultdict(int)
        for t in names:
            i = t.find('(')
            # parse当前名字和频率
            name = t[:i]
            cnt = int(t[i + 1:t.find(')')])
            # 累加到祖先对应的频率中
            cnts[find(name)] += cnt
        res = []
        for k in cnts:
            # 转换成结果要求的格式
            res.append(k + '(' + str(cnts[k]) + ')')
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
