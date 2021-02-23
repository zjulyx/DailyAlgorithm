> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/lfu-cache/)

> 今天继续来做设计题目, 昨天是 LRU, 今天就来做它的进阶: LFU 吧 😂
> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

请你为 [最不经常使用（LFU）缓存算法](https://baike.baidu.com/item/%E7%BC%93%E5%AD%98%E7%AE%97%E6%B3%95)设计并实现数据结构。它应该支持以下操作：get  和  put。

- get(key) - 如果键存在于缓存中，则获取键的值（总是正数），否则返回 -1。
- put(key, value) - 如果键已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量时，则应该在插入新项之前，使最不经常使用的项无效。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除最久未使用的键。

「项的使用次数」就是自插入该项以来对其调用 get 和 put 函数的次数之和。使用次数会在对应项被移除后置为 0 。

进阶：
你是否可以在  O(1)  时间复杂度内执行两项操作？

## 题目样例

### 示例 1

- LFUCache cache = new LFUCache(2); // 2 是缓存容量

- cache.put(1, 1);
- cache.put(2, 2);
- cache.get(1); // 返回 1
- cache.put(3, 3); // 去除 key 2
- cache.get(2); // 返回 -1 (未找到 key 2)
- cache.get(3); // 返回 3
- cache.put(4, 4); // 去除 key 1
- cache.get(1); // 返回 -1 (未找到 key 1)
- cache.get(3); // 返回 3
- cache.get(4); // 返回 4

## 题目思考

1. 相比昨天的 LRU, 这里需要额外保存每个值的访问次数, 还可以继续使用 LRU 的数据结构吗, 还是说需要什么改进?

## 解决方案

### 思路

1. 先设计需要使用的数据结构
   - 回顾之前的 LRU, 我们使用了一个双向链表和一个字典, 分别用于更新和查询
   - LFU 多了一个访问频率 freq, 那么我们需要在节点中额外存储这一信息
   - 淘汰的时候需要淘汰频率最低的且最长时间没有被使用的, 根据这个条件, 我们可以再定义一个 freq 字典, 其 key 是频率, value 是对应的 head 和 tail. 就不需要 LRU 中的全局唯一的 head 和 tail 了, 而是改成按 freq 分层的 head 和 tail
   - 另外需要额外维护一个变量, 存最低频率 minfreq
   - 这样淘汰时直接移除 minfreq 对应的 tail 即可
2. 接下来就是写具体的逻辑了, 这里一共有 3 种操作:
   1. get 和 put 已经存在的节点: 先将当前节点从其所在的 freq 层中移除, 然后将其放到 freq+1 层的头部
   2. put 新的节点: 把新的节点放在 freq=1 层的头部. 如果该操作导致超过 capacity, 需要删除最老的节点. 同时要向 kv 字典中添加 key=>node 的映射
   3. 删除最老的节点: 如果加入新节点后超过 capacity, 那么需要删除 minfreq 层的 tail
3. 逻辑优化
   - 和昨天的 LRU 一样, 在第 2 步中的 3 种操作中有不少重复的部分
   - 所以我们仍然可以将这两部分操作提取出来:
     - 一个是 add 节点操作, 把节点放到对应 freq 层的头部, 并更新连接关系和字典
     - 一个是 remove 节点操作, 删除某个节点, 并更新连接关系和字典. 如果删除后导致该 freq 层没有节点的话, 就把该层整个删除
4. 最后就是具体的代码部分了, 下面代码对每步操作都有详细的注释, 希望可以帮助大家更好理解

### 复杂度

- 时间复杂度 O(1): 链表保证更新是 O(1), 字典保证查询是 O(1)
- 空间复杂度 O(C): C 是 capacity, kv 字典需要存这么多个 kv, 而 freq 字典由于节点的 freq 可能相同, 然后每一层只需要存两个值, 所以其空间消耗最多为 2C (当每个节点频率都不一样时). 综合起来的空间复杂度仍为 O(C)

### 代码

#### Python 3

```python
class BiNode:
    def __init__(self, k, v):
        self.key = k
        self.val = v
        self.freq = 1
        self.pre = None
        self.nex = None


class LFUCache:
    def __init__(self, capacity: int):
        # 维护freq=>head/tail的字典和kv字典
        # 双端链表, 节点存kv和freq
        # 抽取add和remove node操作, 只传入参数node即可
        # 在add和remove中完成两个字典的插入删除, 以及节点的断开和连接, 还有minfreq的更新
        # 初始化minfreq为None, 表示不存在, 注意capacity为0的特殊情况
        self.kv = {}
        self.freqdict = {}
        self.minfreq = None
        self.capacity = capacity

    def add(self, node):
        if len(self.kv) == self.capacity:
            # 如果加入当前node时已经达到capacity, 先移除最老的节点
            old = self.freqdict[self.minfreq][1]
            self.remove(old)
        # 更新kv字典
        self.kv[node.key] = node
        freq = node.freq
        # 如果minfreq不存在或当前节点freq小于minfreq, 就更新它
        if self.minfreq is None or freq < self.minfreq:
            self.minfreq = freq
        if freq not in self.freqdict:
            # 如果当前freq层不存在, 直接设置head和tail都为当前节点即可
            self.freqdict[freq] = [node, node]
        else:
            # 将当前节点加入当前freq层的头部
            curhead = self.freqdict[freq][0]
            curhead.pre = node
            node.nex = curhead
            self.freqdict[freq][0] = node

    def remove(self, node):
        # 更新kv字典
        if node.key in self.kv:
            del self.kv[node.key]
        freq = node.freq
        # 更新左右邻居的连接关系
        pre, nex = node.pre, node.nex
        # 注意当前节点的pre和nex都要重置为None
        node.pre = node.nex = None
        head, tail = self.freqdict[freq]
        if pre:
            pre.nex = nex
        if nex:
            nex.pre = pre
        # 更新当前freq层新的head和tail
        if node == head:
            self.freqdict[freq][0] = nex
        if node == tail:
            self.freqdict[freq][1] = pre
        if self.freqdict[freq][0] is None and self.freqdict[freq][1] is None:
            # 如果当前层没有节点了, 直接删除它
            del self.freqdict[freq]
            # 如果恰好当前freq等于minfreq, 将minfreq置为None表示其不存在了
            if freq == self.minfreq:
                self.minfreq = None

    def get(self, key: int) -> int:
        if key not in self.kv:
            return -1
        node = self.kv[key]
        # 先移除当前节点, freq+1后再将其加入
        self.remove(node)
        node.freq += 1
        self.add(node)
        return node.val

    def put(self, key: int, value: int) -> None:
        # 注意capacity为0的特殊情况
        if self.capacity == 0:
            return
        if key in self.kv:
            # 当前节点已经存在
            # 先移除当前节点, freq+1并更新了value后再将其加入
            node = self.kv[key]
            self.remove(node)
            node.freq += 1
            node.val = value
        else:
            # 新建节点, 默认freq为1 (定义在BiNode类中)
            node = BiNode(key, value)
        # 加入更新后或者新建的节点
        self.add(node)
```

#### C++

```cpp
struct BiNode {
public:
    BiNode(int k, int v) : key(k), value(v) {}

    int key {0};
    int value {0};
    int freq {1};
    BiNode* prev {nullptr};
    BiNode* next {nullptr};
};

class LFUCache {
public:
    LFUCache(int capacity) : capacity(capacity) {
    }

    int get(int key) {
        if (kv.find(key) == kv.end()) {
            return -1;
        }

        auto node = kv[key];
        remove(node);
        ++node->freq;
        add(node);

        return node ? node->value : -1;
    }

    void put(int key, int value) {
        if (capacity <= 0) {
            return;
        }

        BiNode* node;
        if (kv.find(key) != kv.end()) {
            node = kv[key];
            remove(node);
            ++node->freq;
            node->value = value;
        } else {
            node = new BiNode(key, value);
        }

        add(node);
    }

private:
    void add(BiNode* node) {
        if (kv.size() == capacity) {
            remove(freqdict[minfreq][1]);
        }

        int freq = node->freq;
        if (freq < minfreq) {
            minfreq = freq;
        }
        if (freqdict.find(freq) == freqdict.end()) {
            freqdict.emplace(freq, vector<BiNode*>(2, node));
        } else if (freqdict[freq][0]) {
            freqdict[freq][0]->prev = node;
            node->next = freqdict[freq][0];
            freqdict[freq][0] = node;
        }

        kv.emplace(node->key, node);
    }

    void remove(BiNode* node) {
        if (!node) {
            return;
        }

        auto prev = node->prev;
        auto next = node->next;

        if (prev) {
            prev->next = next;
        }
        if (next) {
            next->prev = prev;
        }
        node->prev = node->next = nullptr;

        int freq = node->freq;
        if (node == freqdict[freq][0]) {
            freqdict[freq][0] = next;
        }
        if (node == freqdict[freq][1]) {
            freqdict[freq][1] = prev;
        }
        if (!freqdict[freq][0] && !freqdict[freq][1]) {
            freqdict.erase(freq);
            if (freq == minfreq) {
                minfreq = INT_MAX;
            }
        }
        kv.erase(node->key);
    }

private:
    int capacity {0};
    int minfreq {INT_MAX};
    unordered_map<int, BiNode*> kv;
    unordered_map<int, vector<BiNode*>> freqdict;
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
