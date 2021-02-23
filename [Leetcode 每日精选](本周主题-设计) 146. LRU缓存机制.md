> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/lru-cache/)

> 之前有热心小伙伴提议说每周集中写某一类型的题解, 我觉得这个主意不错, 这周就开始试试吧~ 大家以后有什么想法建议和反馈的话也可以找你最方便的途径跟我交流哦, 包括公众号聊天框/知乎私信评论等等都是可以的!!!🙌

> 这周我们就主要来做一些设计类型的题目吧, 这类题目比较考察综合能力, 中等困难题较多, 但出现频率也相当高, 非常值得思考如何设计和优化

## 题目描述

运用你所掌握的数据结构，设计和实现一个[LRU (最近最少使用) 缓存机制](https://baike.baidu.com/item/LRU)。它应该支持以下操作： 获取数据 get 和 写入数据 put 。

- 获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
- 写入数据 put(key, value) - 如果密钥已经存在，则变更其数据值；如果密钥不存在，则插入该组「密钥/数据值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

进阶:

你是否可以在  O(1) 时间复杂度内完成这两种操作？

## 题目样例

### 示例 1

- LRUCache cache = new LRUCache(2); // 2 是缓存容量

- cache.put(1, 1);
- cache.put(2, 2);
- cache.get(1); // 返回 1
- cache.put(3, 3); // 该操作会使得密钥 2 作废
- cache.get(2); // 返回 -1 (未找到)
- cache.put(4, 4); // 该操作会使得密钥 1 作废
- cache.get(1); // 返回 -1 (未找到)
- cache.get(3); // 返回 3
- cache.get(4); // 返回 4

## 题目思考

1. 相当经典的一道题目, 要使得更新和查询都是 O(1)复杂度, 你想到了哪些数据结构组合能做到?

## 解决方案

### 思路

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

#### Python 3

```python
class BiNode:
    def __init__(self, k, v):
        self.key = k
        self.val = v
        self.pre = None
        self.nex = None


class LRUCache:
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
        # 注意如果当前已经达到capacity的话先移除tail
        if len(self.kv) == self.capacity:
            # 如果加入之后超过capacity, 直接删除tail即可
            self.remove(self.tail)
        # 加入字典
        self.kv[node.key] = node
        if not self.head:
            # 没有head, 说明当前链表为空, 直接head和tail都设为node即可
            self.head = self.tail = node
        else:
            # 更新新的head以及它与老head的连接关系
            curhead = self.head
            node.nex = curhead
            curhead.pre = node
            self.head = node

    def remove(self, node):
        # 移除某个节点
        # 注意第一步仍然是先操作字典
        if node.key in self.kv:
            del self.kv[node.key]
        # 拿到节点的左右邻居
        pre, nex = node.pre, node.nex
        # 更新左右邻居的连接关系
        node.pre = node.nex = None
        if pre:
            pre.nex = nex
        if nex:
            nex.pre = pre
        # 更新新的head和tail
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
            node = self.kv[key]
            self.remove(node)
        # 加入新节点到头部
        newnode = BiNode(key, value)
        self.add(newnode)
```

#### C++

```cpp
struct BiNode {
public:
    BiNode(int k, int v) : key(k), value(v) {}

    int key;
    int value;
    BiNode* prev {nullptr};
    BiNode* next {nullptr};
};

class LRUCache {
public:
    LRUCache(int capacity) : capacity(capacity) {
    }

    int get(int key) {
        if (kv.find(key) == kv.end()) {
            return -1;
        }

        auto node = kv[key];
        remove(node);
        add(node);

        return node ? node->value : -1;
    }

    void put(int key, int value) {
        if (capacity <= 0) {
            return;
        }

        if (kv.find(key) != kv.end()) {
            remove(kv[key]);
        }

        auto node = new BiNode(key, value);
        add(node);

        if (kv.size() > capacity) {
            remove(tail);
        }
    }

private:
    void add(BiNode* node) {
        if (!node) {
            return;
        }

        if (head) {
            head->prev = node;
            node->next = head;
            head = node;
        } else {
            head = tail = node;
        }

        kv.emplace(node->key, node);
    }

    void remove(BiNode* node) {
        if (node->prev) {
            node->prev->next = node->next;
        }
        if (node->next) {
            node->next->prev = node->prev;
        }
        if (node == head) {
            head = node->next;
        }
        if (node == tail) {
            tail = node->prev;
        }

        kv.erase(node->key);
        node->prev = node->next = nullptr;
    }

private:
    int capacity {0};
    BiNode* head {nullptr};
    BiNode* tail {nullptr};
    unordered_map<int, BiNode*> kv;
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
