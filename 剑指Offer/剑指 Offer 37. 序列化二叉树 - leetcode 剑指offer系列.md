> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

> 今天继续更新剑指 offer 系列, 这道题是一道非常灵活的设计题目, 这里提供一种非常容易理解的方法抛砖引玉, 大家有其他的好方法也欢迎私信我一起交流哦

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请实现两个函数，分别用来序列化和反序列化二叉树。

## 题目样例

### 示例

你可以将以下二叉树：

```
    1
   / \
  2   3
     / \
    4   5
```

序列化为 "[1,2,3,null,null,4,5]"

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

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历一次
- 空间复杂度 `O(N)`
  - 队列的消耗, 因为是四元组, 所以是 `O(4*N) = O(N)`

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

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
