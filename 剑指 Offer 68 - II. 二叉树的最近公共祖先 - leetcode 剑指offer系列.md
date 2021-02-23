> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

> 本篇是剑指 offer 系列的最后一篇了, 之后我会做一个总结, 对之前的文章进行归类汇总, 希望这个系列对大家能有所帮助, 也欢迎大家多多分享和转发, 谢谢支持~ 😘

## 题目描述

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树: root = [3,5,1,6,2,0,8,null,null,7,4]

![二叉树](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/binarytree.png)

说明:

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉树中。

## 题目样例

### 示例

- **示例 1:**

- 输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
- 输出: 3
- 解释: 节点 5 和节点 1 的最近公共祖先是节点 3。

- **示例 2:**

- 输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
- 输出: 5
- 解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。

## 题目思考

1. 还可以利用昨天二叉搜索树的方法吗?
2. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 相比昨天的题目 [剑指 Offer 68 - I. 二叉搜索树的最近公共祖先 - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484126&idx=1&sn=24e51dd1e97e65c1ffe3ba8860b97c04&chksm=900285d3a7750cc57ca0d97a9997bcd5f7a5b17c1e0f4bd1f9492ad4f104aac2bf4fdf4c2deb&token=1933556045&lang=zh_CN#rd), 这里的树不再是二叉搜索树, 也就意味着不能根据节点值来判断应该往那边遍历, 只能老老实实逐个节点遍历了
- 首先尝试递归的做法, 如果我们能得出左右子树各自的 p/q 节点存在的情况, 再结合当前节点, 就能得出当前节点以及其所有子节点中是否包含 p 和 q
- 那么在**第一次**找到这样一个自身或子节点中同时包含 p 和 q 的节点时, 这个节点就一定是最近公共祖先, 因为其所有子节点都不满足这一性质, 不可能有更近的祖先了
- 接下来考虑如何实现:
  1. **首先定义待求的祖先节点:** 初始化为空, 表示还未找到
  2. **接下来定义递归方法:** 根据上面的分析, 递归方法中需要传入当前节点, 并返回两个 bool, 分别代表当前节点以及其所有子节点中是否包含 p 或 q
  3. **第三步定义递归出口:** 很显然是节点为空或者祖先节点已经找到的情况, 此时直接返回
  4. **最后设计正常情况下的递归逻辑:** 我们依次递归调用左右子节点并得到它们 p/q 节点存在的情况, 并考虑当前节点本身是否为 p/q, 从而得到当前的两个 bool 返回值. 如果它们都是 true**且当前祖先为空**的话, 就把祖先设为当前节点, 即找到了最近祖先. (因为最近祖先的更高层的父节点的两个 bool 肯定也是 true, 所以必须判断当前祖先为空才赋值, 保证祖先是最近的)
- 下面代码对必要步骤有详细解释, 方便大家参考

#### 复杂度

- 时间复杂度 O(N): 最多需要遍历每个节点一遍
- 空间复杂度 O(H): 递归栈中要存树的高度个节点

#### 代码

```python
class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode,
                             q: TreeNode) -> TreeNode:
        # 方法1: 递归dfs, 返回是否找到p或q
        # 注意ancestor只能赋值一次, 即为最近的祖先
        ancestor = None

        def find(cur):
            nonlocal ancestor
            if not cur or ancestor:
                # 递归出口, 返回全false
                return (False, False)
            # 先得出左右子树上的p/q节点存在情况
            lp, pq = find(cur.left)
            rp, rq = find(cur.right)
            # 然后计算加上当前节点后的p/q节点存在情况
            findp = lp or rp or cur == p
            findq = pq or rq or cur == q
            # 如果两者都存在, 且祖先还为空, 则表示找到最近祖先了, 即当前节点
            if findp and findq and not ancestor:
                ancestor = cur
            # 返回当前节点以及其所有子节点中的p/q节点存在情况
            return (findp, findq)

        find(root)
        return ancestor
```

### 方案 2

#### 思路

- 假如此时要求只能利用迭代来实现, 又该如何做呢?
- 一个很自然的思路是我们从两个节点出发, 往上走, 并把路上的节点都加入集合中
- 然后某个节点如果在加入前已经在集合里了, 就说明这个节点一定也存在于另一条路径中, 也即这个节点是个祖先, 而且显然**第一次遇到的这种节点就是最近祖先**
- 如何实现向上走呢? 我们可以定义一个 parent 字典, key 是当前节点, value 是其父节点, 然后通过将当前节点置为其父节点的方式一直向上走了, 直到到根节点
- 那如何求 parent 字典呢? DFS 和 BFS 都很好实现, 因为它们都有当前节点和子节点的信息, 这里要求迭代, 那就直接用 BFS 好了, 不熟悉的同学可以参考[剑指 Offer 32 - I. 从上到下打印二叉树 - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484054&idx=1&sn=8808bb46a7dcaf740ada3fb9a7d3d7e6&chksm=9002859ba7750c8d743432481f0f12b68c3a60ca96a8d1c3b07b1aab6208c42d8f5f03c135f0#rd)以及[剑指 Offer 32 - II. 从上到下打印二叉树 II - leetcode 剑指 offer 系列](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484055&idx=1&sn=eb46c80176a468ea276ea3746978b501&chksm=9002859aa7750c8c0a010b5df67f411b538d791d5600946773fae1dcecefe3a96e46c115c4f4#rd), 里面有最基本的几种 BFS 的思路和实现方法
- 下面代码对必要步骤有详细解释, 方便大家参考

#### 复杂度

- 时间复杂度 O(N): 最多需要遍历每个节点一遍
- 空间复杂度 O(N): 需要存每个节点的父节点信息, 以及一个集合存当前遍历过的节点

#### 代码

```python
class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode,
                             q: TreeNode) -> TreeNode:
        # 方法2: 迭代BFS, 记录每个节点父节点, p/q依次向上找并加入集合中, 直到找到第一个父节点已在集合的节点, 这个父节点就是最近公共祖先
        if not root:
            return None
        # 初始化根节点的父节点为空
        parent = {root: None}
        # BFS部分, 记录每个节点父节点
        queue = [root]
        for node in queue:
            if node.left:
                parent[node.left] = node
                queue.append(node.left)
            if node.right:
                parent[node.right] = node
                queue.append(node.right)
        # 注意集合需要初始化加上p/q, 因为它们可能自身就是祖先
        v = {p, q}
        while p or q:
            # 这里循环条件为or, 因为有可能p/q高度可能不同, 有可能某个节点先遍历到了根节点, 这时候另外的节点需要继续往上走来找祖先
            if p:
                p = parent[p]
                if p and p in v:
                    # 当前父节点不是空且已经在集合中, 说明它就是最近祖先
                    return p
                v.add(p)
            if q:
                q = parent[q]
                if q and q in v:
                    # 当前父节点不是空且已经在集合中, 说明它就是最近祖先
                    return q
                v.add(q)
        # 最终一定会在循环内部返回的, 所以循环外不需要return, 因为保底祖先是根节点, 一定在集合中
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
