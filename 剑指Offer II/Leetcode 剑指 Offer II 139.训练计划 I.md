> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

教练使用整数数组 actions 记录一系列核心肌群训练项目编号。为增强训练趣味性，需要将所有奇数编号训练项目调整至偶数编号训练项目之前。请将调整后的训练项目编号以 数组 形式返回。

### 示例 1：

- 输入：actions = [1,2,3,4,5]
- 输出：[1,3,5,2,4]
- 解释：为正确答案之一

### 提示：

- 0 <= actions.length <= 50000
- 0 <= actions[i] <= 10000

## 题目思考

1. 如何一次遍历搞定?
2. 可以不使用额外空间, 直接原地修改吗?

## 解决方案

### 方案 1

#### 思路

- 分析题目, 我们要使得奇数在偶数后面, 那么只要保证所有奇数下标都从 0 开始, 而偶数下标从末尾往前即可
- 所以一个很自然的思路就是新建一个数组, 长度和当前一样
- 然后维护两个下标代表新数组的奇数和偶数当前下标
- 最后遍历原数组:
  - 遇到奇数就放到新数组的奇数下标处, 同时下标右移
  - 遇到偶数就放到新数组的偶数下标处, 同时下标左移
- 下面代码完全基于上述分析实现, 必要步骤都有详细注释

#### 复杂度

- 时间复杂度 `O(N)`
  - 只需要遍历一遍数组
- 空间复杂度 `O(N)`
  - 额外需要一个数组存 N 个元素

#### 代码

```python
class Solution:
    def trainingPlan(self, actions: List[int]) -> List[int]:
        odd, even = 0, len(actions) - 1
        res = [0] * len(actions)
        for n in actions:
            if n & 1:
                # 当前是奇数, 存到新数组奇数下标处, 同时下标右移
                res[odd] = n
                odd += 1
            else:
                # 当前是偶数, 存到新数组偶数下标处, 同时下标左移
                res[even] = n
                even -= 1
        return res
```

### 方案 2

#### 思路

- 回顾方案 1, 我们需要额外使用一个数组保存新的数字, 那有没有可能原地修改呢?
- 答案也是可以的, 我们仍然借鉴方案 1 的思路, 只不过这次两个下标对应的是原数组的下标
- 其中左边的下标用于找当前往后第一个偶数, 而右边下标是从后往前第一个奇数, 这样只需要将它们两个交换位置, 就满足奇数在前偶数在后的要求
- 然后两下标继续往中间靠拢, 重复上述步骤即可
- 下面代码完全基于上述分析实现, 必要步骤都有详细注释

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个数字只需要被遍历一遍
- 空间复杂度 `O(1)`
  - 只需要几个变量

#### 代码

```python
class Solution:
    def trainingPlan(self, actions: List[int]) -> List[int]:
        i, j = 0, len(actions) - 1
        while i < j:
            while i < j and actions[i] & 1:
                # 如果当前本身就是奇数, 继续往后找
                i += 1
            while i < j and actions[j] & 1 == 0:
                # 如果当前本身就是偶数, 继续往前找
                j -= 1
            if i < j:
                # 找到一对不满足条件的奇偶数, 交换它们位置
                actions[i], actions[j] = actions[j], actions[i]
            i += 1
            j -= 1
        return actions
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
