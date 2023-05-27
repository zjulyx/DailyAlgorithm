> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/OrIXps/?favorite=e8X3pBZi)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

运用所掌握的数据结构，设计和实现一个 LRU (Least Recently Used，最近最少使用) 缓存机制 。

实现 LRUCache 类：

- LRUCache(int capacity) 以正整数作为容量  capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
- void put(int key, int value)  如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

### 示例：

- 输入
  - ["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
  - [[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
- 输出
  - [null, null, null, 1, null, -1, null, -1, 3, 4]
- 解释

```java
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

### 提示：

- 1 <= capacity <= 3000
- 0 <= key <= 10000
- 0 <= value <= 10^5
- 最多调用 2 \* 10^5 次 get 和 put

## 题目思考

1. 是否可以在  O(1) 时间复杂度内完成这两种操作？

## 解决方案

#### 思路

1. 先设计需要使用的数据结构
   - 根据 LRU 描述, 我们至少需要一个数据结构来存节点的新旧程度, 新的可以放一边, 老的放另一边, 这自然就是双向链表
   - 但是问题是链表虽然更新很快, 是 O(1), 但如何快速通过一个 key 查到对应的节点呢?
   - 很容易想到字典/hash 可以做到 O(1)查询, 然后要做的就是把这两个数据结构结合起来:
     - 双向链表存节点的先后顺序, head 最新, tail 最老
     - 字典存 key=>节点的映射, 方便快速根据 key 定位到节点
2. 接下来就是写具体的逻辑了, 这里一共有 3 种操作:
   1. get 和 put 已经存在的节点: 把已经存在的节点重新放在头部
   2. put 新的节点: 把新的节点放在头部
   3. 删除最老的节点: 如果加入新节点后超过 capacity, 那么需要把最老的 tail 给去掉
3. 逻辑优化
   - 根据第 2 步中分析的 3 种操作, 我们可以直接写出每一种操作对应的代码, 但是里面会有很多重复的部分, 比如放在头部的操作在 2.1 和 2.2 都有, 而删除节点的操作则在 2.1 和 2.3 都有
   - 所以我们完全可以将这两部分操作提取出来
   - 一个是 add 节点操作, 把节点放到头部, 并更新连接关系和字典
   - 一个是 remove 节点操作, 删除某个节点, 并更新连接关系和字典
4. 最后就是具体的代码部分了, 下面代码对每步操作都有详细的注释, 希望可以帮助大家更好理解

### 复杂度

- 时间复杂度 O(1): 链表保证更新是 O(1), 字典保证查询是 O(1)
- 空间复杂度 O(C): C 是 capacity, 字典需要存这么多个 kv, 所以是 O(C)

### 代码

```python
class LRUCache:
    class BiNode:
        def __init__(self, k, v):
            self.key = k
            self.val = v
            self.pre = None
            self.nex = None

    def __init__(self, capacity: int):
        # 双向链表+字典
        # 双向链表存节点的先后顺序, head最新, tail最老
        # 字典方便快速根据key定位到节点
        # 注意链表节点需要存key和value, 存key的目的是用于字典的检索
        # 两者结合就能保证更新和查询的时间复杂度都是O(1), 链表保证更新是O(1), 字典保证查询是O(1)
        # 注意提取新增和移除节点的逻辑, 方便复用, 简化代码
        self.head = None
        self.tail = None
        self.kv = {}
        self.capacity = capacity

    def add(self, node):
        # 将节点加到链表头
        # 先操作字典
        self.kv[node.key] = node
        # 再更新节点连接关系和头尾
        if not self.head:
            # 没有head, 说明当前链表为空, 直接head和tail都设为node即可
            self.head = self.tail = node
        else:
            # 更新新的head以及它与老head的连接关系
            node.nex = self.head
            self.head.pre = node
            self.head = node

    def remove(self, node):
        # 移除某个节点
        # 先操作字典
        if node.key in self.kv:
            del self.kv[node.key]
        # 再更新节点连接关系和头尾
        # 更新左右邻居的连接关系
        pre, nex = node.pre, node.nex
        # 注意当前节点的pre和nex都要重置为None
        node.pre = node.nex = None
        if pre:
            pre.nex = nex
        if nex:
            nex.pre = pre
        # 更新新的头尾
        if node == self.head:
            self.head = nex
        if node == self.tail:
            self.tail = pre

    def get(self, key: int) -> int:
        # 注意get操作也需要将node更新到链表头
        if key in self.kv:
            node = self.kv[key]
            # 先把node从当前位置移除, 然后加入头部
            self.remove(node)
            self.add(node)
            return node.val
        return -1

    def put(self, key: int, value: int) -> None:
        if self.capacity <= 0:
            # 如果capacity是0, 直接无法添加
            return
        if key in self.kv:
            # 如果当前key存在的话, 先移除它
            self.remove(self.kv[key])
        elif len(self.kv) == self.capacity:
            # 注意如果当前已经达到capacity的话先移除tail
            self.remove(self.tail)
        # 加入新节点到头部
        newNode = self.BiNode(key, value)
        self.add(newNode)
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
