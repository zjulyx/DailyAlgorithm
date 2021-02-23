> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

> 今天继续更新剑指 offer 系列, 这道题相比昨天那道题多了个每层打印方向不同的需求, 聪明的你想到应该如何实现了吗?

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

- 节点总数 <= 1000

## 题目样例

### 示例

#### 输入

给定二叉树: [3,9,20,null,null,15,7],

```
    3
   / \
  9  20
    /  \
   15   7
```

#### 输出

```
[
  [3],
  [20,9],
  [15,7]
]
```

## 题目思考

1. 可以继续利用昨天的方案吗, 不行的话需要进行哪些改动?

## 解决方案

### 思路

- 回顾昨天那道题 `剑指 Offer 32 - II. 从上到下打印二叉树 II - leetcode 剑指offer系列`, 我们是单独打印每一层的节点, 只不过都是从左到右的方向
- 针对这道题, 我们可以额外维护一个变量, 记录当前方向, 每次到下一层时就调换方向即可
- 也就是说, 只需要对昨天题目的代码稍加改动就能搞定, 所以熟练掌握前面两种 BFS 的模板是很有必要的, 很多问题都能在它们基础上解决

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N)`
  - 额外需要一个队列

#### 代码

```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        res = []
        if not root:
            return res
        q = [root]
        # 初始从左到右遍历
        fromleft = True
        while q:
            curlen = len(q)
            cur = []
            for node in q[:curlen]:
                cur.append(node.val)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            if fromleft:
                res.append(cur)
            else:
                # 从右向左的话只需要将该层的值翻转加入结果中即可
                res.append(cur[::-1])
            # 每一层结束后都调转方向
            fromleft = not fromleft
            q = q[curlen:]
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
