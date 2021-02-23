> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

> 今天继续更新剑指 offer 系列, 这道题挺有意思的, 需要反其而行之, 这里依然提供两种方案供大家参考, 特别是方法 2, 对空间复杂度做了优化, 值得一读

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。

- 0 <= pushed.length == popped.length <= 1000
- 0 <= pushed[i], popped[i] < 1000
- pushed  是  popped  的排列。

## 题目样例

### 示例 1

#### 输入

pushed = [1,2,3,4,5], popped = [4,5,3,2,1]

#### 输出

true

#### 解释

我们可以按以下顺序执行：
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1

### 示例 2

#### 输入

pushed = [1,2,3,4,5], popped = [4,3,5,1,2]

#### 输出

false

#### 解释

1 不能在 2 之前弹出。

## 题目思考

1. 直接模拟整个过程可行吗?
2. 有没有不使用额外空间的做法?

## 解决方案

### 方案 1

#### 思路

- 我们可以尝试使用一个栈模拟整个过程, 看看模拟过程中能否满足两个序列的顺序
- 对于每个压入序列, 我们直接将其压入栈中
- 而对于弹出序列, 我们需要额外维护一个指针, 指向当前要弹出的元素下标, 显然该指针初始化为 0
- 接下来比较栈顶是否和当前指针指向的元素是否相同, 相同的话弹出并让指针后移一位
- 重复上述过程直到遍历完整个压入序列
- 那么如果两个序列匹配的话, 最终的栈应该为空的.
  - 注意这里无需判断弹出序列是否也遍历完(即指针指向最后一个元素后一位), 因为如果栈为空的话代表每个 push 都对应一个 pop, 而两个序列长度相等, 所以此时弹出序列肯定也遍历完了.
- 而如果最终的栈不为空, 则说明弹出序列不对了, 卡在了某个下标处, 本应该先弹当前栈顶的, 结果弹出序列的当前元素不是栈顶, 无法正常弹出.

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个元素只会被压入和弹出栈一次
- 空间复杂度 `O(N)`
  - 额外需要一个栈

#### 代码

```python
class Solution:
    def validateStackSequences(self, pushed: List[int],
                               popped: List[int]) -> bool:
        # 方法1: 利用栈模拟, 额外使用一个指针存储当前弹出序列位置
        stack = []
        j = 0
        for p in pushed:
            stack.append(p)
            while stack and j < len(popped) and stack[-1] == popped[j]:
                # 当前栈顶恰好等于当前弹出序列对应的元素, 栈弹出, 指针后移
                stack.pop()
                j += 1
        # 如果栈为空则说明这两个序列有效
        return not stack
```

### 方案 2

#### 思路

- 回顾方案 1, 我们需要额外一个栈来老老实实模拟整个过程, 有没有不需要额外空间的做法呢?
- 答案也是肯定的, 方案 1 中的栈的目的就是模拟压入弹出过程, 整个遍历一遍压入队列, 我们其实可以直接利用压入队列来作为这个栈, 然后栈顶就用新的一个指针来代替即可, 这样就不需要额外空间了
- 其他思路和方案 1 相同, 入栈相当于当前压入队列指针的赋值+后移, 而出栈则简单的等同于压入队列指针的前移
- 最终就是判断栈顶指针是否为-1 来判断栈是不是空

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个元素只会被压入和弹出栈一次
- 空间复杂度 `O(1)`
  - 使用常数个变量

#### 代码

```python
class Solution:
    def validateStackSequences(self, pushed: List[int],
                               popped: List[int]) -> bool:
        # 方法2: O(1)空间, 将pushed本身当作栈, 用一个下标表示当前栈顶, 双指针
        topindex, j = -1, 0
        for i in range(len(pushed)):
            # 压入新元素, 所以需要先将栈顶指针后移一位, 然后赋值
            topindex += 1
            pushed[topindex] = pushed[i]
            while j < len(popped) and topindex >= 0 and pushed[
                    topindex] == popped[j]:
                # 当前栈顶恰好等于当前弹出序列对应的元素, 栈顶指针前移, 弹出队列指针后移
                topindex -= 1
                j += 1
        # 如果栈顶指针为-1, 说明栈为空, 也即这两个序列有效
        return topindex == -1
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
