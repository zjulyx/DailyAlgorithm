> 题目难度: 简单

> [原题链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

> 今天继续更新剑指 offer 系列, 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

![二叉搜索树](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/binarysearchtree_improved.png)

说明:

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉搜索树中。

## 题目样例

### 示例

- **示例 1:**

- 输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
- 输出: 6
- 解释: 节点 2 和节点 8 的最近公共祖先是 6。

- **示例 2:**

- 输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
- 输出: 2
- 解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。

## 题目思考

1. 如何利用二叉搜索树的条件?
2. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

#### 思路

- 二叉搜索树满足`左子树任意点的值 < 根节点值 < 右子树任意点的值`
- 利用此性质, 可以分为以下三种情况:

1. 如果当前根节点介于 p/q 的较小和较大值之间, 那么 p/q 肯定位于根节点的左右子树两侧, 根节点一定是最近祖先
   - 特别注意, 题目说明了 p/q 可能某个节点自身就是祖先, 所以这里的判断需要加上等号, 即`mn<=root.val<=mx`
2. 如果当前根节点大于 p/q 的较大值, 说明 p/q 一定在根节点左侧, 往左继续查找
3. 如果当前根节点小于 p/q 的较小值, 说明 p/q 一定在根节点右侧, 往右继续查找

- 下面代码分别实现了递归和迭代两种方案, 都是利用上述思路, 并对必要步骤有详细解释, 方便大家参考

#### 复杂度

- 时间复杂度 O(N): 最多需要遍历每个节点一遍
- 空间复杂度: 迭代的话只需要维护几个变量, 所以是 O(1); 递归的话要考虑栈的消耗, 显然栈中要存树的高度个节点, 所以是 O(H)

#### 代码

##### 实现方案 1 - 迭代

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode',
                             q: 'TreeNode') -> 'TreeNode':
        # 方法1: 迭代, 利用二叉搜索树的性质, 根据当前节点和p/q的关系决定向左还是向右子树遍历
        mn, mx = min(p.val, q.val), max(p.val, q.val)
        while root:
            # 注意祖先节点可能是p或q自身, 所以这里加上了等于判断
            if mn <= root.val <= mx:
                # p和q分居两侧, root一定是最近祖先
                return root
            elif root.val > mx:
                # 根节点比mx还要大, p/q一定都在左子树上
                root = root.left
            else:
                # 根节点比mn还要小, p/q一定都在右子树上
                root = root.right
        return None
```

##### 实现方案 2 - 递归

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode',
                             q: 'TreeNode') -> 'TreeNode':
        # 方法2: 递归, 思路类似方法1
        if not root:
            return root
        if p.val > q.val:
            # 注意保证p.val<q.val以方便判断, 如果p>q的话就交换参数再开始递归
            return self.lowestCommonAncestor(root, q, p)
        if p.val <= root.val <= q.val:
            # 递归出口, 找到了最近公共祖先
            return root
        elif root.val > q.val:
            # 往左子树找
            return self.lowestCommonAncestor(root.left, p, q)
        else:
            # 往右子树找
            return self.lowestCommonAncestor(root.right, p, q)
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
