> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/Qv1Da2/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

- 多级双向链表中，除了指向下一个节点和前一个节点指针之外，它还有一个子链表指针，可能指向单独的双向链表。这些子列表也可能会有一个或多个自己的子项，依此类推，生成多级数据结构，如下面的示例所示。

- 给定位于列表第一级的头节点，请扁平化列表，即将这样的多级双向链表展平成普通的双向链表，使所有结点出现在单级双链表中。

### 示例 1：

- 输入：head = [1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]
- 输出：[1,2,3,7,8,11,12,9,10,4,5,6]
- 解释：

  - 输入的多级列表如下图所示：
  - ![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/multilevellinkedlist.png)

  - 扁平化后的链表如下图：
  - ![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/multilevellinkedlistflattened.png)

### 示例 2：

- 输入：head = [1,2,null,3]
- 输出：[1,3,2]
- 解释：
  - 输入的多级列表如下图所示：
    ```
    1---2---NULL
    |
    3---NULL
    ```

### 示例 3：

- 输入：head = []
- 输出：[]

### 如何表示测试用例中的多级链表？

- 以 示例 1 为例：

```
 1---2---3---4---5---6--NULL
         |
         7---8---9---10--NULL
             |
             11--12--NULL
```

- 序列化其中的每一级之后：
  - [1,2,3,4,5,6,null]
  - [7,8,9,10,null]
  - [11,12,null]
- 为了将每一级都序列化到一起，我们需要每一级中添加值为 null 的元素，以表示没有节点连接到上一级的上级节点。
  - [1,2,3,4,5,6,null]
  - [null,null,7,8,9,10,null]
  - [null,11,12,null]
- 合并所有序列化结果，并去除末尾的 null 。
  - [1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]

### 提示：

- 节点数目不超过 1000
- 1 <= Node.val <= 10^5

## 题目思考

1. 如何处理某个节点有多层子节点的情况？

## 解决方案

#### 思路

- 分析题目, 可以发现如果某个节点有子节点, 则子节点最终展平后的头尾会插入到父节点和父节点的下个节点之间
- 所以很自然地可以想到用递归来做
- 具体就是递归函数返回展平后的头尾节点, 然后将头与父节点相连, 尾与父节点的下个节点相连
- 举个例子, 假如父节点是 cur, 它的下个节点是 nex, 它的子节点展平后的头尾是 ch 和 ct
- 那么新的连接是 `cur<->ch<->...<->ct<->nex`
- 另外在递归函数中, 应该特别注意空节点的处理, 以及尾节点的赋值, 具体可以参考下面的代码和注释

#### 复杂度

- 时间复杂度 O(N): 只需要遍历每个节点常数次
- 空间复杂度 O(H): H 是子节点的最大深度, 也即递归栈使用的空间

#### 代码

```python
class Solution:
    def flatten(self, head: "Node") -> "Node":
        def flat(head):
            if not head:
                # 空节点, flat后的头尾都是空
                return None, None
            cur = head
            tail = head
            while cur:
                # 先保存下一个节点
                nex = cur.next
                if cur.child:
                    # 存在子节点, 先递归展平它, 得到子节点展平后的头和尾
                    ch, ct = flat(cur.child)
                    # 注意展平后子节点要置为空, 否则不是有效的双向链表!!!
                    cur.child = None
                    # 然后重新建立连接, 即cur->ch->...->ct->nex
                    # 连接当前节点和子头
                    cur.next = ch
                    ch.prev = cur
                    # 连接子尾和下一个节点
                    ct.next = nex
                    if nex:
                        # 注意下一个节点可能为空, 这里需要额外判断!!!
                        nex.prev = ct
                    # 有子节点, tail设置为子节点的尾ct (因为下一个节点可能为空)
                    tail = ct
                else:
                    # 没有子节点, 则tail设置为cur
                    tail = cur
                cur = nex
            return head, tail

        return flat(head)[0]
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
