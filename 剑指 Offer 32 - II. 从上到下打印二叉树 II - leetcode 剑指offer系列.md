> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

> 今天继续更新剑指 offer 系列, 这道题相比昨天那道题多了个逐层打印的需求, 这样就不能沿用昨天的方案了, 因为它无法知道每层的边界

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

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
  [9,20],
  [15,7]
]
```

## 题目思考

1. 如何记录当前层的信息?

## 解决方案

### 思路

- 相比昨天那道题, 这里需要把每层节点都单独打印出来, 如果仍使用昨天的方法, 就无法知道每层的边界在哪, 所以需要做出一些改变
- 如果我们能够记录下当前层的节点边界, 然后当前层的子节点都加入队列后, 将队列更新为从下一层节点起点开始, 这样就确保每一层都单独被记下来了
- 这就是今天这道题的思路: 按照每层来循环, 存当前层的节点长度 curlen, 新追加的下层节点起点下标自然就是 curlen, 所以当前层遍历完之后只需要将队列切片成 curlen 及以后的部分即可
- 同样的, 由于是树, 每个节点只需要遍历一次, 所以不需要 visit 集合
- 下面的代码又是个人觉得比较通用的另一个 BFS 模板, 它适用于需要单独处理每一层节点的情况, 供大家参考

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
        while q:
            # 当前层长度
            curlen = len(q)
            curlevel = []
            for node in q[:curlen]:
                # 将当前层的节点值依次加入结果中
                curlevel.append(node.val)
                # 只追加非空子节点
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            res.append(curlevel)
            # 队列切片, 开始处理下一层
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
