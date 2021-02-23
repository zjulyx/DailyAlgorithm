> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/minimum-window-substring/)

## 题目描述

给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字符的最小子串。

## 题目样例

### 示例

#### 输入

```
S = "ADOBECODEBANC", T = "ABC"
```

#### 输出

```
"BANC"
```

### **说明**

1. 如果 S 中不存这样的子串，则返回空字符串 `""`
2. 如果 S 中存在这样的子串，我们保证它是唯一的答案。

## 解决方案

#### 思路

- 统计128种字符在字符串t中出现的次数count
- 设置记录与字符串t相差的字符个数distance，其初始值为len(t)
- 利用滑动窗口的思想，设置当前的左边界为i，其初始值为0
- 遍历字符串s的字符，字符所在的位置为窗口的右边界
- 每次滑动右边界，根据右边界对应的字符串改变count和distance的内容
- 如果distance值为0，则该区间为一个备选区间，检查该区间是否为更小区间
- 接下来滑动左边界，同时改变count和distance的内容，通过检查distance是否为0来判断其是否为备选区间

#### 复杂度

设字符集大小为 *C*

- 时间复杂度 O(C * |s| + |t|): 
- 空间复杂度 O(C)

#### 代码

##### Python 3

```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        count = [0] * 128
        distance = len(t)
        for c in t:
            count[ord(c)] += 1
        
        i = 0
        ans = [0, len(s)]

        for j, c in enumerate(s):
            # 在执行--count[ord(c)]操作之前，不在字符串t内的字符肯定不大于0
            if count[ord(c)] > 0:
                distance -= 1
            count[ord(c)] -= 1

            while distance == 0:
                if j - i < ans[1] - ans[0]:
                    ans = [i, j]
                # 在执行++count[ord(s[i])]操作之前，不在字符串t内的字符肯定小于0
                if count[ord(s[i])] == 0:
                    distance += 1
                count[ord(s[i])] += 1
                i += 1
        
        return s[ans[0]:ans[1]+1] if ans[1] != len(s) else ""
```

##### C++

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        vector<int> count(128, 0);
        for (auto c : t) {
            ++count[c];
        }
        int distance = t.size();
        vector<int> ans = {0, (int)s.size()};
        
        for (int i = 0, j = 0; j < s.size(); ++j) {
            // 在执行--count[s[j]]操作之前，不在字符串t内的字符肯定不大于0
            if (count[s[j]] > 0) {
                --distance;
            }
            --count[s[j]];

            while (!distance) {
                if (j - i < ans[1] - ans[0]) {
                    ans = {i, j};
                }
                // 在执行++count[s[i]]操作之前，不在字符串t内的字符肯定小于0
                if (!count[s[i]]) {
                    ++distance;
                }                    
                ++count[s[i]];
                ++i;
            }
        }

        if (ans[1] == s.size()) {
            return {};
        }

        return s.substr(ans[0], ans[1] - ans[0] + 1);
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
