> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/design-twitter/)

> 前几天的题目有好几道困难题, 正好今天周末了, 我们就来做一道简单点的设计题吧 😂

> 大家有什么想法建议和反馈的话欢迎随时交流, 包括但不限于公众号聊天框/知乎私信评论等等~

## 题目描述

设计一个简化版的推特(Twitter)，可以让用户实现发送推文，关注/取消关注其他用户，能够看见关注人（包括自己）的最近十条推文。你的设计需要支持以下的几个功能：

- postTweet(userId, tweetId): 创建一条新的推文
- getNewsFeed(userId): 检索最近的十条推文。每个推文都必须是由此用户关注的人或者是用户自己发出的。推文必须按照时间顺序由最近的开始排序。
- follow(followerId, followeeId): 关注一个用户
- unfollow(followerId, followeeId): 取消关注一个用户

## 题目样例

### 示例

```java
Twitter twitter = new Twitter();

// 用户1发送了一条新推文 (用户id = 1, 推文id = 5).
twitter.postTweet(1, 5);

// 用户1的获取推文应当返回一个列表，其中包含一个id为5的推文.
twitter.getNewsFeed(1);

// 用户1关注了用户2.
twitter.follow(1, 2);

// 用户2发送了一个新推文 (推文id = 6).
twitter.postTweet(2, 6);

// 用户1的获取推文应当返回一个列表，其中包含两个推文，id分别为 -> [6, 5].
// 推文id6应当在推文id5之前，因为它是在5之后发送的.
twitter.getNewsFeed(1);

// 用户1取消关注了用户2.
twitter.unfollow(1, 2);

// 用户1的获取推文应当返回一个列表，其中包含一个id为5的推文.
// 因为用户1已经不再关注用户2.
twitter.getNewsFeed(1);
```

## 题目思考

1. 根据题目需求, 我们要用到哪些数据结构?
2. 检索最近的十条推文, 这个有没有想到之前用到过的一些思路?

## 解决方案

### 思路

1. 先设计需要使用的数据结构
   - 题目需要有每个用户的推文和关注关系, 那么至少需要有两个数据结构. 这里显然需要用两个字典/hash 表, 一个存用户到其发的推文的映射关系(`{userid:[tweets]}`), 另一个存粉丝到被关注者之间的关注关系(`{follower:[followees]}`).
   - 另外注意需要按时间排序, 虽然针对同一用户来说, 其推文加入推文列表的顺序自然就是时间顺序, 但是如果考虑多个用户时, 我们就必须加入额外的时间戳来比较不同用户之间的推文前后关系, 所以需要维护一个当前时刻, 同时推文内容里也要带上这一时刻.
2. 接下来就是写具体的逻辑了, 这里一共有 4 种操作:
   1. **创建新推文**: 直接追加到用户对应的推文列表即可, 然后时间戳+1
   2. **关注用户**: 直接更新关注关系字典即可, 注意关注者和被关注者相同的边界情况
   3. **取消关注用户**: 直接更新关注关系字典即可, 注意关注者和被关注者相同的边界情况
   4. **检索最近的 10 条推文**: 这个操作最复杂, 我们放最后解释.
      1. 首先我们可以拿到当前用户自身和所有关注者的推文列表, 直接从第一个字典中拿即可.
      2. 然后检索最近的 10 条, 这就是经典的对 k 个有序列表/链表排序的简化版. 有兴趣的同学可以尝试[23. 合并 K 个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)
         1. 朴素思路就是维护一个下标列表, 代表每个用户的当前推文下标, 初始化为各个推文列表长度-1, 因为我们要求的是最近的推文. 然后每次把这些推文中时间最新的加入结果集合中, 同时更新对应的下标-1 即可, 直到结果集合中有 10 条推文, 或者不到 10 条但是没有更多的推文为止.
         2. 还有一种思路就是使用优先队列/最大堆来保存当前最近的推文, 使用三元组(time, userindex, tweetindex), 每次 pop 时间最新的, 然后把对应的 userindex 的 tweetindex-1 (如果大于等于 0 的话) 再加入队列中. 这样可以将时间从 10\*n 优化到 10\*log(n) (n 表示当前用户关注的用户个数+1). 虽然对于此题优化不明显, 但是当关注用户数目很多时这个优化就很必要了.
3. 最后就是具体的代码部分了, 下面代码针对查询最近 10 条推文分为两种方法: 朴素思路版和优先队列/最大堆思路版.

### 复杂度

- 时间复杂度: 对当前用户的 n 个(关注用户+自身)查询最近 m 条的复杂度是 O(m\*n)或者 O(m\*log(n)). 因为至少要循环 m 次, 而对于每条推文, 要么遍历 n 个数组, 要么利用优先队列/最大堆 log(n)得到最新的推文(因为优先队列/最大堆里的元素数目最多是 n). 其他操作都是 O(1)
- 空间复杂度 O(N+M): 需要存 N 条推文和 M 个用户关注关系

### 代码

#### 方法 1: 朴素思路求最近 10 条推文

##### Python 3

```python
class Twitter:
    def __init__(self):
        """
        Initialize your data structure here.
        """
        # 方法1: 使用朴素思路求最近10条推文
        # 维护关注列表和每个人的发推列表即可
        # 发推时候记录时间
        # 然后getfeed时候使用index数组找最近的10个即可
        # 注意关注者和被关注者是同一人的无效情况
        self.following = defaultdict(set)
        self.tweets = defaultdict(list)
        self.time = 0

    def postTweet(self, userId: int, tweetId: int) -> None:
        """
        Compose a new tweet.
        """
        self.tweets[userId].append((self.time, tweetId))
        self.time += 1

    def getNewsFeed(self, userId: int) -> List[int]:
        """
        Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent.
        """
        tweets = [self.tweets[userId]
                  ] + [self.tweets[u] for u in self.following[userId]]
        res = []
        index = [len(t) - 1 for t in tweets]
        while len(res) < 10:
            mxui, mxtime, mxTweet = None, -float('inf'), None
            for ui in range(len(index)):
                ti, t = index[ui], tweets[ui]
                if ti >= 0:
                    time, tweet = t[ti]
                    if time > mxtime:
                        mxui, mxtime, mxTweet = ui, time, tweet
            if mxui is None:
                break
            else:
                res.append(mxTweet)
                index[mxui] -= 1
        return res

    def follow(self, followerId: int, followeeId: int) -> None:
        """
        Follower following a followee. If the operation is invalid, it should be a no-op.
        """
        if followeeId == followerId: return
        self.following[followerId].add(followeeId)

    def unfollow(self, followerId: int, followeeId: int) -> None:
        """
        Follower unfollows a followee. If the operation is invalid, it should be a no-op.
        """
        if followeeId == followerId: return
        if followeeId in self.following[followerId]:
            self.following[followerId].remove(followeeId)
```

##### C++

```cpp
class Twitter {
public:
    /** Initialize your data structure here. */
    Twitter() {
    }

    /** Compose a new tweet. */
    void postTweet(int userId, int tweetId) {
        tweets[userId].push_back(make_pair(time++, tweetId));
    }

    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    vector<int> getNewsFeed(int userId) {
        vector<int> userIds({userId});
        if (following.find(userId) != following.end()) {
            userIds.insert(userIds.begin(), following[userId].begin(), following[userId].end());
        }

        vector<int> index(userIds.size());
        for (int i = 0; i < userIds.size(); ++i) {
            index[i] = tweets[userIds[i]].size() - 1;
        }

        vector<int> res;
        while (res.size() < 10) {
            int mxi = 0, mxtime = INT_MIN, mxTweet = 0;
            for (int i = 0; i < index.size(); ++i) {
                int idx = index[i];
                if (idx >= 0) {
                    int ui = userIds[i];
                    int time = tweets[ui][idx].first;
                    if (time > mxtime) {
                        mxi = i;
                        mxtime = time;
                        mxTweet = tweets[ui][idx].second;
                    }
                }
            }

            if (mxtime == INT_MIN) {
                break;
            }

            --index[mxi];
            res.push_back(mxTweet);
        }

        return res;
    }

    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    void follow(int followerId, int followeeId) {
        if (followerId != followeeId) {
            following[followerId].insert(followeeId);
        }
    }

    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    void unfollow(int followerId, int followeeId) {
        if (following.find(followerId) == following.end()) {
            return;
        }
        following[followerId].erase(followeeId);
        if (following[followerId].empty()) {
            following.erase(followerId);
        }
    }

private:
    int time {0};
    unordered_map<int, unordered_set<int>> following;
    unordered_map<int, vector<pair<int, int>>> tweets;
};
```

#### 方法 2: 优先队列/最大堆思路求最近 10 条推文

##### Python 3

```python
import heapq


class Twitter:
    def __init__(self):
        """
        Initialize your data structure here.
        """
        # 方法2: 思路同方法1, 使用优先队列求最近10条推文
        self.following = defaultdict(set)
        self.tweets = defaultdict(list)
        self.time = 0

    def postTweet(self, userId: int, tweetId: int) -> None:
        """
        Compose a new tweet.
        """
        self.tweets[userId].append((self.time, tweetId))
        self.time += 1

    def getNewsFeed(self, userId: int) -> List[int]:
        """
        Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent.
        """
        tweets = [self.tweets[userId]
                  ] + [self.tweets[u] for u in self.following[userId]]
        res = []
        index = [len(t) - 1 for t in tweets]
        q = []
        for ui, ti in enumerate(index):
            # ui表示第ui个用户的推文列表
            # ti表示对应推文中的第ti条推文(0是time, 1是具体内容)
            # 注意需要最大堆, 所以time取相反数存入heapq中
            # 注意某些用户的推文列表可能为空, 需要判断这种情况
            if ti >= 0:
                heapq.heappush(q, (-tweets[ui][ti][0], ui, ti))
        while q and len(res) < 10:
            _, mxui, mxti = heapq.heappop(q)
            res.append(tweets[mxui][mxti][1])
            if mxti - 1 >= 0:
                mxti -= 1
                heapq.heappush(q, (-tweets[mxui][mxti][0], mxui, mxti))
        return res

    def follow(self, followerId: int, followeeId: int) -> None:
        """
        Follower following a followee. If the operation is invalid, it should be a no-op.
        """
        if followeeId == followerId: return
        self.following[followerId].add(followeeId)

    def unfollow(self, followerId: int, followeeId: int) -> None:
        """
        Follower unfollows a followee. If the operation is invalid, it should be a no-op.
        """
        if followeeId == followerId: return
        if followeeId in self.following[followerId]:
            self.following[followerId].remove(followeeId)
```

##### C++

```cpp
class Twitter {
public:
    using Node = tuple<int, int, int>;

    /** Initialize your data structure here. */
    Twitter() {
    }

    /** Compose a new tweet. */
    void postTweet(int userId, int tweetId) {
        tweets[userId].push_back(make_pair(time++, tweetId));
    }

    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    vector<int> getNewsFeed(int userId) {
        vector<int> userIds({userId});
        if (following.find(userId) != following.end()) {
            userIds.insert(userIds.begin(), following[userId].begin(), following[userId].end());
        }

        auto node_cmp = [](Node &a, Node &b) { return get<0>(a) < get<0>(b); };
        priority_queue<Node, vector<Node>, decltype(node_cmp)> p(node_cmp);

        vector<int> index(userIds.size(), -1);
        for (int i = 0; i < userIds.size(); ++i) {
            int size = tweets[userIds[i]].size();
            if (size > 0) {
                index[i] = size - 1;
                auto &tweet = tweets[userIds[i]][index[i]];
                p.push(make_tuple(tweet.first, tweet.second, i));
            }
        }

        vector<int> res;

        while (!p.empty() && res.size() < 10) {
            auto node = p.top();
            p.pop();
            res.push_back(get<1>(node));
            int i = get<2>(node);
            if (index[i] >= 1) {
                --index[i];
                auto &tweet = tweets[userIds[i]][index[i]];
                p.push(make_tuple(tweet.first, tweet.second, i));
            }
        }

        return res;
    }

    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    void follow(int followerId, int followeeId) {
        if (followerId != followeeId) {
            following[followerId].insert(followeeId);
        }
    }

    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    void unfollow(int followerId, int followeeId) {
        if (following.find(followerId) == following.end()) {
            return;
        }
        following[followerId].erase(followeeId);
        if (following[followerId].empty()) {
            following.erase(followerId);
        }
    }

private:
    int time{0};
    unordered_map<int, unordered_set<int>> following;
    unordered_map<int, vector<pair<int, int>>> tweets;
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
