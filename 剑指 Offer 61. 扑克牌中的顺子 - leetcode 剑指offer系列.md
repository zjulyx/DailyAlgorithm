> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

从扑克牌中随机抽 5 张牌，判断是不是一个顺子，即这 5 张牌是不是连续的。2 ～ 10 为数字本身，A 为 1，J 为 11，Q 为 12，K 为 13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

- 数组长度为 5

- 数组的数取值为 [0, 13] .

## 题目样例

### 示例

- 输入: [1,2,3,4,5]
- 输出: True

- 输入: [0,0,1,2,5]
- 输出: True

## 题目思考

1. 顺子需要满足什么条件?

## 解决方案

#### 思路

- 分析题目, 我们可以把数字分为两类, 0 和非 0 的数
- 对于非 0 的数而言, 显然首先它要不能重复, 其次最大最小值之差要小于 5, 否则中间一定会有空位
- 而对于 0 而言, 它可以变成任意数, 所以只要非 0 数的条件满足后, 直接用 0 来填充空位即可形成顺子
- 所以我们可以使用一个集合专门存储非 0 的数, 有重复的话直接返回 false, 最后再拿到非 0 数的最大最小值, 判断它们之差是否小于 5 即可

#### 复杂度

- 时间复杂度 O(1): 常数次判断
- 空间复杂度 O(1): 只使用了常数个变量

#### 代码

```python
class Solution:
    def isStraight(self, nums: List[int]) -> bool:
        # 使用集合判断非0数是否有重复
        # 然后判断非0数的最大最小值差值是否小于5
        v = set()
        # 初始化最大值和最小值, 有效的非0数一定大于或小于该初始值
        mn, mx = 14, 0
        for x in nums:
            if x != 0:
                if x in v:
                    # 发现重复非0数了, 当前序列无效
                    return False
                # 将非0数加入集合中
                v.add(x)
                # 求非0数的最大最小值
                mn = min(mn, x)
                mx = max(mx, x)
        return mx - mn < 5
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
