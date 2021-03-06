> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

> 今天继续更新剑指 offer 系列, 这道题能帮助我们更好地理解二分查找, 很值得一做. 另外基于它还能解决一些进阶问题, 例如[面试题 10.03. 搜索旋转数组](https://leetcode-cn.com/problems/search-rotate-array-lcci/), 在最后面我也会说下那道题的思路, 也有对应的题解链接, 感兴趣的同学也可以自己试试哦

> 若无意外, 每天**晚上 6 点 45 分**准时更新, 中间可能会穿插一些周赛题解. 大家在我的公众号"每日精选算法题"中的聊天框中回复 **offer** 就能看到该系列当前已经更新的文章了

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组  `[3,4,5,1,2]` 为 `[1,2,3,4,5]` 的一个旋转，该数组的最小值为 1。

## 题目样例

### 示例 1

#### 输入

[3,4,5,1,2]

#### 输出

1

### 示例 2

#### 输入

[2,2,2,0,1]

#### 输出

0

## 题目思考

1. 如果利用原始数组有序这一条件?

## 解决方案

### 思路

#### 分析

- 直接利用 min 或者遍历一遍求最小值也能得到结果, 但这完全没有用上原始数组有序这一条件, 所以也不是这道题目考察的本意所在
- 而数组有序一般就可以尝试二分查找来加速运算, 这道题也不例外
- 因为进行过旋转, 所以数组最多可能会有两段递增的区间, 而它们的分界点就是旋转点(当然也有可能旋转 0 个数字, 那就是自身一段递增区间)
- 基于经典二分查找, 考虑中间下标 m 对应的数字:
  1. 如果中间值小于末尾, 那么一定说明该数字之后(后半段)有序.
     - 这里可以用反证法证明: 假设此时后半段无序, 意味着分界点就在后半段, 那么前半段一定有序, 也即**中间值大于等于开头**, 而分界点又是原数组起点, 它在后半段, 这就意味着**开头一定大于等于末尾**, 综合起来就是**中间值大于等于末尾**, 正好与前提矛盾.
     - 后半段有序, 那么最小值一定是中间值或者更前面, 所以**二分进入前半段即可, 注意需要保留 m 作为候选项**
  2. 如果中间值大于末尾, 那么毫无疑问后半段无序.
     - 后半段无序, 那么一定会排除前半段. 因为根据上面的分析, 末尾此时一定小于等于开头, 开头到中间的部分都大于等于末尾, 所以**二分进入右半段即可, 这里不需要保留 m 作为候选项, 因为末尾一定小于它**
  3. 如果中间值等于末尾, 那就不好判断是前半段无序还是后半段无序了, 举两个例子大家就应该很清楚了: 前半段无序 `[2,1,2,2,2]`; 后半段无序 `[2,2,2,1,2]`
     - 这种情况由于无法判断有序, 只能退化成逐个遍历, **末尾-1 即可**. 注意此处不能是开头+1, 因为并不确定开头和中间值的关系, 有可能开头是最小值
- 根据上述三种情况, 每次区间缩小都会保留最小值作为候选项, 所以最后收敛到的值一定是最小值

#### 实现

- 经典二分查找的变种
- 定义开头下标和结尾下标 s 和 e
- 计算中间下标 m, 根据中间值与末尾值的关系来二分区间, 具体关系和上面分析完全一致
- 下面代码对必要步骤都有解释, 方便大家理解

### 复杂度

- 时间复杂度 `O(logN)~O(N)`
  - 大部分情况下 O(logN) 时间就能二分出结果, 最差情况会退化成`O(N)` (数组元素都相等时)
- 空间复杂度 `O(1)`
  - 只需要几个变量即可

### 代码

```python
class Solution:
    def minArray(self, numbers: List[int]) -> int:
        # 简单二分, 当m=e时无脑直接e-1...
        # 最后返回跳出循环后的numbers[s]
        # 注意m<e时要把e设为m, 因为最小值可能就是m
        s, e = 0, len(numbers) - 1
        while s < e:
            m = (s + e) >> 1
            if numbers[m] < numbers[e]:
                # m可能是最小值, 不能排除它
                e = m
            elif numbers[m] > numbers[e]:
                # m一定不是最小值, 排除它
                s = m + 1
            else:
                # 退化的情况
                e -= 1
        return numbers[s]
```

## 进阶问题思路

- 看到这里, 相信大家已经掌握了求旋转数组中最小数字的方法
- 对于进阶问题, 它不是求旋转数组最小数字, 而是求目标数字在里面的最小下标, 这里为了方便大家查看, 我先把那道题的题目贴出来

> 搜索旋转数组。给定一个排序后的数组，包含 n 个整数，但这个数组已被旋转过很多次了，次数不详。请编写代码找出数组中的某个元素，假设数组元素原先是按升序排列的。若有多个相同元素，返回索引值最小的一个。

- 注意进阶问题里说的是旋转多次, 但其实**旋转一次和旋转多次没有任何区别, 最终还是只有一个旋转点, 以及不多于 2 个的有序区间**
- 基于这一点, 我们就可以在这两个有序区间内利用经典二分查找来找目标值, 所以问题就转化为了如何找分界点
- 而**分界点一定是最小数字, 且其上一个值要大于它**. 这样就可以用本题的思路, 判断 e 是不是分界点即可: 是的话直接退出循环, 否则继续二分找包含最小数字的区间.
  - **注意**: 这里必须用 e 来判断分界点, 是因为用 m 的话可能会在退化情况时漏掉正确分界点, 例如`[2,2,2,1,2]`这个例子, 分界点是 1, 但是 m 始终不会达到 1, 而 e 则总能
- 另外注意如果前半段找到了就没必要找后半段了, 因为要求的是最小下标
- 对于这个进阶问题, 我也在 leetcode 上发布了一篇题解: [进阶问题题解](https://leetcode-cn.com/problems/search-rotate-array-lcci/solution/fen-cheng-liang-duan-you-xu-shu-zu-jin-xing-er-fen/), 里面有实现的代码, 感兴趣的同学也可以去看看~

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
