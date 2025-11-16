> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

一棵圣诞树记作根节点为 root 的二叉树，节点值为该位置装饰彩灯的颜色编号。请按照从左到右的顺序返回每一层彩灯编号，每一层的结果记录于一行。

### 示例 1：

![](https://pic.leetcode.cn/1694758674-XYrUiV-%E5%89%91%E6%8C%87%20Offer%2032%20-%20I_%E7%A4%BA%E4%BE%8B1.png)

- 输入：root = [8,17,21,18,null,null,6]
- 输出：[[8],[17,21],[18,6]]

### 提示：

- 节点总数 <= 1000

## 题目思考

1. 如何记录当前层的信息?

## 解决方案

### 思路

- 相比上周那道题, 这里需要把每层节点都单独打印出来, 如果仍使用上周的方法, 就无法知道每层的边界在哪, 所以需要做出一些改变
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
    def decorateRecord(self, root: TreeNode) -> List[List[int]]:
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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
