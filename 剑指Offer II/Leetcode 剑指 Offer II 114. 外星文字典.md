> 题目难度: 困难

> [原题链接](https://leetcode.cn/problems/Jf1JuT/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

现有一种使用英语字母的外星文语言，这门语言的字母顺序与英语顺序不同。

给定一个字符串列表 words ，作为这门语言的词典，words 中的字符串已经 按这门新语言的字母顺序进行了排序 。

请你根据该词典还原出此语言中已知的字母顺序，并 按字母递增顺序 排列。若不存在合法字母顺序，返回 "" 。若存在多种可能的合法字母顺序，返回其中 任意一种 顺序即可。

字符串 s 字典顺序小于 字符串 t 有两种情况：

在第一个不同字母处，如果 s 中的字母在这门外星语言的字母顺序中位于 t 中字母之前，那么 s 的字典顺序小于 t 。
如果前面 min(s.length, t.length) 字母都相同，那么 s.length < t.length 时，s 的字典顺序也小于 t 。

### 示例 1：

- 输入：words = ["wrt","wrf","er","ett","rftt"]
- 输出："wertf"

### 示例 2：

- 输入：words = ["z","x"]
- 输出："zx"

### 示例 3：

- 输入：words = ["z","x","z"]
- 输出：""
- 解释：不存在合法字母顺序，因此返回 ""。

### 提示：

- 1 <= words.length <= 100
- 1 <= words[i].length <= 100
- words[i] 仅由小写英文字母组成

## 题目思考

1. 需要使用什么算法和数据结构?

## 解决方案

- 观察题目, 要想还原出字母顺序, 我们需要利用已知字符串列表的顺序信息, 先比较所有相邻的字符串, 并找到它们第一个不同的字符, 然后根据这个字符的大小关系建立一个有向图, 并维护其入度, 例如`a->b`, 表示`a`在`b`之前, `b`的入度加 1
- 等找到所有字符的大小关系和入度后, 我们就可以将问题转换成上周类似的拓扑排序的思路, 先加入顺序最小(入度为 0)的字符, 等到所有顺序最小的字符都加入后, 就可以加入顺序第二小的字符, 以此类推, 大家可以参考上周文章[Leetcode 剑指 Offer II 113.课程表 II](https://mp.weixin.qq.com/s/a23FuLILd3lwPlpOjNa4Uw)的思考过程
- 举个例子, 对于`words = ["wrt","wrf","er","ett","rftt"]`, 我们比较相邻字符串后, 可以得到以下的大小关系:`t->f`,`w->e`, `r->t`, `e->r`
  - 首先 w 的入度为 0, 所以先加入 w;
  - 然后 e 的入度变成 0, 所以加入 e;
  - 再然后 r 的入度变成 0, 所以加入 r;
  - 接下来 t 的入度变成 0, 所以加入 t;
  - 最后 f 的入度变成 0, 所以加入 f;
- 这样我们就可以得到最终的顺序是`wertf`
- 具体代码实现时, 分为以下四步:
  1. 首先比较所有相邻字符串, 遍历其相同位置字符, 找到第一个不同的字符, 从而建立大小关系和入度, 如果发现顺序矛盾 (例如前面已经得到`a<b`, 但是新的单词对又要求`b<a`; 或者后一个单词是前一个单词的前缀, 例如前者`abc`和后者`ab`,此时应该是后者更小才对), 则直接返回空字符串
  2. 然后遍历所有字符, 找出入度为 0 的字符, 加入结果集, 并等待后续遍历处理
  3. 接下来遍历当前可以加入最终结果的字符, 利用大小关系找出这些字符指向的字符, 将它们入度减 1, 如果某个字符入度变成了 0, 则代表它的顺序确定了, 将其加入最终结果中, 并等待后续遍历处理
  4. 最后遍历结束时, 如果最终结果长度等于字符集合长度, 说明所有字符的顺序都可以确定, 否则说明有些字符顺序无法确定, 返回空字符串
- 下面的代码对必要步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(MN+C)`: 单词数是 N, 单词平均长度是 M, 不同的字符数是 C, 构建后继集合、入度表和字符集合的复杂度是`O(MN)`, 拓扑排序部分需要处理 N-1 个边和 C 个节点, 即 `O(N+C)`, 所以总时间复杂度就是`O(MN+C)`
- 空间复杂度 `O(N+C)`: 后继集合存 N-1 个值, 入度表和字符集合存 C 个值

#### 代码

```python
class Solution:
    def alienOrder(self, words: List[str]) -> str:
        # 大于当前字符的字符集合(后继集合)
        nxt = collections.defaultdict(set)
        # 当前字符的入度(小于当前字符的不同字符个数)
        indegree = collections.defaultdict(int)
        # 字符集合
        chars = set()
        for i, cur in enumerate(words):
            # 更新字符集合
            chars |= set(cur)
            if i > 0:
                pre = words[i - 1]
                for c1, c2 in zip(pre, cur):
                    # 找pre和cur的第一个不同字符
                    if c1 != c2:
                        if c1 in nxt[c2]:
                            # 前面已经建立了c1>c2的连接关系, 与当前期望pre<cur的顺序矛盾, 说明顺序不合法, 返回""
                            return ""
                        if c2 not in nxt[c1]:
                            # c1<c2, 且该连接关系尚未建立, 更新c1的后继集合和c2的入度
                            nxt[c1].add(c2)
                            indegree[c2] += 1
                        break
                else:
                    # 此时说明pre和cur具有相同前缀
                    if len(pre) > len(cur):
                        # pre的长度更长, 应该位于cur之后才对, 所以顺序不合法, 返回""
                        return ""
        # 拓扑排序部分
        res = []
        for c in chars:
            if indegree[c] == 0:
                # 初始加入入度为0的字符, 这些字符顺序最小
                res.append(c)
        for c in res:
            for nc in nxt[c]:
                indegree[nc] -= 1
                if indegree[nc] == 0:
                    # nc的入度变成了0, 说明小于它的字符都已加入最终集合, 可以安全地加入nc了
                    res.append(nc)
        # 如果最终结果长度等于总字符数, 说明所有字符的顺序都可以确定, 否则说明有些字符顺序无法确定, 返回""
        return "".join(res) if len(res) == len(chars) else ""
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
