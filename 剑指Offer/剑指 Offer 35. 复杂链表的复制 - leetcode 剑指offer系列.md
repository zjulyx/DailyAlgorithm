> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

> 今天继续更新剑指 offer 系列, 这道题实现起来并不难, 但需要一定的思考, 大家可以先尝试自己想想如何解决

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

- -10000 <= Node.val <= 10000
- Node.random 为空（null）或指向链表中的节点。
- 节点数目不超过 1000 。

## 题目样例

### 示例

#### 输入

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/01/09/e1.png)

head = [[7,null],[13,0],[11,4],[10,2],[1,0]]

#### 输出

[[7,null],[13,0],[11,4],[10,2],[1,0]]

## 题目思考

1. 如何处理 random 指针?

## 解决方案

#### 思路

- 如果只有 next 指针的话很简单, 我们只需要对每个节点新建一个相同值的节点, 并保持指向关系, 逐个遍历过去即可
- 现在多了个 random 指针, 想要定位新的指向的节点, 一个比较自然的想法就是额外维护一个老节点到新节点的映射关系, 可以用字典来实现
- 第一遍遍历, 就只关注 next 部分, 并建立好映射关系
- 第二遍遍历, 考虑 random 部分, 找到对应的新链表的节点, 然后当前节点的 random 指针指向它即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要遍历两次
- 空间复杂度 `O(N)`
  - 额外需要一个字典

#### 代码

```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        if not head:
            return None
        maps = {}
        # 第一遍遍历, 建立新的链表, 以及老节点到新节点的映射关系
        copyHead = Node(head.val)
        origin, copy = head, copyHead
        maps[origin] = copy
        while origin.next:
            # 新建下一个节点, 并建立next关系
            copy.next = Node(origin.next.val)
            origin = origin.next
            copy = copy.next
            maps[origin] = copy
        # 第二遍遍历, 处理random指针部分
        origin, copy = head, copyHead
        while origin:
            if origin.random:
                # 如果老节点random指针指向非空的话, 就将当前新节点也指向随机节点对应的新节点
                copy.random = maps[origin.random]
            origin = origin.next
            copy = copy.next
        return copyHead
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
