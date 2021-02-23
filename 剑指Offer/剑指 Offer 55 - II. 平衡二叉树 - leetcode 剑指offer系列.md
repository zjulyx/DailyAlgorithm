> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过 1，那么它就是一棵平衡二叉树。

- 1 <= 树的结点个数 <= 10000

## 题目样例

### 示例

```
给定二叉树 [3,9,20,null,null,15,7]

    3
   / \
  9  20
    /  \
   15   7
返回 true 。
```

```
给定二叉树 [1,2,2,3,3,null,null,4,4]

       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
返回 false 。
```

## 题目思考

1. 可以只需要遍历一遍节点得到结果吗?

## 解决方案

#### 思路

- 有了昨天[剑指 Offer 55 - I. 二叉树的深度 - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484091&idx=1&sn=b8d57ddb5c325b7fceeb7dcb8dc2ece3&chksm=900285b6a7750ca0c3e66511a8b5f4bde47c7dff7e863f7e67120db2a28784fdc72f939ebe70&token=1230510346&lang=zh_CN#rd)的铺垫, 这道题相信大家都可以迎刃而解
- 一个比较容易想到的思路是: 先计算出每个节点的深度, 并将其存入`节点=>深度`字典中; 然后再遍历一遍节点, 针对每个节点, 判断它左右子节点的深度是否满足要求, 所有节点都满足的话才说明平衡. 但是这种方案需要遍历两边节点, 效率不太高, 如何一次性遍历得出结果呢?
- 回顾递归求深度的方案, 我们是先求得左右子树的深度, 然后才进一步得到当前节点的深度, 所以我们就可以直接加一个全局变量记录当前是否平衡, 并额外引入一个逻辑来比较子树深度, 如果不满足要求, 则直接把变量置为 false 直接返回即可
- **注意本题并不适用于 BFS 迭代求深度的算法**, 因为迭代方案求的是当前节点**从上到下**所在的层数, 每个节点并不知道自己的深度(从下往上, 从叶子节点到自身)究竟是多少, 所以无从判断是否平衡
- 下面代码对必要的步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 需要遍历整个树
- 空间复杂度 O(H): H 表示树的高度, 也即递归的栈的消耗

#### 代码

```python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
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

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
