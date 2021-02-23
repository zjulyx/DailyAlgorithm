> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/insert-delete-getrandom-o1-duplicates-allowed)

> 今天我们来做一道包含随机因素的设计题, 这道题是 [380. 常数时间插入、删除和获取随机元素](https://leetcode-cn.com/problems/insert-delete-getrandom-o1/)的进阶版, 感兴趣的同学可以先看看那道题如何解决

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

设计一个支持在平均时间复杂度 O(1) 下，执行以下操作的数据结构。

- insert(val)：当元素 val 不存在时，向集合中插入该项。
- remove(val)：元素 val 存在时，从集合中移除该项。
- getRandom：随机返回现有集合中的一项。每个元素应该有相同的概率被返回。

## 题目样例

### 示例

```java
// 初始化一个空的集合。
RandomizedSet randomSet = new RandomizedSet();

// 向集合中插入 1 。返回 true 表示 1 被成功地插入。
randomSet.insert(1);

// 返回 false ，表示集合中不存在 2 。
randomSet.remove(2);

// 向集合中插入 2 。返回 true 。集合现在包含 [1,2] 。
randomSet.insert(2);

// getRandom 应随机返回 1 或 2 。
randomSet.getRandom();

// 从集合中移除 1 ，返回 true 。集合现在包含 [2] 。
randomSet.remove(1);

// 2 已在集合中，所以返回 false 。
randomSet.insert(2);

// 由于 2 是集合中唯一的数字，getRandom 总是返回 2 。
randomSet.getRandom();
```

## 题目思考

1. 注意题目要求, 要使得每个元素返回的概率都相同, 但又存在重复, 你想到了哪些方法可以保证这种平均性?
2. 要使得三种操作平均复杂度都是 O(1), 需要哪些数据结构的组合?

## 解决方案

### 思路

1. 先设计需要使用的数据结构
   - 考虑随机元素取值要平均, 自然想到可以使用一个**列表**存下来所有的元素, 然后每次直接取一个长度范围内的下标对应的元素即可
   - 如何保证插入和删除也要 O(1), 我们需要额外引入一个**字典/hash**, 因为删除的是元素值, 那么字典中存的 key 就需要是元素值, 这样才能在 O(1)时间内定位
   - 而为了关联起来字典和列表, 字典的值需要是列表下标的集合. 为什么不能是下标列表呢, 这是为了删除的时候也能做到 O(1)
2. 接下来就是写具体的逻辑了, 这里一共有 3 种操作:
   1. 获取随机值: 只需要得到一个在长度范围内的随机下标即可, 只需要 O(1)时间, 而且这样保证了每个元素都有相同的概率被取到
   2. 插入值: 列表直接追加一个元素, 并更新对应的值和下标组合到字典中, 只需要 O(1)时间
   3. 删除值: 删除的操作比较复杂, 因为为了保证删除列表元素也只使用 O(1)时间, 我们需要额外的处理
      1. 首先定位到字典对应值的下标集合, 如果列表最后一个下标恰好在该集合, 直接把列表 pop 一下, 然后字典中移除最后的下标即可, 都只需要 O(1)时间
      2. 否则可以先从集合 pop 一个下标出来, **然后交换该下标和列表最后一个下标的值**, 并更新字典中对应键值对的结果, 最后采用上一步的操作即可, 也只需要 O(1)时间
3. 最后就是具体的代码部分了, 下面代码对每步操作都有详细的注释, 希望可以帮助大家更好理解

### 复杂度

- 时间复杂度 O(1): 每种操作都只需要 O(1)时间, 不管是列表 pop, 集合 remove 还是字典更新
- 空间复杂度 O(N): 需要存 N 个元素

### 代码

#### Python 3

```python
from collections import defaultdict
import random


class RandomizedCollection:
    def __init__(self):
        """
        Initialize your data structure here.
        """
        # 使用字典+列表的组合
        # 列表存每个元素值
        # 字典存{元素值:{对应的列表所有下标}}
        self.dict = defaultdict(set)
        self.list = []

    def insert(self, val: int) -> bool:
        """
        Inserts a value to the collection. Returns true if the collection did not already contain the specified element.
        """
        notExist = True
        if val in self.dict:
            notExist = False
        # 列表追加值
        self.list.append(val)
        # 字典更新val和对应的下标
        self.dict[val].add(len(self.list) - 1)
        return notExist

    def remove(self, val: int) -> bool:
        """
        Removes a value from the collection. Returns true if the collection contained the specified element.
        """
        exist = False
        if val in self.dict and self.dict[val]:
            exist = True
            lastIndex = len(self.list) - 1
            if lastIndex in self.dict[val]:
                # 只需要pop移除列表的最后一个元素
                self.dict[val].remove(lastIndex)
                self.list.pop()
            else:
                # 列表最后的下标不在集合中, 需要下标交换
                index = self.dict[val].pop()
                lastVal = self.list[lastIndex]
                # 交换当前下标和最后下标的值, 并将列表pop, (O(1))
                self.list[index], self.list[lastIndex] = self.list[
                    lastIndex], self.list[index]
                self.list.pop()
                # 注意, 也需要更新对应的字典, 由于最后的下标已经移除了, 所以这时候不需要在dict[val]中加入lastIndex
                self.dict[lastVal].add(index)
                self.dict[lastVal].remove(lastIndex)

        return exist

    def getRandom(self) -> int:
        """
        Get a random element from the collection.
        """
        index = random.randint(0, len(self.list) - 1)
        return self.list[index]
```

#### C++

```cpp
class RandomizedCollection {
public:
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    bool insert(int val) {
        bool ret = notExist(val);
        list.push_back(val);
        dict[val].insert(list.size() - 1);
        return ret;
    }

    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    bool remove(int val) {
        if (notExist(val) || dict[val].empty()) {
            return false;
        }

        int lastIndex = list.size() - 1;
        if (dict[val].find(lastIndex) != dict[val].end()) {
            dict[val].erase(lastIndex);
            list.pop_back();
        } else {
            int index = *dict[val].begin();
            dict[val].erase(index);
            int lastValue = list[lastIndex];
            list[index] = lastValue;
            list.pop_back();
            dict[lastValue].insert(index);
            dict[lastValue].erase(lastIndex);
        }

        return true;
    }

    /** Get a random element from the collection. */
    int getRandom() {
        return list[rand() % list.size()];
    }

private:
    bool notExist(int val) {
        return dict.find(val) == dict.end();
    }

private:
    vector<int> list;
    unordered_map<int, unordered_set<int>> dict;
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
