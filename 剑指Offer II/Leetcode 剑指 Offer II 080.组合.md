> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/uUsW3B/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

### 示例 1:

- 输入: n = 4, k = 2
- 输出:

```
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

### 示例 2:

- 输入: n = 1, k = 1
- 输出: [[1]]

### 提示:

- 1 <= n <= 20
- 1 <= k <= n

## 题目思考

1. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 首先我们可以尝试用递归的思路来解决
- 分析题目, 我们可以发现针对 1 到 n 的每个数字, 都可以细分成两种情况: 使用该数字和不使用该数字
- 大家可以将整个过程想象成一个二叉树: 从 1 开始遍历数字, 每遍历到一个数字, 都可以将其分成两个分支继续向下, 直到遍历到 n 为止
- 我们可以基于上述分析进行递归求解, 具体做法如下:
  - 传入当前数字以及当前使用的数字的子集
  - 然后递归调用下一数字, 此时分为两种情况: 使用当前数字和不使用当前数字
  - 如果子集长度达到 k, 则就形成了一个有效组合, 将其加入到最终结果, 然后返回
  - 另外如果当前子集长度加上剩余数字个数小于 k, 则肯定不可能构成有效组合, 直接返回
- 由于每次都添加的是不同数字, 所以每个递归出口形成的有效组合也各不相同, 无需手动去重

#### 复杂度

- 时间复杂度 O(2^N): 每遍历一个数字我们都有两种选择, 所以总时间是 2^N
- 空间复杂度 O(N): 递归栈的消耗, 对应的是上述分析中二叉树的高度, 即数字个数 N

#### 代码

##### Python 3

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        # 方法1: 递归传入当前下标和列表
        res = []

        def dfs(i, subset):
            if len(subset) == k:
                # 递归出口#1, 将其加入最终结果集
                res.append(subset)
                return
            if len(subset) + n - i + 1 < k:
                # 递归出口#2, 即使后面的数字全用上, 得到的集合长度也小于k
                # 无效情况, 直接返回
                return
            dfs(i + 1, subset + [i])
            dfs(i + 1, subset)

        dfs(1, [])
        return res
```

### 方案 2

#### 思路

- 接下来我们尝试用迭代的思路来解决
- 根据方案 1 的分析, 我们不难发现 1 到 N 数字形成的所有子集都可以用一个长度为 N 的 mask 来表示
- 其中 mask 的第 i 位为 1 就对应使用数字 i+1, 为 0 则不使用该数字
- 所以我们可以依次遍历[0, 2^N-1], 统计当前 mask 的 1 的个数
- 如果它正好等于 k, 则构建其对应的子集, 并加入最终结果中即可

#### 复杂度

- 时间复杂度 O(N\*2^N): 需要遍历 2^N 个元素, 内层循环需要遍历 N 位得到具体的子集
- 空间复杂度 O(1): 只使用了几个常数空间的变量 (结果集占用的空间不计算在内)

#### 代码

##### Python 3

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        # 方法2: 迭代+位运算
        res = []
        for mask in range(1, 1 << n):
            # 得到当前mask的1的个数
            cnt = bin(mask).count("1")
            if cnt == k:
                # 有k个1时, 说明是有效组合, 将其加入最终结果
                res.append([x + 1 for x in range(n) if (mask >> x) & 1])
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
