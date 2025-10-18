> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/dui-cheng-de-er-cha-shu-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

请设计一个函数判断一棵二叉树是否 轴对称 。

### 示例 1：

![](https://pic.leetcode.cn/1694689008-JaaRdV-%E8%BD%B4%E5%AF%B9%E7%A7%B0%E4%BA%8C%E5%8F%89%E6%A0%911.png)

- 输入：root = [6,7,7,8,9,9,8]
- 输出：true
- 解释：从图中可看出树是轴对称的。

### 示例 2：

![](https://pic.leetcode.cn/1694689054-vENzHe-%E8%BD%B4%E5%AF%B9%E7%A7%B0%E4%BA%8C%E5%8F%89%E6%A0%912.png)

- 输入：root = [1,2,2,null,3,null,3]
- 输出：false
- 解释：从图中可看出最后一层的节点不对称。

### 提示：

- 0 <= 节点个数 <= 1000

## 题目思考

1. 生成镜像比较可以吗, 直接比较呢?
2. 如果限制只能用递归或者迭代, 如何解决?

## 解决方案

### 方案 1

#### 思路

- 根据题目描述, 最自然的思路就是生成原树的镜像后, 再逐个节点比较是否完全相等即可
- 对于生成镜像部分不清楚的同学可以回顾下上篇文章 `Leetcode 剑指 Offer II 144.翻转二叉树`
- 需要注意这里生成的镜像不能直接在原树基础上改动了, 需要生成新的节点, 也即新建一个节点使用原来的节点值, 不然下面比较的时候就是和自身比较, 肯定总是相等了
- 逐个节点比较也很简单, 如果其中某个节点为空, 则必须另一个也是空; 而如果两个节点都非空的话, 则要比较它们的值, 并传入左右子节点递归比较子树的相等性

#### 复杂度

- 时间复杂度 `O(N)`
  - 所有节点只需要遍历一遍
- 空间复杂度 `O(N)`
  - 递归栈的消耗, 以及镜像树的空间

#### 代码

```python
class Solution:
    def checkSymmetricTree(self, root: Optional[TreeNode]) -> bool:
        # 方法1: 先拿到镜像, 然后逐节点比较
        def getMirror(root):
            if not root:
                return root
            # 注意这里要新建node, 不能原地修改!!
            newNode = TreeNode(root.val)
            newNode.left, newNode.right = getMirror(root.right), getMirror(
                root.left)
            return newNode

        mirror = getMirror(root)

        def ismatch(n1, n2):
            if not n1 or not n2:
                return not n1 and not n2
            # 这里需要比较n1和n2树精确匹配
            return n1.val == n2.val and ismatch(n1.left, n2.left) and ismatch(
                n1.right, n2.right)

        # 当原树和镜像树完全匹配的时候才对称
        return ismatch(root, mirror)
```

### 方案 2

#### 思路

- 回顾方案 1, 我们需要先生成镜像再比较, 有没有直接一遍比较就能得出结果的方法呢?
- 答案也是有的, 注意题目描述, 对称时左子树的最左节点等于右子树的最右节点, 就像中间有个对称轴一样
- 所以我们可以传入当前左右子树上处于对称位置的两个节点, 比较它们是否相等, 相等的话再递归调用比较方法, 比较(左节点的右子节点, 右节点的左子节点)以及(左节点的左子节点, 右节点的右子节点), 也即子树上的两对对称节点, 如果都满足对称性的话则说明整个树对称

#### 复杂度

- 时间复杂度 `O(N)`
  - 所有节点只需要遍历一遍
- 空间复杂度 `O(N)`
  - 递归栈的消耗

#### 代码

```python
class Solution:
    def checkSymmetricTree(self, root: Optional[TreeNode]) -> bool:
        # 方法2: 递归直接比较
        if not root:
            return True

        def symm(left, right):
            if not left or not right:
                # left和right某一个是空的时候, 需要两个都是空才算对称
                return not left and not right
            # 对称需要满足当前两节点值相等, 且左边的左节点=右边的右节点 & 左边的右节点=右边的左节点
            return left.val == right.val and symm(
                left.left, right.right) and symm(left.right, right.left)

        # 此时root一定非空, 直接传入左右子节点判断对称性即可
        return symm(root.left, root.right)
```

### 方案 3

#### 思路

- 接下来我们考虑迭代做法
- 根据方案 2, 我们需要比较左右子树处于对称位置的节点
- 所以我们完全可以将这些对称节点放在两个队列中, 分别代表左右子树中的节点
- 然后仍然按照方案 2 的顺序将子节点依次加入队列, 保证比较的是处于对称位置的节点即可
- 注意空节点也需要被加入队列, 用作占位符, 不然对于用例 2 就会错误地判断成对称的了
- 下面代码中有详细解释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`
  - 所有节点只需要遍历一遍
- 空间复杂度 `O(N)`
  - 两个队列各自需要存 N/2 个节点

#### 代码

```python
class Solution:
    def checkSymmetricTree(self, root: Optional[TreeNode]) -> bool:
        # 方法3: 迭代, 使用两个队列存左子树和右子树的节点
        if not root:
            return True
        leftq, rightq = [root.left], [root.right]
        # 这里可以优化为只使用一个下标, 因为下标总是相等的. 这里为了更容易理解, 采用两个下标
        i, j = 0, 0
        while i < len(leftq) or j < len(rightq):
            if i == len(leftq) or j == len(rightq):
                # 其中一方队列遍历完了, 肯定不对称
                return False
            left, right = leftq[i], rightq[j]
            i += 1
            j += 1
            if not left or not right:
                # 某一边节点为空的情况
                if not left and not right:
                    # 必须两边都是空才可以继续循环
                    continue
                else:
                    # 否则肯定不对称
                    return False
            else:
                if left.val != right.val:
                    # 值不相等, 不对称
                    return False
                # 注意这里顺序, 保证左边的right和右边的left比较, 左边的left和右边的right比较 (同方法2思路)
                leftq.append(left.right)
                leftq.append(left.left)
                rightq.append(right.left)
                rightq.append(right.right)
        return True
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
