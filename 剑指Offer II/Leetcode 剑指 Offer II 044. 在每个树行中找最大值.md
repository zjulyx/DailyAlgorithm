> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/hPov7L/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一棵二叉树的根节点 root ，请找出该二叉树中每一层的最大值。

### 示例 1：

- 输入: root = [1,3,2,5,3,null,9]
- 输出: [1,3,9]
- 解释:

```
          1
         / \
        3   2
       / \   \
      5   3   9
```

### 示例 2：

- 输入: root = [1,2,3]
- 输出: [1,3]
- 解释:

```
          1
         / \
        2   3
```

### 示例 3：

- 输入: root = [1]
- 输出: [1]

### 示例 4：

- 输入: root = [1,null,2]
- 输出: [1,2]
- 解释:

```
           1
            \
             2
```

### 示例 5：

- 输入: root = []
- 输出: []

### 提示：

- 二叉树的节点个数的范围是 [0,10^4]
- -2^31 <= Node.val <= 2^31 - 1

## 题目思考

1. 如何记录当前层的信息?

## 解决方案

#### 思路

- 分析题目, 需要找到每一层的最大值, 所以我们可以使用经典的按层 BFS 来解决, 具体思路如下:
  - 记录下当前层的节点边界
  - 然后当前层的子节点都加入队列后, 将队列更新为从下一层节点起点开始
  - 这样就确保每一层都单独被记下来了
- 具体实现细节如下:
  - 使用一个队列存储节点
  - 接下来开始循环, 记录当前队列长度 curlen
  - 然后遍历前 curlen 个节点, 统计其中的最大值, 并将它们的左右非空子节点追加到队列结尾
  - 当前层遍历结束时, 将最大值追加到最终结果中, 下层的起点下标自然就是 curlen, 所以只需要将队列切片成 curlen 及以后的部分即可
  - 最终当队列没有元素时则说明所有节点都遍历过了, 退出循环
- 由于这里是树, 所以每个节点只可能被加入队列访问一次, 无需额外的 visit 集合
- 下面的代码就对应了上面的整个过程, 并且有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(N): 需要遍历每个节点一遍
- 空间复杂度 O(N): 需要存储所有节点到对应的层

#### 代码

```python
class Solution:
    def largestValues(self, root: TreeNode) -> List[int]:
        # 按层BFS
        res = []
        if not root:
            # 没有节点, 返回空列表
            return res
        # 队列初始化为第一层, 即根节点
        q = [root]
        while q:
            # 记录当前层的节点个数curlen
            curlen = len(q)
            # 记录当前层最大值, 初始化为该层第一个节点的值
            mx = q[0].val
            # 只遍历当前层的节点, 即前curlen个
            for node in q[:curlen]:
                # 更新最大值
                mx = max(mx, node.val)
                # 左右子节点非空时, 将其追加到队列中
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            # 将当前层最大值追加到最终结果中
            res.append(mx)
            # 将队列更新成下一层的节点
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
