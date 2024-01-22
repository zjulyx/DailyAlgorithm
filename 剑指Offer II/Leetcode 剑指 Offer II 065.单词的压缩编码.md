> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/iSwD2y/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

- 单词数组 words 的 有效编码 由任意助记字符串 s 和下标数组 indices 组成，且满足：
  - words.length == indices.length
  - 助记字符串 s 以 '#' 字符结尾
  - 对于每个下标 indices[i] ，s 的一个从 indices[i] 开始、到下一个 '#' 字符结束（但不包括 '#'）的 子字符串 恰好与 words[i] 相等
- 给定一个单词数组 words ，返回成功对 words 进行编码的最小助记字符串 s 的长度 。

### 示例 1：

- 输入：words = ["time", "me", "bell"]
- 输出：10
- 解释：一组有效编码为 s = "time#bell#" 和 indices = [0, 2, 5] 。
  - words[0] = "time" ，s 开始于 indices[0] = 0 到下一个 '#' 结束的子字符串，如加粗部分所示 "**time**#bell#"
  - words[1] = "me" ，s 开始于 indices[1] = 2 到下一个 '#' 结束的子字符串，如加粗部分所示 "ti**me**#bell#"
  - words[2] = "bell" ，s 开始于 indices[2] = 5 到下一个 '#' 结束的子字符串，如加粗部分所示 "time#**bell**#"

### 示例 2：

- 输入：words = ["t"]
- 输出：2
- 解释：一组有效编码为 s = "t#" 和 indices = [0] 。

### 提示：

- 1 <= words.length <= 2000
- 1 <= words[i].length <= 7
- words[i] 仅由小写字母组成

## 题目思考

1. 需要使用什么数据结构?

## 解决方案

#### 思路

- 我们先来理解下题目, 题目要求最小助记字符串, 要想做到最小, 那就得尽可能的共用助记字符串
- 不难发现: 当且仅当字符串 a 是字符串 b 的后缀时, 两者才可以共用同一个助记字符串, 正如示例 1 中的"me"和"time"; 否则只能使用两个单独的助记字符串
- 在之前的题目中, 我们已经了解到, 字典树 trie 可以用来解决前缀问题 (TODO-062/063)
- 而这道题需要处理后缀, 我们如果将每个单词进行翻转, 那么就转化成了前缀问题
- 这就自然引出了核心思路: 在添加翻转后的单词到 trie 时, 如果该单词的路径已经在 trie 中了, 则说明当前单词是之前某个单词的后缀, 可共用之前的助记字符串, 总助记字符串的长度不变; 否则说明需要新的助记字符串, 总助记字符串的长度加上当前单词长度加 1 (末尾的#号)
- 另外特别注意, 要想保证添加当前单词时, 以它作为后缀的单词已经在 trie 中, 那么必须对单词数组按照长度从大到小排序, 这样才不会遗漏
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(NlogN+NC): 假设 N 是单词的数目, C 是每个单词的平均长度, 那么对单词数组的排序复杂度是 O(NlogN), 而插入单词到 trie 的复杂度是 O(NC)
- 空间复杂度 O(NC): 最差情况下, trie 需要存每个单词的所有字符

#### 代码

```python
class Node:
    def __init__(self):
        self.children = {}


class Trie:
    def __init__(self):
        self.root = Node()

    def insert(self, word):
        cur = self.root
        # isExist标记用于判断当前单词对应的路径是否已经存在于字典树中
        isExist = True
        for c in word:
            if c not in cur.children:
                # 当前字符不存在, 将isExist置为false
                isExist = False
                cur.children[c] = Node()
            cur = cur.children[c]
        # 如果当前单词对应的路径已经存在, 则可以共用之前的助记字符串;
        # 否则需要将当前单词加上结尾的#作为新的助记字符串
        return 0 if isExist else len(word) + 1


class Solution:
    def minimumLengthEncoding(self, words: List[str]) -> int:
        # 先将单词按照长度从大到小排序, 保证长的单词先添加
        words.sort(key=lambda w: -len(w))
        # 初始化字典树
        trie = Trie()
        res = 0
        for w in words:
            # 注意这里添加单词的逆序, 这样才能判断某个单词是否是另一个单词的后缀
            res += trie.insert(w[::-1])
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
