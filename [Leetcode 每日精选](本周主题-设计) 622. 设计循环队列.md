> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/design-circular-queue/)

> 今天我们来做一道 FIFO 的设计, 这下连带前两天的 LRU 和 LFU, 把常用的内存淘汰算法补齐了 😂
> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

设计你的循环队列实现。 循环队列是一种线性数据结构，其操作表现基于 FIFO（先进先出）原则并且队尾被连接在队首之后以形成一个循环。它也被称为“环形缓冲器”。

循环队列的一个好处是我们可以利用这个队列之前用过的空间。在一个普通队列里，一旦一个队列满了，我们就不能插入下一个元素，即使在队列前面仍有空间。但是使用循环队列，我们能使用这些空间去存储新的值。

你的实现应该支持如下操作：

- MyCircularQueue(k): 构造器，设置队列长度为 k 。
- Front: 从队首获取元素。如果队列为空，返回 -1 。
- Rear: 获取队尾元素。如果队列为空，返回 -1 。
- enQueue(value): 向循环队列插入一个元素。如果成功插入则返回真。
- deQueue(): 从循环队列中删除一个元素。如果成功删除则返回真。
- isEmpty(): 检查循环队列是否为空。
- isFull(): 检查循环队列是否已满。

- 所有的值都在 0 至 1000 的范围内；
- 操作数将在 1 至 1000 的范围内；
- 请不要使用内置的队列库。

## 题目样例

### 示例 1

- MyCircularQueue circularQueue = new MyCircularQueue(3); // 设置长度为 3

- circularQueue.enQueue(1);  // 返回 true

- circularQueue.enQueue(2);  // 返回 true

- circularQueue.enQueue(3);  // 返回 true

- circularQueue.enQueue(4);  // 返回 false，队列已满

- circularQueue.Rear();  // 返回 3

- circularQueue.isFull();  // 返回 true

- circularQueue.deQueue();  // 返回 true

- circularQueue.enQueue(4);  // 返回 true

- circularQueue.Rear();  // 返回 4

## 题目思考

1. 要求循环队列的 FIFO, 你想到了哪些数据结构可以胜任?

## 解决方案

### 方案 1

#### 思路

- 根据题目要求, 空间要能够重复利用, 且要体现循环, 那么可以使用单向链表, 然后初始化好 front 和 rear, 再让 rear 指向 front, 这样就形成了一个环
- 因为链表本身无法统计长度, 所以需要额外维护一个 cnt, 代表当前队列中的元素个数, 用于判断 empty 或者 full
- 这样入队和出队只需要简单的移动 front 和 rear 节点到下一位, 并更新对应节点的值即可

#### 复杂度

- 时间复杂度: 初始化需要 O(K), 每次查询和更新都只需要 O(1)
- 空间复杂度 O(K): 需要存 K 个元素

#### 代码

##### Python 3

```python
class Node:
    def __init__(self, v):
        self.val = v
        self.nex = None


class MyCircularQueue:
    def __init__(self, k: int):
        """
        Initialize your data structure here. Set the size of the queue to be k.
        """
        self.capacity = k
        self.count = 0
        self.front = Node(0)
        cur = self.front
        for i in range(k - 1):
            nex = Node(0)
            cur.nex = nex
            cur = nex
        self.rear = cur
        self.rear.nex = self.front

    def enQueue(self, value: int) -> bool:
        """
        Insert an element into the circular queue. Return true if the operation is successful.
        """
        if self.isFull():
            return False
        self.count += 1
        self.rear = self.rear.nex
        self.rear.val = value
        return True

    def deQueue(self) -> bool:
        """
        Delete an element from the circular queue. Return true if the operation is successful.
        """
        if self.isEmpty():
            return False
        self.count -= 1
        self.front = self.front.nex
        return True

    def Front(self) -> int:
        """
        Get the front item from the queue.
        """
        if self.isEmpty():
            return -1
        return self.front.val

    def Rear(self) -> int:
        """
        Get the last item from the queue.
        """
        if self.isEmpty():
            return -1
        return self.rear.val

    def isEmpty(self) -> bool:
        """
        Checks whether the circular queue is empty or not.
        """
        return self.count == 0

    def isFull(self) -> bool:
        """
        Checks whether the circular queue is full or not.
        """
        return self.count == self.capacity
```

##### C++

```cpp
struct Node {
public:
    Node() {}
    Node(int v) : value {v} {}

    int value {-1};
    Node* next {nullptr};
};

class MyCircularQueue {
public:
    /** Initialize your data structure here. Set the size of the queue to be k. */
    MyCircularQueue(int k) : nodes(k) {
        for (int i = 0; i < k - 1; ++i) {
            nodes[i].next = &nodes[i + 1];
        }
        nodes[k - 1].next = &nodes[0];
        front = &nodes[0];
        rear = &nodes[k - 1];
    }

    /** Insert an element into the circular queue. Return true if the operation is successful. */
    bool enQueue(int value) {
        if (isFull()) {
            return false;
        }

        ++count;
        rear = rear->next;
        rear->value = value;
        return true;
    }

    /** Delete an element from the circular queue. Return true if the operation is successful. */
    bool deQueue() {
        if (isEmpty()) {
            return false;
        }
        --count;
        front = front->next;
        return true;
    }

    /** Get the front item from the queue. */
    int Front() {
        if (isEmpty()) {
            return -1;
        }
        return front->value;
    }

    /** Get the last item from the queue. */
    int Rear() {
        if (isEmpty()) {
            return -1;
        }
        return rear->value;
    }

    /** Checks whether the circular queue is empty or not. */
    bool isEmpty() {
        return count == 0;
    }

    /** Checks whether the circular queue is full or not. */
    bool isFull() {
        return count == nodes.size();
    }

private:
    int count {0};
    Node* front{nullptr};
    Node* rear{nullptr};
    vector<Node> nodes;
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们使用了一个单向链表来作为循环队列, 需要初始化 k 个节点, 有没有可能直接用一个数组做到?
- 答案也是可以的, 我们只需要将方案 1 中的实际节点转成对应数组的下标, 然后每次更新只需要移动下标, 如果到达末尾后就取模重新回到开头即可

#### 复杂度

- 时间复杂度 O(1): 初始化只需要初始化一个长度为 k 的数组, 更新只是下标的移动, 查询也只是数组下标定位, 都是 O(1)
- 空间复杂度 O(K): 需要存 K 个元素

#### 代码

##### Python 3

```python
class MyCircularQueue:
    def __init__(self, k: int):
        """
        Initialize your data structure here. Set the size of the queue to be k.
        """
        # 使用front和rear指针代表队列的两端, 使用cnt表示当前队列长度
        # 注意enqueue和dequeue都是front或rear加1后然后取模
        # 注意rear初始化要为-1, 使得最开始的enqueue从0开始赋值
        # dequeue只需要移动front下标即可
        self.q = [0] * k
        self.front = 0
        self.rear = -1
        self.cnt = 0

    def enQueue(self, value: int) -> bool:
        """
        Insert an element into the circular queue. Return true if the operation is successful.
        """
        if self.isFull():
            return False
        self.cnt += 1
        self.rear = (self.rear + 1) % len(self.q)
        self.q[self.rear] = value
        return True

    def deQueue(self) -> bool:
        """
        Delete an element from the circular queue. Return true if the operation is successful.
        """
        if self.isEmpty():
            return False
        self.cnt -= 1
        self.front = (self.front + 1) % len(self.q)
        return True

    def Front(self) -> int:
        """
        Get the front item from the queue.
        """
        if self.isEmpty():
            return -1
        return self.q[self.front]

    def Rear(self) -> int:
        """
        Get the last item from the queue.
        """
        if self.isEmpty():
            return -1
        return self.q[self.rear]

    def isEmpty(self) -> bool:
        """
        Checks whether the circular queue is empty or not.
        """
        return self.cnt == 0

    def isFull(self) -> bool:
        """
        Checks whether the circular queue is full or not.
        """
        return len(self.q) == self.cnt
```

##### C++

```cpp
class MyCircularQueue {
public:
    /** Initialize your data structure here. Set the size of the queue to be k. */
    MyCircularQueue(int k) : q(k, 0) {
    }

    /** Insert an element into the circular queue. Return true if the operation is successful. */
    bool enQueue(int value) {
        if (isFull()) {
            return false;
        }

        ++count;
        rear = (rear + 1) % q.size();
        q[rear] = value;
        return true;
    }

    /** Delete an element from the circular queue. Return true if the operation is successful. */
    bool deQueue() {
        if (isEmpty()) {
            return false;
        }
        --count;
        front = (front + 1) % q.size();
        return true;
    }

    /** Get the front item from the queue. */
    int Front() {
        if (isEmpty()) {
            return -1;
        }
        return q[front];
    }

    /** Get the last item from the queue. */
    int Rear() {
        if (isEmpty()) {
            return -1;
        }
        return q[rear];
    }

    /** Checks whether the circular queue is empty or not. */
    bool isEmpty() {
        return count == 0;
    }

    /** Checks whether the circular queue is full or not. */
    bool isFull() {
        return count == q.size();
    }

private:
    int count {0};
    int front{0};
    int rear{-1};
    vector<int> q;
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
