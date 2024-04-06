> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/SsGoHC/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

### 示例 1：

- 输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
- 输出：[[1,6],[8,10],[15,18]]
- 解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].

### 示例 2：

- 输入：intervals = [[1,4],[4,5]]
- 输出：[[1,5]]
- 解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。

### 提示：

- 1 <= intervals.length <= 10^4
- intervals[i].length == 2
- 0 <= starti <= endi <= 10^4

## 题目思考

1. 如何优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 最容易想到的思路就是针对每个区间, 遍历其他区间查看两者是否有重叠, 有的话两者合并, 没有的话将当前区间加入最终结果
- 不过这样使用了两重循环, 时间复杂度达到了 `O(N^2)`, 如何优化呢?
- 如果我们按照区间起点从小到大排序, 那么有重叠的区间一定是挨着的, 这样我们只需要比较相邻的, 无需两重循环判断是否重叠了
- 我们可以利用这一点, 先对区间按照起点排序, 并维护一个列表 res 存储最终结果
- 然后从头开始遍历, 并动态更新 res
- 当遍历到某个区间时, 分为以下两种情况进行处理:
  1. 如果 res 中已经有一些区间, 且最后一个区间的终点大于等于当前区间起点, 则说明两者有重叠, 更新最后区间的终点为两者终点的较大值 (因为最后区间有可能完全包含了当前区间, 此时终点仍为原来的)
  2. 否则, 则说明当前区间与之前区间没有重叠, 将其追加到 res 中
- 最后, 遍历完的 res 即为所求
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(NlogN): 对区间排序的复杂度是 O(NlogN), 之后遍历区间的复杂度是 O(N), 综合起来的时间复杂度就是 O(NlogN)
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        res = []
        # 按照起点从小到大排序
        intervals.sort()
        for s, e in intervals:
            if res and res[-1][1] >= s:
                # 前一个区间与当前区间有重叠, 将其合并, 即更新终点为两者较大值
                res[-1][1] = max(e, res[-1][1])
            else:
                # 前一个区间与当前区间没有重叠, 追加当前区间
                res.append([s, e])
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
