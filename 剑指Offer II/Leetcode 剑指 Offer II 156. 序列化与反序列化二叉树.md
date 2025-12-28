> 题目难度: 困难

> [原题链接](https://leetcode.cn/problems/xu-lie-hua-er-cha-shu-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。

请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

提示: 输入输出格式与 LeetCode 目前使用的方式一致，详情请参阅 LeetCode 序列化二叉树的格式。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

### 示例 1：

![](https://assets.leetcode.com/uploads/2020/09/15/serdeser.jpg)

- 输入：root = [1,2,3,null,null,4,5]
- 输出：[1,2,3,null,null,4,5]

### 示例 2：

- 输入：root = []
- 输出：[]

### 示例 3：

- 输入：root = [1]
- 输出：[1]

### 示例 4：

- 输入：root = [1,2]
- 输出：[1,2]

### 提示：

树中结点数在范围 [0, 104] 内
-1000 <= Node.val <= 1000

## 题目思考

1. 如何定位节点之间的关系?

## 解决方案

#### 思路

- 既然题目没有限制序列化和反序列化的算法, 我们就怎样简单怎样来
- 如果我们给每个节点一个唯一的下标, 同时保存它的父节点下标, 以及父节点对当前节点的指向(即左还是右子节点), 当然还有节点本身的值, 这样就能唯一确定整棵树了
- 根据这个思路, 我们序列化的时候就可以使用 BFS, 用一个四元组保存上述信息, 最后转成字符串即可
- 反序列化就更简单了: 使用字典保存下标到值的映射关系(下标唯一), 因为保存的时候是 BFS 逐层遍历的, 所以当要转换某个节点时, 其父节点一定已经转换好并保存在字典中了, 这样就能拿到父节点, 再根据指向决定当前是左还是右子节点即可
- 下面的代码对必要的步骤都有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N)`: 每个节点只需要遍历一次
- 空间复杂度 `O(N)`: 队列的空间消耗, 因为是四元组, 所以是 `O(4*N) = O(N)`

#### 代码

```python
class Codec:
    def serialize(self, root):
        """Encodes a tree to a single string.

        :type root: TreeNode
        :rtype: str
        """
        if not root:
            return ''
        # 注意这里用List而不是Tuple存四元组, 用于下标赋值
        q = [[0, -1, 'L', root]]
        i = 1
        for t in q:
            # 此处只需要关心当前节点下标, 以及当前节点自身即可, 不用管当前节点的父节点和父节点指向
            p, _, _, node = t
            if node.left:
                q.append([i, p, 'L', node.left])
                i += 1
            if node.right:
                q.append([i, p, 'R', node.right])
                i += 1

        def convertToStr(t):
            # 将节点转成对应的值
            t[3] = t[3].val
            # 然后将当前四元组转成字符串
            return ','.join(str(x) for x in t)

        q = [convertToStr(t) for t in q]
        # 最后将数组转成字符串, 注意此处的分隔符要和上面的四元组分隔符区分开来
        return '+'.join(q)

    def deserialize(self, data):
        """Decodes your encoded data to tree.

        :type data: str
        :rtype: TreeNode
        """
        if not data:
            return None
        # 拆分成字符串数组
        data = data.split('+')
        maps = {}
        root = None
        for t in data:
            # 进一步拆分出四元组
            i, p, direction, val = t.split(',')
            node = TreeNode(int(val))
            if i == '0':
                # 保存根节点
                root = node
            # 将当前节点存入字典中
            maps[i] = node
            if p in maps:
                # 父节点存在, 更新指向关系
                parent = maps[p]
                if direction == 'L':
                    parent.left = node
                else:
                    parent.right = node
        return root
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
