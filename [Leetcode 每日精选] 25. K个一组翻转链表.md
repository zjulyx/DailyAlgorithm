> 题目难度: 困难

> [原题链接](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

> 今天这道题又是每日一题, 感觉理解了这道题后大部分链表题就都会做了, 无外乎画图=>模拟=>代码三部曲, 理清楚节点指向要如何变化即可

## 题目描述

给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

## 题目样例

### 示例

给你这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5

## 题目思考

1. 每一组的翻转过程都是一样的, 可以单独把这一过程提取出来吗?
2. 需要维护哪些额外变量?
3. 需要考虑到哪些边界条件?

## 解决方案

### 方案 1

#### 思路

- 提取翻转函数, 传入当前区间开头和结尾后将其翻转并返回新的开头和结尾
- 使用一个计数器, 每 k 个节点调用一次那个翻转函数
- 注意需要额外维护翻转区间开头的上一个节点和结尾的下一个节点, 用于将新的开头和结尾与之前和之后的节点相连
- 注意边界条件 k==1，此时不用翻转
- 代码内有很详细的解释帮助理解~

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历两遍
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        if k <= 1:
            return head

        def reverse(head, tail):
            # 简单地把head->...->tail翻转成tail->...->head
            pre, cur = head, head.next
            while cur != tail:
                nex = cur.next
                cur.next = pre
                pre, cur = cur, nex
            cur.next = pre
            return (tail, head)

        dummy = ListNode(0)
        dummy.next = head
        prehead = dummy
        cur = head
        cnt = 0
        while cur:
            cnt += 1
            if cnt % k == 0:
                # 找到一个长度为k的区间了, 当前区间的结尾就是cur
                # 先存下区间结尾之后的节点nextail
                nextail = cur.next
                # 翻转[head, cur]区间的节点
                newhead, newtail = reverse(head, cur)
                # prehead要指向新的头newhead
                prehead.next = newhead
                # 新的prehead也就是当前区间结尾, 即newtail
                prehead = newtail
                # newtail要指向nextail
                newtail.next = nextail
                # 更新下个区间的head为nextail
                head = nextail
                # 更新cur, 继续遍历
                cur = nextail
            else:
                # 当前区间长度没达到k, 继续遍历
                cur = cur.next
        return dummy.next
```

##### C++

```c++
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (k <= 1) {
            return head;
        }

        auto reverse = [](ListNode* head, ListNode* tail) {
            ListNode* prev = head;
            ListNode* curr = head->next;
            while (curr != tail) {
                ListNode* next = curr->next;
                curr->next = prev;
                prev = curr;
                curr = next;
            }
            curr->next = prev;
            return pair<ListNode*, ListNode*>({tail, head});
        };

        ListNode dummy = ListNode(0);
        ListNode* prev = &dummy;
        ListNode* tail = head;
        int count = 0;

        while (tail) {
            if (++count % k) {
                tail = tail->next;
                continue;
            }

            ListNode* next = tail->next;
            tie(head, tail) = reverse(head, tail);
            prev->next = head;
            tail->next = next;
            prev = tail;
            head = tail = next;
        }

        return dummy.next;
    }
};
```

### 方案 2

#### 思路

- 回顾方案 1, 我们是把翻转当前区间的头和尾单独提取出来, 那如果把当前区间头的前一个元素,以及当前区间尾的后一个元素作为参数传入, 就不需要在遍历的时候多做很多额外的操作了呢
- 方案 2 就是这样的思路, 相比方案 1 而言, 提取出来的 reverse 函数逻辑增加了一些, 但好处是遍历的时候逻辑比较简单干净
- 代码内有很详细的解释帮助理解~

#### 复杂度

- 时间复杂度 O(N): 每个节点只需要遍历两遍
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

##### Python 3

```python
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        if k <= 1:
            return head
        dummy = ListNode(0)
        dummy.next = head

        def reverse(prehead, nextail):
            # 翻转prehead->head->...->tail->nextail为prehead->tail->...->head->nextail
            pre = prehead.next
            # 此处pre一定不可能是None, 因为k长度至少为2
            cur = pre.next
            newtail = pre
            while cur:
                nex = cur.next
                cur.next = pre
                if nex == nextail:
                    # 当前的cur即为当前区间结尾, prehead指向它
                    prehead.next = cur
                    # 当前新结尾要指向nextail
                    newtail.next = nextail
                    break
                pre, cur = cur, nex
            # 返回新的结尾, 用于更新下个区间的prehead
            return newtail

        cnt = 0
        prehead = dummy
        cur = head
        while cur:
            cnt += 1
            if cnt % k == 0:
                # 找到区间了, 传入prehead和nextail
                nextail = cur.next
                # reverse函数返回的是新的区间的结尾, 也即新的prehead
                prehead = reverse(prehead, nextail)
                # 更新cur为新区间结尾的下个节点, 继续遍历
                cur = prehead.next
            else:
                # 当前区间长度没达到k, 继续遍历
                cur = cur.next
        return dummy.next
```

##### C++

```c++
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (k <= 1) {
            return head;
        }

        auto reverse = [](ListNode* prehead, ListNode* nextail) {
            auto pre = prehead->next;
            auto cur = pre->next;
            auto tail = pre;
            while (cur) {
                auto nex = cur->next;
                cur->next = pre;
                if (nex == nextail) {
                    prehead->next = cur;
                    tail->next = nextail;
                    break;
                }
                pre = cur;
                cur = nex;
            }
            return tail;
        };

        auto dummy = ListNode(0);
        dummy.next = head;
        auto prev = &dummy;
        auto curr = head;
        int count = 0;

        while (curr) {
            if (++count % k) {
                curr = curr->next;
                continue;
            }

            auto nextail = curr->next;
            prev = reverse(prev, nextail);
            curr = nextail;
        }

        return dummy.next;
    }
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
