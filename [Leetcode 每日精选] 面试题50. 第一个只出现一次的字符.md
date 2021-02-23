> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

> 今天来一道简单题~思路虽然简单, 但也有一些优化的空间

## 题目描述

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。

## 题目样例

### 示例 1

#### 输入

s = "abaccdeff"

#### 输出

"b"

### 示例 2

#### 输入

s = ""

#### 输出

" "

## 题目思考

1. 只出现一次: 可以用什么数据结构?
2. 有没有一次遍历就得出结果的方法?

## 解决方案

### 方案 1

#### 思路

- 直接根据题干意思, 两次遍历
- 第一次遍历使用一个计数字典统计每个字符的次数
- 第二次遍历从头开始, 查看当前字符的次数是否为 1, 是的话直接返回
- 次数都不是 1 的话就返回空格

#### 复杂度

- 时间复杂度 O(N): 遍历两次字符串, 2N
- 空间复杂度 O(1): 虽然用到了一个计数字典, 但 key 只有有限个字符, 不与 N 相关

#### 代码

##### Python 3

```python
from collections import defaultdict

class Solution:
    def firstUniqChar(self, s: str) -> str:
        cnt = defaultdict(int)
        for c in s:
            cnt[c] += 1
        for c in s:
            if cnt[c] == 1:
                return c
        return ' '
```

##### C++

```cpp
class Solution {
public:
    char firstUniqChar(string s) {
        unordered_map<char, int> count;

        for (auto c : s) {
            ++count[c];
        }

        for (auto c : s) {
            if (count[c] == 1) {
                return c;
            }
        }

        return ' ';
    }
};
```

### 方案 2

#### 思路

- 方案 1 需要两次遍历, 那么如果要求一次遍历就得出结果怎么做呢?
- 那么就需要动态的判断当前字符的状态, 一个计数字典是无法做到的, 因为不知道当前字符后面会不会有重复
- 可以改用 1 个下标字典和一个无效集合
- 字典存{字符:首次出现的下标}, 集合存无效字符
- 动态判断: 遍历时如果 c 已经在字典中, 删除它并加入无效集合
- 如果字典为空则返回' ', 否则返回最小的字典值对应的字符

#### 复杂度

- 时间复杂度 O(N): 同样需要遍历整个字符串, 但这次只用了 1 次遍历
- 空间复杂度 O(1): 虽然用到了一个计数字典和一个集合, 但 key 只有有限个字符, 不与 N 相关

#### 代码

##### Python 3

```python
class Solution:
    def firstUniqChar(self, s: str) -> str:
        # 字典+集合
        firstIndex = {}
        invalid = set()
        for i, c in enumerate(s):
            if c in firstIndex:
                # 有重复了, 加入无效字符集合, 同时从下标字典中删除
                del firstIndex[c]
                invalid.add(c)
            else:
                # 没有重复, 且没有在无效集合, 说明c是第一次遇到, 加入下标字典中
                if c not in invalid:
                    firstIndex[c] = i
        if not firstIndex:
            return ' '
        mnIndex = min(firstIndex.values())
        return s[mnIndex]
```

##### C++

```cpp
class Solution
{
public:
    char firstUniqChar(string s)
    {
        unordered_map<char, int> first_visited;
        unordered_set<char> not_first_visited;

        for (int i = 0; i < s.size(); ++i)
        {
            if (first_visited.find(s[i]) != first_visited.end())
            {
                first_visited.erase(s[i]);
                not_first_visited.insert(s[i]);
            }
            else if (not_first_visited.find(s[i]) == not_first_visited.end())
            {
                first_visited.emplace(s[i], i);
            }
        }

        if (first_visited.empty())
        {
            return ' ';
        }

        auto min_iter = min_element(
            first_visited.begin(), first_visited.end(),
            [](const std::pair<char, int> &left, const std::pair<char, int> &right) { return left.second < right.second; });

        return min_iter->first;
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
