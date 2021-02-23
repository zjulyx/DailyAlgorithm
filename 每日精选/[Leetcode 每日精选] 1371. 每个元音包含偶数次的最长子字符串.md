> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)

> 今天继续做每日一题, 这道题也算是一类典型题目了, 值得一做

## 题目描述

给你一个字符串 s ，请你返回满足以下条件的最长子字符串的长度：每个元音字母，即 'a'，'e'，'i'，'o'，'u' ，在子字符串中都恰好出现了偶数次。

- 1 <= s.length <= 5 x 10^5
- s 只包含小写英文字母。

## 题目样例

### 示例 1

#### 输入

s = "eleetminicoworoep"

#### 输出

13

#### 解释

最长子字符串是 "leetminicowor" ，它包含 e，i，o 各 2 个，以及 0 个 a，u 。

### 示例 2

#### 输入

"leetcodeisgreat"

#### 输出

5

#### 解释

最长子字符串是 "leetc" ，其中包含 2 个 e 。

## 题目思考

1. 注意数据规模很大, 时间复杂度为 O(N^2)或者更高的算法肯定会超时
2. 如何利用元音字母只有 5 个这一条件?
3. 遍历的时候需要存储哪些内容?

## 解决方案

### 思路

- 经典的状态压缩动态规划, 一般遇到要保存的状态不多的问题时都可以通过状态压缩来解决
- 注意到元音字母只有 5 个, 所以根据不同字母的奇偶性总共也就 32 个状态
  - 状态为 0 时代表[0,0,0,0,0], 表示所有元音字母都有偶数个
  - 状态为 1 时代表[0,0,0,0,1], 表示只有 a 是奇数, 其余字母有偶数个
  - 状态为 2 时代表[0,0,0,1,0], 表示只有 e 是奇数, 其余字母有偶数个
  - ...
  - 状态为 31 时代表[1,1,1,1,1], 表示所有元音字母都有奇数个
- 可以利用这一点, 存不同状态下的最左边下标, 并保存当前的状态, 初始化状态为 0, 因为此时各个元音字母都是 0 个, 即偶数个
- 如果当前下标 i 对应的状态已经有最左下标 j 了, 那么[j+1,i]这段区间的各个元音字母的数目一定是偶数个, 这样才能保证 i 和 j 处的状态相同, 如果其长度大于结果值就更新结果即可
- 特别注意当状态为 0 时本身就是合法的, 长度就是当前下标+1, 所以可以初始化该状态的下标为-1, 这样就可以和其他状态下的逻辑一致了

### 复杂度

- 时间复杂度 O(N): 只需要遍历每个字符一次
- 空间复杂度 O(1): 只使用了常数空间的列表和字典

### 代码

#### Python 3

```python
class Solution:
    def findTheLongestSubstring(self, s: str) -> int:
        # 位运算时每个元音字母对应的下标位置, 这个顺序可以随意调换, 只要保证状态各不相同即可
        vowelindex = {'a': 0, 'e': 1, 'i': 2, 'o': 3, 'u': 4}
        # 初始化只有0状态下标是-1, 其他都标记成None代表不存在
        leftindex = [-1] + [None] * 31
        # 初始化状态为0, 因为所有元音字母都是0, 都是偶数个
        curstate = 0
        res = 0
        for i, c in enumerate(s):
            if c in vowelindex:
                # 如果当前字符是元音字母, 利用异或交换其奇偶性
                curstate ^= 1 << vowelindex[c]
            if leftindex[curstate] is None:
                leftindex[curstate] = i
            else:
                res = max(res, i - leftindex[curstate])
        return res
```

#### C++

```c++
class Solution {
public:
    int findTheLongestSubstring(string s) {
        unordered_map<char, int> vowelindex({{'a', 0},
                                             {'e', 1},
                                             {'i', 2},
                                             {'o', 3},
                                             {'u', 4}});
        vector<int> leftindex(32, INT_MIN);
        leftindex[0] = -1;
        int res = 0;

        for (int i = 0, state = 0; i < s.size(); ++i) {
            if (vowelindex.find(s[i]) != vowelindex.end()) {
                state ^= 1 << vowelindex[s[i]];
            }
            if (leftindex[state] == INT_MIN) {
                leftindex[state] = i;
            } else {
                res = max(res, i - leftindex[state]);
            }
        }

        return res;
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
