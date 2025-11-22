> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

一棵圣诞树记作根节点为 root 的二叉树，节点值为该位置装饰彩灯的颜色编号。请按照如下规则记录彩灯装饰结果：

第一层按照从左到右的顺序记录
除第一层外每一层的记录顺序均与上一层相反。即第一层为从左到右，第二层为从右到左。

### 示例 1：

![](https://pic.leetcode.cn/1694758674-XYrUiV-%E5%89%91%E6%8C%87%20Offer%2032%20-%20I_%E7%A4%BA%E4%BE%8B1.png)

- 输入：root = [8,17,21,18,null,null,6]
- 输出：[[8],[21,17],[18,6]]

### 提示：

- 节点总数 <= 1000

## 题目思考

1. 可以继续利用上周的方案吗, 不行的话需要进行哪些改动?

## 解决方案

### 思路

- 回顾上周那道题 `Leetcode 剑指 Offer II 150.彩灯装饰记录 II`, 我们是单独打印每一层的节点, 只不过都是从左到右的方向
- 针对这道题, 我们可以额外维护一个变量, 记录当前方向, 每次到下一层时就调换方向即可
- 也就是说, 只需要对上周题目的代码稍加改动就能搞定, 所以熟练掌握前面两种 BFS 的模板是很有必要的, 很多问题都能在它们基础上解决

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N)`
  - 额外需要一个队列

#### 代码

```python
class Solution:
    def decorateRecord(self, root: Optional[TreeNode]) -> List[List[int]]:
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

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
