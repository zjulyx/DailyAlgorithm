> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

一棵圣诞树记作根节点为 root 的二叉树，节点值为该位置装饰彩灯的颜色编号。请按照从 左 到 右 的顺序返回每一层彩灯编号。

### 示例 1：

![](https://pic.leetcode.cn/1694758674-XYrUiV-%E5%89%91%E6%8C%87%20Offer%2032%20-%20I_%E7%A4%BA%E4%BE%8B1.png)

- 输入：root = [8,17,21,18,null,null,6]
- 输出：[8,17,21,18,6]

### 提示：

- 节点总数 <= 1000

## 题目思考

1. 哪些数据结构可以做到按层输出?

## 解决方案

### 思路

- 经典的 BFS 思路, 将当前节点的左右非空子节点追加到队列结尾, 然后继续往后遍历队列, 最终整个队列就是最后的结果
- 由于这里是树, 所以每个节点只可能被加入队列访问一次, 无需额外的 visit 集合
- 另外这道题不需要分开保存每一层的节点, 所以这里不需要记录当前层的长度之类的情况, 可以直接一次循环搞定
- 数据结构方面, 可以采用列表 + 动态的 for 循环(python 3 适用, 其他语言可能不支持遍历过程中更改容器)或者双端队列 + while 循环(所有语言通用, 每次 pop 最左边的元素作为当前节点)
- 下面的代码是个人觉得比较通用的 BFS 模板, 包括列表和双端队列两种方式的实现, 适用于不需要单独处理每一层节点的情况, 供大家参考

### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N)`
  - 额外需要一个列表/双端队列

### 代码

#### 方案 1 - 列表 + for 循环

```python
class Solution:
    def decorateRecord(self, root: Optional[TreeNode]) -> List[int]:
        # 方案1: 列表+for循环
        if not root:
            return []
        q = [root]
        for node in q:
            # 只将非空子节点追加到队列
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        return [x.val for x in q]
```

#### 方案 2 - 双端队列 + while 循环

```python
import collections

class Solution:
    def decorateRecord(self, root: Optional[TreeNode]) -> List[int]:
        # 方案2: 双端队列+while循环
        if not root:
            return []
        q = collections.deque([root])
        res = []
        while q:
            node = q.popleft()
            res.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
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
