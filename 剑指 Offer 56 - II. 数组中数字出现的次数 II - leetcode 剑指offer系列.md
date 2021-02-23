> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

在一个数组 nums 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

- 1 <= nums.length <= 10000
- 1 <= nums[i] < 2^31

## 题目样例

### 示例

- 输入：nums = [3,4,3,3]
- 输出：4

- 输入：nums = [9,1,7,9,7,9,7]
- 输出：1

## 题目思考

1. 还能使用昨天的异或思路吗?
2. 可以单独统计每一位吗?

## 解决方案

#### 思路

- 分析题目, 其他数字都出现了三次, 这时候如果还用异或的话, 这些出现 3 次的数字并不能异或为 0, 而是异或成各自自身, 最终异或的结果相当于每个不同的数都异或了一次, 没有任何意义
- 换个角度分析, 如果我们**单独统计每个数字二进制每一位上为 1 的次数并累加**, 那么对于出现 3 次的数而言, 它们的这一位为 1 的次数之和一定是 3 的倍数
- 很显然, 加上了单独出现 1 次的数之后, 这一位上的次数除以 3 的余数要么是 0 (代表这个数字这一位是 0), 要么是 1(代表这个数字这一位是 1), 利用这一点, 我们就能计算出这个数字每一位上到底是 0 还是 1
- 看到这里, 结合昨天的[剑指 Offer 56 - I. 数组中数字出现的次数 - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484093&idx=1&sn=fd84ac3ad0bbe15bc5470097b8e08ff5&chksm=900285b0a7750ca62912a06a1d449ab5f44cf83cbbdf2d48093eac6b4553862cec0ab267eda4&token=208451789&lang=zh_CN#rd), 相信聪明的大家都能发现一些规律, 我在这里也总结一下:
  - 其他数字出现**偶数次**, 某个数字出现奇数次: 全部数字异或
  - 其他数字出现**偶数次**, 某两个数字各出现奇数次: 异或后根据异或结果的某个 1 分两类
  - 其他数字出现**奇数次**, 某个数字出现 1 次: 按照二进制每一位统计次数, 然后对这个奇数次取模
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 遍历一遍数组, 然后对于每个数要统计其每一位的次数, 这部分操作是`O(32) == O(1)`, 所以总体复杂度仍为 O(N)
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        res = 0
        for i in range(32):
            # 当前统计的位数为第i位
            # 假设i==1, 对应的mask就是0b000...00010 (共32位, 高位全为0)
            mask = 1 << i
            cnt = 0
            for x in nums:
                if x & mask:
                    # 如果这个数字与mask相与的结果为1, 则说明其当前位为1, 累加到次数中
                    cnt += 1
            if cnt % 3 == 1:
                # 如果次数为1, 则说明出现一次的数字在这一位上为1, 将最终结果或上当前mask即可
                res |= mask
                # 当然也可以选择结果加上mask, 因为每个mask都是只有一位为1且各不相同
                # res += mask
        return res
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
