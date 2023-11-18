> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/opLdQZ/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个二叉搜索树的 根节点 root 和一个整数 k , 请判断该二叉搜索树中是否存在两个节点它们的值之和等于 k 。假设二叉搜索树中节点的值均唯一。

### 示例 1：

- 输入: root = [8,6,10,5,7,9,11], k = 12
- 输出: true
- 解释: 节点 5 和节点 7 之和等于 12

### 示例 2：

- 输入: root = [8,6,10,5,7,9,11], k = 22
- 输出: false
- 解释: 不存在两个节点值之和为 22 的节点

### 提示：

- 二叉树的节点个数的范围是 [1, 10^4].
- -10^4 <= Node.val <= 10^4
- root 为二叉搜索树
- -10^5 <= k <= 10^5

## 题目思考

1. 如何利用二叉搜索树的性质?

## 解决方案

#### 思路

- 分析题目, 一个很容易想到的思路就是中序遍历, 将二叉搜索树转换成一个有序数组, 存储递增的值
- 这样就把问题转换成了经典的有序数组两数之和问题, 之前我们也做过类似的题目[剑指 Offer 57. 和为 s 的两个数字](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484095&idx=1&sn=984ebd48c6aa4064d57989547a87be97&token=493419333&lang=zh_CN#rd), 为了方便起见, 我把对应的思路也贴在这里了:
- 将两个下标 i 和 j 初始化为数组的头和尾, 然后往中间靠拢
- 根据当前的和, 具体分为以下三种情况:
  1. `nums[i] + nums[j] == k`: 找到一对满足条件的数字了, 直接返回 true
  2. `nums[i] + nums[j] < k`: 当前和小于 k, 因为数组有序, 如果保留 nums[i], 而 j 继续往左的话, 新的和肯定更小于 k, 所以 nums[i]可以被安全排除, 即 i 直接加 1
  3. `nums[i] + nums[j] > k`: 当前和大于 k, 因为数组有序, 如果保留 nums[j], 而 i 继续往右的话, 新的和肯定更大于 k, 所以 nums[j]可以被安全排除, 即 j 直接减 1
- 这样遍历下去最终肯定 i 和 j 会相遇, 此时退出循环, 说明没找到满足条件的数字对, 返回 false 即可
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 中序遍历每个节点一遍, 双指针遍历每个值一遍
- 空间复杂度 O(N): 额外有序数组存储所有节点的值

#### 代码

```python
class Solution:
    def findTarget(self, root: TreeNode, k: int) -> bool:
        # 中序遍历+双指针
        # 先用中序遍历将二叉搜索树转换成有序数组
        nums = []

        def inorder(node):
            if not node:
                return
            inorder(node.left)
            nums.append(node.val)
            inorder(node.right)

        inorder(root)
        # 然后对有序数组进行双指针遍历, 检查是否存在和为k的数字对
        i, j = 0, len(nums) - 1
        while i < j:
            sm = nums[i] + nums[j]
            if sm == k:
                return True
            elif sm < k:
                i += 1
            else:
                j -= 1
        return False
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
