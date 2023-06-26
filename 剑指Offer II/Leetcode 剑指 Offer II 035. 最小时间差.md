> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/569nqc/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个 24 小时制（小时:分钟 "HH:MM"）的时间列表，找出列表中任意两个时间的最小时间差并以分钟数表示。

### 示例 1：

- 输入：timePoints = ["23:59","00:00"]
- 输出：1

### 示例 2：

- 输入：timePoints = ["00:00","23:59","00:00"]
- 输出：0

### 提示：

- 2 <= timePoints <= 2 \* 10^4
- timePoints[i] 格式为 "HH:MM"

## 题目思考

1. 如何快速得到两个时间的时间差?
2. 如何进一步优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 要想求最小时间差, 最直观的做法就是将时间从小到大排序, 然后依次获得相邻两个时间的差值, 从中取最小的即可
- 不过这里需要额外加上一种情况: 某个时间与下一天另一时间的时间差, 如示例 1 那样
- 由于我们已经将时间排好序了, 所以只需要额外考虑最后一个时间与下一天第一个时间的时间差, 这是这种情况下的最小值
- 接下来考虑如何快速得到时间差, 我们可以将时间转换成相比 0 点 0 分的分钟数, 这样相邻两个时间的分钟数直接相减即可
- 至于首尾时间差, 只需要将第一个时间加上 24\*60, 即可得到下一天相同时刻的分钟数, 再减去末尾时间的分钟数即可
- 最后我们可以进一步对时间复杂度进行优化, 因为一天最多只有 24\*60 个时刻, 所以如果给定时间列表的长度超过这一值, 那么根据鸽巢原理, 一定存在重复的两个时刻, 此时最小时间差是 0, 直接返回即可
- 下面的代码就对应了上面的整个过程, 并且有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(min(N,C)\*log(min(N,C))): N 是时间列表长度, C 是常数(24\*60), 最多需要排序的时间个数是 min(N,C)
- 空间复杂度 O(1): 只使用了几个常数空间的变量

#### 代码

```python
class Solution:
    def findMinDifference(self, timePoints: List[str]) -> int:
        # 鸽巢原理+排序+注意首尾差
        if len(timePoints) > 24 * 60:
            # 时间数目超过了24小时的时间总数, 必然有重复的时间, 最小时间差为0, 直接返回
            return 0

        def getMinute(t):
            # 将时间字符串转成相比0点0分的分钟数
            h, m = t.split(":")
            return 60 * int(h) + int(m)

        # 按照时间从小到大排序
        timePoints.sort()
        first = getMinute(timePoints[0])
        last = getMinute(timePoints[-1])
        # 最终结果初始化为首尾两个时间的差, 需要额外加上一天
        res = 24 * 60 + first - last
        pre = first
        for t in timePoints[1:]:
            # 统计相邻两个时间的时间差
            cur = getMinute(t)
            # 更新较小值到最终结果中
            res = min(res, cur - pre)
            pre = cur
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
