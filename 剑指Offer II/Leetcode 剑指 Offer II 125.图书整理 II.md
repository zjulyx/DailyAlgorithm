> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

读者来到图书馆排队借还书，图书管理员使用两个书车来完成整理借还书的任务。书车中的书从下往上叠加存放，图书管理员每次只能拿取书车顶部的书。排队的读者会有两种操作：

- push(bookID)：把借阅的书籍还到图书馆。
- pop()：从图书馆中借出书籍。
- 为了保持图书的顺序，图书管理员每次取出供读者借阅的书籍是 最早 归还到图书馆的书籍。你需要返回 每次读者借出书的值 。

如果没有归还的书可以取出，返回 -1 。

### 示例 1：

- 输入：
  - ["BookQueue", "push", "push", "pop"]
  - [[], [1], [2], []]
- 输出：
  - [null,null,null,1]
- 解释：
  - MyQueue myQueue = new MyQueue();
  - myQueue.push(1); // queue is: [1]
  - myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
  - myQueue.pop(); // return 1, queue is [2]

### 提示：

- 1 <= bookID <= 10000
- 最多会对 push、pop 进行 10000 次调用

## 题目思考

1. 书车对应什么数据结构, 如何利用它们实现要求的操作?

## 解决方案

### 思路

#### 分析

- 书车中的书从下往上叠加存放，图书管理员每次只能拿取书车顶部的书, 这就是典型的栈
- 而题目所需的操作是归还书籍到一端, 然后从另一端借出书籍, 这就要求我们实现一个队列
- 栈的特性是后进先出, 队列的特性是先进先出
- 队列尾部插入很简单, 由于队列尾对应就是最新的元素, 尾部插入就直接压入栈中即可
- 而队列头部删除相对麻烦, 因为单就一个栈而言, 此时队列头是在栈的最下面
- 因此我们需要第二个栈, 逆序存放第一个栈中比较老的数据, 这样其栈顶就对应队列头了
- 每次遇到需要删除队列头时
  - 如果第二个栈中还有数字, 就把其栈顶弹出即可
  - 否则就把第一个栈的所有数字都逆序导入第二个栈中, 然后再弹出第二个栈的栈顶
  - 如果两个栈都没有数字, 就返回-1

#### 实现

- 定义两个栈 stackIn 和 stackOut: 前者对应上面分析的第一个栈, 只用于尾部插入; 后者对应第二个栈, 只用于头部删除
- 尾部插入
  - 无脑压入新数字到 stackIn
- 头部删除
  - 如果 stackOut 不是空, 弹出栈顶
  - 如果 stackOut 是空, 分为两种情况:
    - 如果 stackIn 也是空, 代表队列为空, 返回-1
    - 否则就将 stackIn 的数字倒入 stackOut 中, 再弹出栈顶

### 复杂度

- 时间复杂度
  - 尾部插入, 时间复杂度 `O(1)`
  - 头部删除, 均摊复杂度也是 `O(1)`. 因为第一次删除导入一批后, 之后该批的删除都只需要常数时间的弹出, 分摊下来每个数字的删除都只需要 `O(1)`. 当然如果只删除一次的话, 那还是需要把之前所以插入的都倒过来, 这样就是 `O(N)` 了..
- 空间复杂度 `O(N)`
  - 使用了两个栈

### 代码

```python
class CQueue:

    def __init__(self):
        # 存较新的尾部插入数字
        self.stackIn = []
        # 存较老的逆序数字
        self.stackOut = []

    def appendTail(self, value: int) -> None:
        # 直接压入stackIn
        self.stackIn.append(value)

    def deleteHead(self) -> int:
        if self.stackOut:
            # stackOut还有数字, 直接pop
            return self.stackOut.pop()
        if not self.stackIn:
            # stackIn也没有数字, 队列为空
            return -1
        while self.stackIn:
            # 将stackIn的数字倒序导入stackOut中
            self.stackOut.append(self.stackIn.pop())

        # 弹出stackOut
        return self.stackOut.pop()
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
