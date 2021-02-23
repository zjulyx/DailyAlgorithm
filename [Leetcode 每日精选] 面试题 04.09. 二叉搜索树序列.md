> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/bst-sequences-lcci/)

> 今天来一道困难题, 但我觉得这道题很有质量, 可以考察逆向思维, 思路也比较清晰, 个人预测这道题出现频率会比较高 😂

## 题目描述

从左向右遍历一个数组，通过不断将其中的元素插入树中可以逐步地生成一棵二叉搜索树。给定一个由不同节点组成的二叉树，输出所有可能生成此树的数组。

## 题目样例

### 示例 1

#### 输入

```
        2
       / \
      1   3
```

#### 输出

[
[2,1,3],
[2,3,1]
]

#### 解释

第一个数一定要是 2, 第二和第三个数可以互换

## 题目思考

1. 从左向右遍历, 那么根节点一定是数组第一个元素, 可以利用到这一特性吗?
2. 对于数组某个元素而言, 其有效插入位置可能有哪些?

## 解决方案

### 思路

- 使用一个 queue 存储下个所有可能的节点
- 然后选择其中一个作为 path 的下一个元素
- 递归直到 queue 元素为空
- 将对应的 path 加入结果中
- 由于二叉搜索树没有重复元素, 而且每次递归的使用元素的顺序都不一样, 所以自动做到了去重
- 也可以将 queue 换成集合, 效果一样, 效率也差不多, 只是把切片变成了求差集
- python 3 只需要 15 行代码即可做到

### 复杂度

- 时间复杂度 O((N^2)\*(2^N)): 对于每个节点都要考虑其左右子节点, 所以 2^N 次递归, 每个递归里面也要遍历 queue, 以及切片产生新的 queue, 这部分是 O(N^2)
- 空间复杂度 O(N): 递归的栈的消耗, 以及两个数组, 其元素数目都是 N (不考虑结果数组的占用)

### 代码

#### Python 3

```python
class Solution:
    def BSTSequences(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return [[]]
        res = []
        def findPath(curNode, nextValidNodes, path):
            if curNode.left:
                nextValidNodes.append(curNode.left)
            if curNode.right:
                nextValidNodes.append(curNode.right)
            if not nextValidNodes:
                res.append(path)
                return
            for i, nex in enumerate(nextValidNodes):
                newq = nextValidNodes[:i] + nextValidNodes[i + 1:]
                findPath(nex, newq, path + [nex.val])
        findPath(root, [], [root.val])
        # 使用集合的做法, 又能少一行..
        # def findPath(curNode, nextValidNodes, path):
        #     if curNode.left:
        #         nextValidNodes.add(curNode.left)
        #     if curNode.right:
        #         nextValidNodes.add(curNode.right)
        #     if not nextValidNodes:
        #         res.append(path)
        #         return
        #     for nex in nextValidNodes:
        #         findPath(nex, nextValidNodes - {nex}, path + [nex.val])
        # findPath(root, set(), [root.val])
        return res
```

#### C++

```cpp
class Solution {
public:
    vector<vector<int>> BSTSequences(TreeNode *root) {
        vector<vector<int>> res;
        if (!root) {
            res.push_back({});
            return res;
        }

        function<void(TreeNode *, vector<TreeNode *>, vector<int>)> findPath =
                [&](TreeNode *curNode, vector<TreeNode *> nextValidNodes, vector<int> path) {
                    if (curNode->left) {
                        nextValidNodes.push_back(curNode->left);
                    }
                    if (curNode->right) {
                        nextValidNodes.push_back(curNode->right);
                    }
                    if (nextValidNodes.empty()) {
                        res.push_back(path);
                        return;
                    }

                    int size = nextValidNodes.size();
                    for (int i = 0; i < size; ++i) {
                        swap(nextValidNodes[i], nextValidNodes[size - 1]);
                        vector<TreeNode *> newq(&nextValidNodes[0], &nextValidNodes[size - 1]);
                        swap(nextValidNodes[i], nextValidNodes[size - 1]);
                        path.push_back(nextValidNodes[i]->val);
                        findPath(nextValidNodes[i], newq, path);
                        path.pop_back();
                    }
                };

        findPath(root, {}, {root->val});

        return res;
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
