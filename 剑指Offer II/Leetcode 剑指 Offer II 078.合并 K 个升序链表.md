> 题目难度: 困难

> [原题链接](https://leetcode.cn/problems/vvXgSW/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

给定一个链表数组，每个链表都已经按升序排列。

请将所有链表合并到一个升序链表中，返回合并后的链表。

### 示例 1：

- 输入：lists = [[1,4,5],[1,3,4],[2,6]]
- 输出：[1,1,2,3,4,4,5,6]
- 解释：链表数组如下：

```
[
  1->4->5,
  1->3->4,
  2->6
]
```

将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6

### 示例 2：

- 输入：lists = []
- 输出：[]

### 示例 3：

- 输入：lists = [[]]
- 输出：[]

### 提示：

- k == lists.length
- 0 <= k <= 10^4
- 0 <= lists[i].length <= 500
- -10^4 <= lists[i][j] <= 10^4
- lists[i] 按 升序 排列
- lists[i].length 的总和不超过 10^4

## 题目思考

1. 如何尽可能优化时间复杂度?

## 解决方案

#### 思路

- 分析题目, 一个很容易想到的思路就是按顺序依次合并两个有序链表: 维护当前已合并的链表链表, 初始化为空, 然后遍历每个链表, 将它与当前已合并链表合并
- 不过这样已合并的节点会被重复多次遍历, 假设共有 K 个链表, 每个链表平均节点数为 N, 那么时间复杂度就是`N+2N+3N+...+KN=O(NK^2)`,如何优化呢?
- 回顾经典的归并排序算法, 同样是合并有序部分, 它利用分治的方法, 将排序时间复杂度优化到了 O(NlogN)
- 这里只是将数组换成了链表, 我们同样可以利用类似的方法进行优化
- 具体做法如下:
  - 取当前待合并的链表部分的起点 s 和终点 e, 初始传入的自然是 0 和 k-1
  - 然后计算其区间中点 m, 递归合并左右部分`[s,m]`和`[m+1,e]`
  - 此时得到两个有序链表, 分别代表左右部分, 将其利用经典的双有序链表合并算法合并即可, 这个和上道题([剑指 Offer II 077.排序链表](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484785&idx=1&sn=e789e9e254b83d97dbb720ebbc1f708c&token=1176311268&lang=zh_CN#rd))的 mergeTwo 函数很类似, 只是不需要返回归并后的链表尾
  - 另外当`s>=e`时是递归出口, 返回对应单个节点(`s==e`时)或空(`s>e`时)
- 下面代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 O(NKlogK): 假设共有 K 个链表, 每个链表平均节点数为 N. 第一次归并 K/2 次, 每对有 2N 个节点, 得到 K/2 个链表; 第二次归并 K/4 次, 每对有 4N 个节点, 得到 K/8 个链表. 依此类推, 共需要归并 logK 次. 然后每次归并的时间复杂度都是 KN, 所以整体时间复杂度就是 O(NKlogK)
- 空间复杂度 O(logK): 递归栈的空间消耗

#### 代码

```python
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        # 分治归并
        def mergeTwo(head1, head2):
            # 将链表head1和head2进行归并排序
            # 使用哨兵节点简化处理
            dummy = ListNode(0)
            cur = dummy
            while head1 or head2:
                if not head2 or head1 and head1.val <= head2.val:
                    # head2为空, 或者head1的值更小, 追加head1
                    cur.next = head1
                    # head1向后移动
                    head1 = head1.next
                else:
                    # 此时只能追加head2
                    cur.next = head2
                    # head2向后移动
                    head2 = head2.next
                # 当前节点向后移动
                cur = cur.next
            # 返回归并排序后的链表头
            return dummy.next

        def merge(s, e):
            if s > e:
                # 递归出口1: 区间为空, 返回None
                return None
            if s == e:
                # 递归出口2: 区间只有一个链表, 返回它
                return lists[s]
            # 求当前区间中点
            m = (s + e) >> 1
            # 递归得到左右两区间合并后的结果
            left = merge(s, m)
            right = merge(m + 1, e)
            # 最后再调用mergeTwo合并得到的两个链表
            return mergeTwo(left, right)

        # 初始传入整个区间进行归并
        return merge(0, len(lists) - 1)
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
