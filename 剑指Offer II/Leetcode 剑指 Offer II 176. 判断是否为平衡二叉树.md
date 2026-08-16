> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过 1，那么它就是一棵平衡二叉树。

### 示例 1：

- 输入：root = [3,9,20,null,null,15,7]
- 输出：true
- 解释：如下图

![](https://pic.leetcode.cn/1695102431-vbmWJn-image.png)

### 示例 2：

- 输入：root = [1,2,2,3,3,null,null,4,4]
- 输出：false
- 解释：如下图

![](https://pic.leetcode.cn/1695102434-WlaxCo-image.png)

### 提示：

- 0 <= 树的结点个数 <= 10000

## 题目思考

1. 可以只需要遍历一遍节点得到结果吗?

## 解决方案

- 分析题目, 一个比较容易想到的思路是: 先计算出每个节点的深度, 并将其存入`节点=>深度`字典中; 然后再遍历一遍节点, 针对每个节点, 判断它左右子节点的深度是否满足要求, 所有节点都满足的话才说明平衡. 但是这种方案需要遍历两边节点, 效率不太高, 如何一次性遍历得出结果呢?
- 回顾递归求深度的方案, 我们是先求得左右子树的深度, 然后才进一步得到当前节点的深度, 所以我们就可以直接加一个全局变量记录当前是否平衡, 并额外引入一个逻辑来比较子树深度, 如果不满足要求, 则直接把变量置为 false 直接返回即可
- **注意本题并不适用于 BFS 迭代求深度的算法**, 因为迭代方案求的是当前节点**从上到下**所在的层数, 每个节点并不知道自己的深度(从下往上, 从叶子节点到自身)究竟是多少, 所以无从判断是否平衡
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 需要遍历整个树
- 空间复杂度 O(H): H 表示树的高度, 也即递归的栈的消耗

#### 代码

```python
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        # 递归, 边求深度边判断, 返回深度, 全局变量标记当前是否平衡
        balance = True

        def getDepth(node):
            nonlocal balance
            if not node or not balance:
                # 递归出口: 如果节点为空或者不平衡, 返回0, 无需继续递归了
                return 0
            ldepth = getDepth(node.left)
            rdepth = getDepth(node.right)
            if abs(ldepth - rdepth) > 1:
                # 不平衡, 全局变量设为false
                balance = False
            # 返回当前节点自身的深度
            return max(ldepth, rdepth) + 1

        getDepth(root)
        return balance
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
