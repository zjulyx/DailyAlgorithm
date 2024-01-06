> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/g5c51o/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个整数数组 nums 和一个整数 k ，请返回其中出现频率前 k 高的元素。可以按 任意顺序 返回答案。

### 示例 1:

- 输入: nums = [1,1,1,2,2,3], k = 2
- 输出: [1,2]

### 示例 2:

- 输入: nums = [1], k = 1
- 输出: [1]

### 提示：

- 1 <= nums.length <= 10^5
- k 的取值范围是 [1, 数组中不相同的元素的个数]
- 题目数据保证答案唯一，换句话说，数组中前 k 个高频元素的集合是唯一的

- 进阶：所设计算法的时间复杂度 必须 优于 O(n log n) ，其中 n 是数组大小。

## 题目思考

1. 可以使用什么数据结构?

## 解决方案

#### 思路

- 分析题目, 我们需要做两件事情: 1. 统计元素的出现频率; 2. 获取出现频率前 k 高的元素
- 对于第一个任务, 我们可以使用一个计数字典来统计
- 对于第二个任务, 不难发现它和上一道题目[Leetcode 剑指 Offer II 059. 数据流中的第 K 大元素](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484722&idx=1&sn=ca9f648d8db7750f332f2685f3244a49&token=1962459982&lang=zh_CN#rd)非常类似, 只是把第 k 大改成了前 k 大
- 所以我们仍然可以沿用相同的思路, 只需要稍作改动:
  - 维护一个最小堆存储(元素频率, 元素)
  - 然后遍历计数字典的键值对, 键就是元素, 而值就是其频率
  - 将(元素频率, 元素)直接加入堆中, 加入后如果堆中元素超过了 k, 就把堆顶弹出
  - 由于是最小堆, 在遍历结束时, 堆中元素正是出现频率最高的 k 个元素及其频率, 更低的都被弹出去了
  - 我们只需要提取出来每个元素, 舍弃其频率, 即为最终结果
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(nlogk): 统计每个元素出现频率是 O(n), 而获取出现频率前 k 高的元素需要遍历整个数组, 每次添加或弹出都是操作最多 k 个元素的最小堆, 所以整体是 O(nlogk)
- 空间复杂度 O(n): 计数字典最多存储 n 个元素, 最小堆最多存储 k 个元素, n>=k, 所以整体是 O(n)

#### 代码

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        # 计数字典+最小堆
        cnts = collections.Counter(nums)
        q = []
        for num, cnt in cnts.items():
            # 将当前[元素频率, 元素]加入最小堆中
            heapq.heappush(q, [cnt, num])
            if len(q) > k:
                # 如果最小堆个数超过了k, 就把堆顶弹出
                heapq.heappop(q)
        # 最终结果提取出堆中[元素频率, 元素]元组的元素部分
        return [num for _, num in q]
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
