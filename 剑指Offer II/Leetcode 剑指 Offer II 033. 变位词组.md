> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/sfvd7V/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个字符串数组 strs ，将 变位词 组合在一起。 可以按任意顺序返回结果列表。

注意：若两个字符串中每个字符出现的次数都相同，则称它们互为变位词。

### 示例 1:

- 输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
- 输出: [["bat"],["nat","tan"],["ate","eat","tea"]]

### 示例 2:

- 输入: strs = [""]
- 输出: [[""]]

### 示例 3:

- 输入: strs = ["a"]
- 输出: [["a"]]

### 提示：

- 1 <= strs.length <= 10^4
- 0 <= strs[i].length <= 100
- strs[i] 仅包含小写字母

## 题目思考

1. 变位词有什么特点?

## 解决方案

#### 思路

- 根据题目描述, 变位词是指字母相同, 但排列不同的字符串
- 那么如果将这些变位词自身按照字典序排序, 其排序后的字符串一定相同
- 我们可以利用这一特点, 新建一个键为排序后的字符串, 值用于存储原始字符串列表的字典
- 然后我们就可以将各个单词排序后的字符串作为字典的 key, 并将原始字符串存储在字典的值列表中
- 最终结果即为字典的所有值列表
- 下面的代码就对应了上面的整个过程, 并且有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(NMlogM)`: N 是字符串数目, M 是字符串长度, 需要遍历所有字符串, 然后内部需要对字符串进行排序, 所以时间复杂度是`O(NMlogM)`
- 空间复杂度 `O(NM)`: 额外使用了一个字典, 包含所有字符串

#### 代码

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        # 排序后字符串作为key
        d = collections.defaultdict(list)
        for s in strs:
            # key就是排序后的字符串
            key = "".join(sorted(s))
            # 将原始字符串加入字典键key对应的列表中
            d[key].append(s)
        # 最终结果即为字典所有的值, 每个值是一个变位词列表
        return list(d.values())
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
