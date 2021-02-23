> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/accounts-merge/)

> 今天继续来做并查集的问题, 这道题又有另外一些变化, 但核心仍然是并查集. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **并查集** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

给定一个列表 accounts，每个元素 accounts[i]  是一个字符串列表，其中第一个元素 accounts[i][0]  是   名称 (name)，其余元素是 emails 表示该帐户的邮箱地址。

现在，我们想合并这些帐户。如果两个帐户都有一些共同的邮件地址，则两个帐户必定属于同一个人。请注意，即使两个帐户具有相同的名称，它们也可能属于不同的人，因为人们可能具有相同的名称。一个人最初可以拥有任意数量的帐户，但其所有帐户都具有相同的名称。

合并帐户后，按以下格式返回帐户：每个帐户的第一个元素是名称，其余元素是按顺序排列的邮箱地址。accounts 本身可以以任意顺序返回。

- accounts 的长度将在[1，1000]的范围内。
- accounts[i]的长度将在[1，10]的范围内。
- accounts[i][j]的长度将在[1，30]的范围内。

## 题目样例

### 示例

#### 输入

accounts = [["John", "johnsmith@mail.com", "john00@mail.com"], ["John", "johnnybravo@mail.com"], ["John", "johnsmith@mail.com", "john_newyork@mail.com"], ["Mary", "mary@mail.com"]]

#### 输出

[["John", 'john00@mail.com', 'john_newyork@mail.com', 'johnsmith@mail.com'], ["John", "johnnybravo@mail.com"], ["Mary", "mary@mail.com"]]

#### 解释

- 第一个和第三个 John 是同一个人，因为他们有共同的电子邮件 "johnsmith@mail.com"。
- 第二个 John 和 Mary 是不同的人，因为他们的电子邮件地址没有被其他帐户使用。
- 我们可以以任何顺序返回这些列表，例如答案[['Mary'，'mary@mail.com']，['John'，'johnnybravo@mail.com']，['John'，'john00@mail.com'，'john_newyork@mail.com'，'johnsmith@mail.com']]仍然会被接受。

## 题目思考

1. 如何进行合并?
2. 需要额外记录什么信息?

## 解决方案

### 思路

- **分析**
  - 这道题多了一个账户名字, 而且也不像前几道题那样直接给出合并关系, 所以我们需要进行一些额外处理
  - 首先需要找到每个账户名字下面的所有 email, 这个可以通过定义一个 key 是名字, value 是 email 集合的字典实现. 注意每个名字对应的 email 集合中的所有 email 可能分属不同的人, 因为不同的人可能用相同的名字.
  - 然后遍历每个账户, 统计其名字到 email 的映射关系, 并加入上面的字典中. 还需要将同属同一账户的 email 进行合并, 这里就用到并查集的 union 操作. 这里的合并操作和前面几道题不同的地方在于没有直接给出合并的两个点, 我们需要自己找出并合并. **特别注意我们只需要把所有 email 和第一个 email 合并即可, 这样就能保证它们具有相同的祖先, 而无需两两合并增加时间复杂度.**
  - 最后遍历名字=>email 集合字典, 将具有相同祖先的 email 归为一类, 最后将所有类按照结果格式保存即可
- **实现**
  - 下面的代码中对每个步骤都有注释, 方便大家理解

### 复杂度

- 时间复杂度 O(NlogM): 假设 N 为 account 每一项的长度之和, M 是 email 的个数. 那么两次循环的个数都是 N, 然后并查集操作的时间复杂度为 O(logM), 总时间复杂度就是 O(NlogM)
- 空间复杂度 O(M): pre 字典中存 M 个 email

### 代码

#### Python 3

```python
import collections


class Solution:
    def accountsMerge(self, accounts: List[List[str]]) -> List[List[str]]:
        # 核心并查集部分
        pre = {}

        def find(x):
            if x not in pre:
                pre[x] = x
            elif x != pre[x]:
                pre[x] = find(pre[x])
            return pre[x]

        def union(x, y):
            pre[find(x)] = find(y)

        # nameToEmails字典记录每个名字到它名下的所有email的集合
        nameToEmails = collections.defaultdict(set)
        for account in accounts:
            name = account[0]
            # 把当前email集合合并到对应的账户名下
            nameToEmails[name] |= set(account[1:])
            for i in range(1, len(account)):
                # 合并属于同一账户的email, 注意这里只需要和第一个union即可, 这样就能保证对应的所有email同属于一个集合, 因为它们都指向同一个祖先, 无需两两union
                union(account[1], account[i])
        res = []
        for name in nameToEmails:
            # emailDict存当前名字对应的所有email的{祖先:[emails]}
            emailDict = collections.defaultdict(list)
            for email in nameToEmails[name]:
                # 将email对应的祖先作为key, 将email本身加到对应的value列表中
                emailDict[find(email)].append(email)
            for key in emailDict:
                # 转换成结果格式, 注意email列表需要排序
                res.append([name] + sorted(emailDict[key]))
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
