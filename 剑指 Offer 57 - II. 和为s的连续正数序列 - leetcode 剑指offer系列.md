> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

- 1 <= target <= 10^5

## 题目样例

### 示例

- 输入：target = 9
- 输出：[[2,3,4],[4,5]]

- 输入：target = 15
- 输出：[[1,2,3,4,5],[4,5,6],[7,8]]

## 题目思考

1. 满足条件的序列长度有什么规律?
2. 时间复杂度可以小于 O(target)吗?

## 解决方案

### 方案 1

#### 思路

- 一个比较容易想到的思路是利用滑动窗口, 维护起点和终点以及当前窗口的和
- 然后根据当前窗口和与 target 的关系来决定起点和终点的移动
  - `窗口和 > target`: 起点右移
  - `窗口和 = target`: 起点右移, 并将当前起点终点加入结果集中
  - `窗口和 < target`: 终点右移
- 注意终点上限是`target//2+1`, 对应长度为 2 的情况
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(target): 终点至少需要遍历到`target//2+1`
- 空间复杂度 O(1): 只使用了几个变量(结果数组使用的空间不计入内)

#### 代码

```python
class Solution:
    def findContinuousSequence(self, target: int) -> List[List[int]]:
        # 方法1: 滑动窗口, 维护起点/终点/当前窗口和, 根据窗口和与target的关系进行移动, 终点最多只需要到target // 2 + 1
        # 初始化起点和窗口和均为1
        l = 1
        sm = 1
        res = []
        for r in range(2, target // 2 + 2):
            # 注意终点需要从2开始, 因为题目要求序列至少有2个数
            # 加入当前终点到窗口和中
            sm += r
            while sm > target:
                # 当前窗口的和大于target, 窗口和减去当前起点, 起点右移
                sm -= l
                l += 1
            if sm == target:
                # 找到可行解了, 加入结果集中
                res.append(list(range(l, r + 1)))
                # 同时要将窗口和减去当前起点, 起点右移, 因为不可能再有满足条件的窗口是以当前l为起点了, 后面的窗口和只可能更大
                sm -= l
                l += 1
        return res
```

### 方案 2

#### 思路

- 回顾方案 1, 它的时间复杂度达到了 O(target), 还有更优的方案吗?
- 答案是有的, 我们可以尝试找序列和与对应长度的规律, 利用一波小学/初中数学知识 😂
  - 对于序列长度为奇数的情况, 设其长度为 le, 中间值为 m, 那么 target 一定为 `m*le`, 例如 `15=1+2+3+4+5=3*5`
  - 对于连续正整数为偶数的情况, 设其长度为 le, 两个中间值为 m 和 m+1, 那么 target 一定为`(m+0.5)*le`, 例如 `10=1+2+3+4=2.5*4`
- 分析出来这个就很简单了, 具体步骤如下 (注意下面公式中的`//`表示整除, `/`表示浮点数除法, 对应 Python3 的语法):
  1. 按照长度 le 从 2 开始遍历, 循环条件是`le*(le+1)//2 <= target`
     - 因为从 1 开始长度为 le 的连续整数之和为 `le*(le+1)//2`, 这是最小的和了, 它必须小于等于 target. 因为如果它都大于 target 的话, 后面以更大值为起点且长度为 le 的序列和肯定更大于 target
  2. 当前长度为奇数时, 如果其能被 target 整除, 则说明当前长度可行. 中间值 m 是 `target//le`, 前面还有`le//2`个数, 所以起点自然是`target//le-le//2`
  3. 当前长度为偶数时, 如果能保证 `target/le` 得到一个.5 的小数, 则说明当前长度可行, 也就是检查 `target/le` 和 `target//le` 的差是否是 0.5 即可. 左边中间值 m 是 `target//le`, 前面还有`le//2-1`个数, 所以起点自然是`target//le-le//2+1`
- 根据上述分析得到了起点和长度, 就能将对应的数组加入结果中
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(sqrt(target)): 根据循环条件, 只需要遍历 `sqrt(2*target)` 个数
- 空间复杂度 O(1): 只使用了几个变量(结果数组使用的空间不计入内)

#### 代码

```python
class Solution:
    def findContinuousSequence(self, target: int) -> List[List[int]]:
        # 方法2: 找规律, 枚举长度le, 判断target能否由该长度的数组求和所得, 注意le上限要满足le * (le + 1) // 2 <= target
        le = 2
        res = []
        while le * (le + 1) // 2 <= target:
            start = None
            if le & 1 and target % le == 0:
                # 长度为奇数且满足条件的情况
                # 中点是target // le, 前面有le // 2个数
                # 所以起点是target // le - le // 2
                start = target // le - le // 2
            elif le & 1 == 0 and target / le - target // le == 0.5:
                # 长度为偶数且满足条件的情况
                # 左侧中点是target // le, 前面有le // 2 - 1个数
                # 所以起点是target // le - le // 2 + 1
                start = target // le - le // 2 + 1
            if start is not None:
                # 找到了合适的起点和长度, 将其加入结果中
                res.append(list(range(start, start + le)))
            le += 1
        # 注意长度是从小到大遍历的, 自然对应的起点是从大到小, 所以最后结果集需要倒置
        return res[::-1]
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
