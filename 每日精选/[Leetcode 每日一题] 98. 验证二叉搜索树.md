> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/validate-binary-search-tree/)

## 题目描述

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

## 题目样例

### 示例 1

#### 输入

```
  2
 / \
1   3
```

#### 输出

true

### 示例 2

#### 输入

```
  5
 / \
1   4
   / \
  3   6
```

#### 输出

false

#### 解释

根节点的值为 5, 但是其右子节点值为 4, 不是二叉搜索树

## 题目思考

1. 根据二叉搜索树的性质, 是否可以先判断左右子树, 然后再判断当前节点这样分治解决?
2. 二叉搜索树经常和中序遍历结合起来, 对于这个题是不是也可以考虑中序遍历呢?
3. 最小时间复杂度是 O(N), 因为至少需要遍历每个节点一次才能做出判断, 那有没有空间复杂度更小的方案呢?

## 解决方案

### 方案 1

#### 思路

- 分别返回左右子树的(整体最大值, 最小值, 是否是有效二叉搜索树)三元组
- 然后再与当前节点值比较看是否满足二叉搜索树性质即可

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历一次
- 空间复杂度 O(N): 递归的栈的消耗

#### 代码

##### Python 3

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        def isvalid(node):
            if not node:
                return (0, 0, True)
            if node.left:
                lmx, lmn, lflag = isvalid(node.left)
            else:
                lmx, lmn, lflag = -float('inf'), node.val, True
            if node.right:
                rmx, rmn, rflag = isvalid(node.right)
            else:
                rmx, rmn, rflag = node.val, float('inf'), True
            if lflag and rflag and lmx < node.val < rmn:
                return (rmx, lmn, True)
            else:
                return (0, 0, False)
        return isvalid(root)[2]
```

##### C++

```cpp
class Solution {
public:
    bool isValidBST(TreeNode *root) {
        return std::get<2>(isValid(root));
    }

    std::tuple<long, long, bool> isValid(TreeNode *root) {
        if (!root) {
            return {0, 0, true};
        }

        long lmax, lmin, rmax, rmin;
        bool lflag, rflag;

        if (root->left) {
            std::tie(lmax, lmin, lflag) = isValid(root->left);
        } else {
            lmax = LONG_MIN;
            lmin = root->val;
            lflag = true;
        }

        if (root->right) {
            std::tie(rmax, rmin, rflag) = isValid(root->right);
        } else {
            rmax = root->val;
            rmin = LONG_MAX;
            rflag = true;
        }

        if (lflag && rflag && lmax < root->val && root->val < rmin) {
            return {rmax, lmin, true};
        }

        return {0, 0, false};
    }
};
```

### 方案 2

#### 思路

- 因为二叉搜索树的中序遍历是单调递增的
- 那么只需要中序遍历, 判断上一个节点值是否始终小于当前节点值即可

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历一次
- 空间复杂度 O(N): 递归的栈的消耗

#### 代码

##### Python 3

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        self.pre = -float('inf')
        def inorder(node):
            if not node:
                return True
            if not inorder(node.left) or self.pre>=node.val:
                return False
            self.pre = node.val
            return inorder(node.right)
        return inorder(root)
```

##### C++

```cpp
class Solution {
public:
    bool isValidBST(TreeNode *root) {
        return inorder(root);
    }

    bool inorder(TreeNode *root) {
        if (!root) {
            return true;
        }
        if (!inorder(root->left) || pre >= root->val) {
            return false;
        }
        pre = root->val;
        return inorder(root->right);
    }

private:
    long pre{LONG_MIN};
};
```

### 方案 3

#### 思路

- 思路同方案 2, 将传统的递归中序遍历转为 morris 中序遍历, 即可以只使用常数空间
  > 不熟悉 morris 遍历的同学可以参考这篇文章: [What is Morris traversal?](https://www.educative.io/edpresso/what-is-morris-traversal), 解释的蛮清楚的, 也可以举一反三试试 morris 前序和后序遍历

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历一次
- 空间复杂度 **O(1)**: 由于没有使用递归, 且只使用了 1 个变量, 所以空间复杂度是常数

#### 代码

##### Python 3

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        pre = -float('inf')
        while root:
            if root.left:
                left = root.left
                preroot = root.left
                while preroot.right:
                    preroot = preroot.right
                preroot.right = root
                root.left = None
                root = left
            else:
                if pre >= root.val:
                    return False
                pre = root.val
                root = root.right
        return True
```

##### C++

```cpp
class Solution {
public:
    bool isValidBST(TreeNode *root) {
        long pre = LONG_MIN;

        while (root) {
            if (root->left) {
                auto left = root->left;
                auto preroot = root->left;
                while (preroot->right) {
                    preroot = preroot->right;
                }
                preroot->right = root;
                root->left = nullptr;
                root = left;
            } else {
                if (pre >= root->val) {
                    return false;
                }
                pre = root->val;
                root = root->right;
            }
        }

        return true;
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
