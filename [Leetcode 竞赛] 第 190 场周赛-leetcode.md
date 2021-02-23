# [5416] 检查单词是否为句中其他单词的前缀

## 题目思考

1. 题目很直白, 直接遍历匹配就行了吧..

## 解决方案

### 思路

- 遍历数组, 检查当前字符串的前缀是目标字符串即可

### 复杂度

- 时间复杂度 O(NM): N 表示单词个数, M 表示搜索字符串长度, 对于每个单词需要匹配一遍搜索字符串, 所以复杂度是 O(NM)
- 空间复杂度 O(1) 或 O(N): 如果利用 split 转成字符串列表就是 O(N), 如果直接对字符串本身进行处理就是 O(1). 下面的代码中用的是 O(N)方法, 更简洁一些, 感兴趣的同学可以自己试试 O(1)空间的做法, 就是碰到空格后重置下标即可

### 代码

```python3 []
class Solution:
    def isPrefixOfWord(self, sentence: str, searchWord: str) -> int:
        a = sentence.split()
        res = -1
        for j, w in enumerate(a):
            if len(w) < len(searchWord):
                # 长度小于搜索字符串, 直接不满足要求
                continue
            for i in range(len(searchWord)):
                if w[i] != searchWord[i]:
                    # 某一位不匹配了, 直接break
                    break
            else:
                # 都匹配, 就是要求的结果
                # 注意要求的下标是从1开始的
                return j + 1
        return res
```

```cpp []
class Solution {
public:
    int isPrefixOfWord(string sentence, string searchWord) {
        auto split = [](const string& s, const char delim = ' ') {
            vector<string> res;
            istringstream stream(s);
            string substr;
            while(getline(stream, substr, delim)) {
                res.emplace_back(substr);
            }
            return res;
        };

        auto words = split(sentence);
        auto searchSize = searchWord.size();

        for (int i = 0; i < words.size(); ++i) {
            if (words[i].size() >= searchSize && words[i].compare(0, searchSize, searchWord) == 0) {
                return i + 1;
            }
        }

        return -1;
    }
};
```

---

# [5417] 定长子串中元音的最大数目

## 题目思考

1. 固定长度为 k 的字符串, 你想到了什么方法?

## 解决方案

### 思路

- 滑动窗口
- 固定一个长度为 k 的窗口, 以及窗口内元音字母的数目
- 然后窗口每次向右滑动 1 格, 更新元音字母数目, 以及结果即可
- 特别注意边界条件和初始的滑动过程, 我当时做的时候就是在窗口初始化之后, 错误地让左边界从 1 开始移除, 结果错了两次的..

### 复杂度

- 时间复杂度 O(N): 每个字符只需要遍历两次
- 空间复杂度 O(1): 只使用了几个变量和常数空间的集合

### 代码

```python3 []
class Solution:
    def maxVowels(self, s: str, k: int) -> int:
        v = {'a', 'e', 'i', 'o', 'u'}
        res = 0
        vcnt = 0
        for i in range(k):
            if s[i] in v:
                # 先初始化计算[0,k]窗口的元音字母个数
                vcnt += 1
        res = vcnt
        for b in range(len(s)):
            e = b + k
            if e >= len(s):
                break
            if s[e] in v:
                # 更新新的右边界的值
                vcnt += 1
            if s[b] in v:
                # 去除旧的左边界的值
                vcnt -= 1
            res = max(res, vcnt)
        return res
```

```cpp []
class Solution {
public:
    int maxVowels(string s, int k) {
        vector<int> isVowel(s.size(), 0);
        unordered_set<char> d = {'a', 'e', 'i', 'o', 'u'};

        for (int i = 0; i < s.size(); ++i) {
            if (d.find(s[i]) != d.end()) {
                isVowel[i] = 1;
            }
        }

        if (s.size() <= k) {
            return accumulate(isVowel.begin(), isVowel.end(), 0);
        }

        int num = accumulate(isVowel.begin(), isVowel.begin() + k, 0);
        int res = num;

        for (int i = k; i < s.size(); ++i) {
            num += isVowel[i] - isVowel[i - k];
            res = max(res, num);
        }

        return res;
    }
};
```

---

# [5418] 二叉树中的伪回文路径

## 题目思考

1. 先看数据规模, 数量是 10^5, O(N^2)算法很可能超时
2. 分析题目, 回文序列有哪些性质, 如何利用这一点?
3. 注意到每个节点的值只有 1~9, 你能想到什么优化思路吗?

## 解决方案

### 方案 1

#### 思路

- 根据回文序列性质, 其中的每个字符的个数是有要求的, 最多只能有 1 个字符是奇数个(放在最中间), 其他字符都必须是偶数个. 当然也可以所有字符都是偶数个, 即对称中心是两字符的中间, 而不是某个字符本身.
- 利用这一点, 我们可以 dfs 遍历, 然后遍历过程中维护一个计数字典, key 是节点值, value 是对应的个数
- 然后遍历到叶子节点时, 统计当前计数字典是否满足回文序列的要求即可
- 可以额外增加一个变量 oddcnt 代表计数字典中数目为奇数的元素个数, 加速运算, 不需要每次在叶子节点处遍历所有计数字典统计奇数数目 key 的个数了
- 进一步优化: 可以将上面的计数字典和 oddcnt 转换成一个集合, 只存数目为奇数的元素即可, 然后每次该元素数目变化时, 就根据其新的个数将其从集合移入或移除

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历一遍
- 空间复杂度 O(N): 递归的栈的消耗, 集合只使用了常数个元素, 因为节点的值是 1~9

#### 代码

```python3 []
class Solution:
    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        self.res = 0
        if not root:
            return 0

        # 把集合移入和移出操作提取出来, 简化代码
        def changeset(oddsets, v):
            if v in oddsets:
                oddsets.remove(v)
            else:
                oddsets.add(v)

        def dfs(node, oddsets):
            if not node.left and not node.right:
                # 叶子节点, 且奇数个数的元素数目不大于1就是满足条件的路径
                if len(oddsets) <= 1:
                    self.res += 1
                return
            if node.left:
                # 注意每次改变集合状态后, 在dfs遍历完要恢复成原始状态, 避免对后面的遍历产生影响, 下同
                changeset(oddsets, node.left.val)
                dfs(node.left, oddsets)
                changeset(oddsets, node.left.val)
            if node.right:
                changeset(oddsets, node.right.val)
                dfs(node.right, oddsets)
                changeset(oddsets, node.right.val)

        dfs(root, {root.val})
        return self.res
```

```cpp []
class Solution {
public:
    int pseudoPalindromicPaths (TreeNode* root) {
        int res = 0;
        unordered_set<int> oddsets;

        auto changeset = [&oddsets](int val) {
            if (oddsets.find(val) == oddsets.end()) {
                oddsets.insert(val);
            } else {
                oddsets.erase(val);
            }
        };

        function<void(TreeNode*)> dfs = [&](TreeNode* root) {
            if (!root) {
                return;
            }

            changeset(root->val);

            if (root->left || root->right) {
                dfs(root->left);
                dfs(root->right);
            } else {
                if (oddsets.size() <= 1) {
                    ++res;
                }
            }

            changeset(root->val);
        };

        dfs(root);

        return res;
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们使用了一个集合来标记奇数数目元素
- 根据节点取值范围, 以及上面的 changeset 操作, 我们是不是也可以用位运算?
- 我们只需要取一个 10 位的数, 然后 changeset 就对应异或的过程, 这样相比方案 1 代码会简化很多
- 通常来说, 如果状态数不多的时候, 我们总是可以尝试把字典或者集合转成位运算, 这样时间和空间都会节省不少

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历两遍
- 空间复杂度 O(N): 递归的栈的消耗

#### 代码

```python3 []
class Solution:
    def pseudoPalindromicPaths(self, root: TreeNode) -> int:
        self.res = 0
        if not root:
            return 0

        def get1bit(n):
            # 计算一个数字1的位数
            res = 0
            while n:
                n &= n - 1
                res += 1
            return res

        def dfs(node, mask):
            if not node.left and not node.right:
                # 叶子节点, 且奇数个数的元素数目不大于1就是满足条件的路径
                if get1bit(mask) <= 1:
                    self.res += 1
                return
            if node.left:
                dfs(node.left, mask ^ (1 << node.left.val))
            if node.right:
                dfs(node.right, mask ^ (1 << node.right.val))

        dfs(root, 1 << root.val)
        return self.res
```

```cpp []
class Solution {
public:
    int pseudoPalindromicPaths (TreeNode* root) {
        int res = 0;

        auto countBits = [](int value) {
            int count = 0;

            while (value) {
                ++count;
                value &= value - 1;
            }

            return count;
        };

        function<void(TreeNode*, int)> countPath = [&](TreeNode* root, int mask) {
            if (!root) {
                return;
            }

            mask ^= 1 << (root->val);

            if (root->left || root->right) {
                countPath(root->left, mask);
                countPath(root->right, mask);
            } else if (countBits(mask) <= 1) {
                ++res;
            }
        };

        countPath(root, 0);

        return res;
    }
};
```

---

# [5419] 两个子序列的最大点积

## 题目思考

1. 观察数据规模, O(N^2)复杂度肯定可以, O(N^3)复杂度不太确定
2. 可以尝试动态规划的思路吗?
3. 如何进行预处理?

## 解决方案

### 思路

- 这种求子序列的问题可以先尝试能否用动态规划解决, 此题也是可以的, 不过需要一些优化
- 设 dp[i,j]为使用 nums1 的第 i 个元素和 nums2 的第 j 个元素所能得到的最大序列
- 那么不难看出: `dp[i,j] = max(nums1[i]*nums2[j], nums1[i]*nums2[j]+mx)`, mx 是 `max(dp[ii,jj]) (ii<i 且 jj<j)`, 可能为负数, 这时候就只用 i 和 j 自身的乘积了
- 如果我们不做任何优化的话, 也即每次计算 dp[i,j]的时候都计算一遍 mx, 这个复杂度就达到了 O(NMNM), 显然是不能接受的, 所以我们必须进行预处理
- 注意到 mx 的下标取值范围是小于当前 i 和 j 的所有组合, 所以我们完全可以利用这一点
- 额外引入两个数组 premx 和 curmx, 其长度为 nums2 的长度, premx[j]和 curmx[j]分别表示 i-1,j 及之前的最大 dp 值, 以及当前正在计算的 i,j 及之前的最大 dp 值
- 那么自然上面的 mx 就是 premx[j-1]了
- 而 curmx[j] 则可以根据当前的 dp 值以及上一个 curmx[j-1]进行更新
- 然后每次循环到新的 i 时就将 curmx 的值复制到 premx 即可
- 这样我们就做到了只需要两重循环计算出结果

### 复杂度

- 时间复杂度 O(MN): 只需要二重遍历, M 和 N 分别表示 nums1 和 nums2 的长度
- 空间复杂度 O(MN): 使用了大小为 MN 的 dp 数组

### 代码

```python3 []
class Solution:
    def maxDotProduct(self, nums1: List[int], nums2: List[int]) -> int:
        dp = collections.defaultdict(int)
        n1, n2 = len(nums1), len(nums2)
        res = -float('inf')
        premx = [-float('inf')] * n2
        curmx = [-float('inf')] * n2
        for i in range(n1):
            # 复制premx的值
            premx = curmx[::]
            for j in range(n2):
                v = nums1[i] * nums2[j]
                # dp转移公式, 注意考虑j-1<0和premx[j - 1] < 0的特殊情况
                dp[i,
                   j] = v if j - 1 < 0 or premx[j - 1] < 0 else v + premx[j -
                                                                          1]
                res = max(res, dp[i, j])
                # 更新curmx
                curmx[j] = max(curmx[j], dp[i, j])
                if j - 1 >= 0:
                    curmx[j] = max(curmx[j], curmx[j - 1])
        return res
```

```cpp []
class Solution {
public:
    int maxDotProduct(vector<int>& nums1, vector<int>& nums2) {
        int res = INT_MIN;
        int n1 = nums1.size();
        int n2 = nums2.size();
        vector<int> curmx(n2, INT_MIN);

        for (int i = 0; i < n1; ++i) {
            auto premx = curmx;
            for (int j = 0; j < n2; ++j) {
                int value = nums1[i] * nums2[j];
                if (j > 0 && premx[j - 1] > 0) {
                    value += premx[j - 1];
                }
                res = max(res, value);
                curmx[j] = max(curmx[j], value);
                if (j > 0) {
                    curmx[j] = max(curmx[j], curmx[j - 1]);
                }
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

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
