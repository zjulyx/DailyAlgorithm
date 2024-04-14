> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/0H97ZC/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定两个数组，arr1 和 arr2，

- arr2 中的元素各不相同
- arr2 中的每个元素都出现在 arr1 中
- 对 arr1 中的元素进行排序，使 arr1 中项的相对顺序和 arr2 中的相对顺序相同。未在 arr2 中出现过的元素需要按照升序放在 arr1 的末尾。

### 示例：

- 输入：arr1 = [2,3,1,3,2,4,6,7,9,2,19], arr2 = [2,1,4,3,9,6]
- 输出：[2,2,2,1,4,3,3,9,6,7,19]

### 提示：

- 1 <= arr1.length, arr2.length <= 1000
- 0 <= arr1[i], arr2[i] <= 1000
- arr2 中的元素 arr2[i] 各不相同
- arr2 中的每个元素 arr2[i] 都出现在 arr1 中

## 题目思考

1. 如何模拟整个过程?

## 解决方案

#### 思路

- 分析题目, 不难发现需要分两部分进行排序:
  1. 对于 arr1 在 arr2 中的数字, 要按照 arr2 的数字出现顺序来排序
  2. 对于 arr1 的其他数字, 需要按照升序排序
- 我们可以使用一个计数字典统计 arr1 每个数字的出现次数, 然后模拟上述两个过程
- 首先遍历 arr2 的每个数字, 将 arr1 中所有的该数字作为子数组, 添加到最终结果中
  - 举个例子, 假如 arr2 第一个数字是 5, arr1 的 5 出现了 3 次, 那么就是添加`[5,5,5]`
  - 而如果该数字没在 arr1 中, 那自然是什么都不添加
  - 另外还需要将该数字从计数字典中移除, 避免重复添加
- 然后对计数字典的剩余数字按升序排序, 同样将 arr1 中所有的该数字作为子数组, 依次添加到最终结果
- 上面两步结束后, 得到的数组就是按照上述规则排序后的结果了, 直接返回即可
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N1logN1+N2): 假设 arr1 和 arr2 的元素数目分别是 N1 和 N2, 首先记录 arr1 每个数字的出现次数是 O(N1), 之后遍历 arr2 的复杂度是 O(N2), 最后剩余部分的排序是 O(N1logN1), 综合起来的时间复杂度就是 O(N1logN1+N2)
- 空间复杂度 O(N1): 使用了额外计数字典存储 arr1 的元素

#### 代码

```python
class Solution:
    def relativeSortArray(self, arr1: List[int], arr2: List[int]) -> List[int]:
        # 记录arr1每个数字的出现次数
        cnts = collections.Counter(arr1)
        res = []
        for x in arr2:
            # 先按照arr2的顺序依次添加数字
            res += [x] * cnts[x]
            # 注意用过之后要删除
            del cnts[x]
        for x in sorted(cnts):
            # 剩余数字按照升序排列
            res += [x] * cnts[x]
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
