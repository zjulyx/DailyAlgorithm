> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某班级考试成绩按非严格递增顺序记录于整数数组 scores，请返回目标成绩 target 的出现次数。

### 示例 1：

- 输入: scores = [2, 2, 3, 4, 4, 4, 5, 6, 6, 8], target = 4
- 输出: 3

### 示例 2：

- 输入: scores = [1, 2, 3, 5, 7, 9], target = 6
- 输出: 0

### 提示：

- 0 <= scores.length <= 105
- -10^9 <= scores[i] <= 109
- scores 是一个非递减数组
- -10^9 <= target <= 10^9

## 题目思考

1. 可以用小于 O(N)的时间复杂度得出结果吗?
2. 可以做到 O(1) 空间复杂度吗?

## 解决方案

#### 思路

- 一个比较容易想到的思路是使用一个计数字典, 遍历一遍数组统计每个数的出现次数, 最后返回 target 的次数. 但这样时间和空间复杂度都是 O(N), 也用不上题目中数组是排序的这一条件
- 如何利用排序这一条件统计数字出现次数呢? 我们可以尝试二分查找的思路, 分别找到该数字的左右边界对应的下标, 然后次数就是`右边界-左边界+1` (数组存在该数字的情况下)
  - 查找左边界: 如果找到等于 target 的数时, 需要继续往左找. 而如果数组中没有等于 target 的数, 则直接返回 None, 此时就知道该数字的出现次数为 0 了, 无需继续找右边界
  - 查找右边界: 如果找到等于 target 的数时, 需要继续往右找
- 注意可以将二分查找代码整合到一个方法中, 传入一个 flag 标记当前是找左边界还是右边界, 以减少代码冗余
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(logN): 二分查找两次
- 空间复杂度 O(1): 只定义了几个变量

#### 代码

```python
class Solution:
    def countTarget(self, scores: List[int], target: int) -> int:
        def binarySearch(isleft):
            # 传入flag isleft, 标记当前是查找左边界还是右边界
            s, e = 0, len(scores) - 1
            # 初始化结果为None
            res = None
            while s <= e:
                m = (s + e) >> 1
                if scores[m] == target:
                    if isleft:
                        # 当前查找的是左边界, 更新结果为等于target的更小的下标, 同时向左继续查找
                        res = m if res is None else min(res, m)
                        e = m - 1
                    else:
                        # 当前查找的是右边界, 更新结果为等于target的更大的下标, 同时向右继续查找
                        res = m if res is None else max(res, m)
                        s = m + 1
                elif scores[m] < target:
                    s = m + 1
                else:
                    e = m - 1
            return res

        left = binarySearch(True)
        if left is None:
            # 如果左边界不存在, 则说明整个数组没有target, 直接返回0
            return 0
        right = binarySearch(False)
        # 最终结果就是右边界-左边界+1
        return right - left + 1
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
