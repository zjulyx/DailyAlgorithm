> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/zlDJc7/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

一个密码锁由 4 个环形拨轮组成，每个拨轮都有 10 个数字： '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' 。每个拨轮可以自由旋转：例如把 '9' 变为 '0'，'0' 变为 '9' 。每次旋转都只能旋转一个拨轮的一位数字。

锁的初始数字为 '0000' ，一个代表四个拨轮的数字的字符串。

列表 deadends 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。

字符串 target 代表可以解锁的数字，请给出解锁需要的最小旋转次数，如果无论如何不能解锁，返回 -1 。

### 示例 1:

- 输入：deadends = ["0201","0101","0102","1212","2002"], target = "0202"
- 输出：6
- 解释：
  - 可能的移动序列为 "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202"。
  - 注意 "0000" -> "0001" -> "0002" -> "0102" -> "0202" 这样的序列是不能解锁的，因为当拨动到 "0102" 时这个锁就会被锁定。

### 示例 2:

- 输入: deadends = ["8888"], target = "0009"
- 输出：1
- 解释：
  - 把最后一位反向旋转一次即可 "0000" -> "0009"。

### 示例 3:

- 输入: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
- 输出：-1
- 解释：
  - 无法旋转到目标数字且不被锁定。

### 示例 4:

- 输入: deadends = ["0000"], target = "8888"
- 输出：-1

### 提示：

- 1 <= deadends.length <= 500
- deadends[i].length == 4
- target.length == 4
- target 不在 deadends 之中
- target 和 deadends[i] 仅由若干位数字组成

## 题目思考

1. 如何求最小旋转次数?
2. 如何处理 deadends?

## 解决方案

- 分析题目, 要求'0000'到 target 的最小旋转次数, 我们可以尝试 BFS, 只是需要额外考虑 deadends:
  - 我们可以维护一个队列 q 记录当前需要遍历的密码, 并使用一个集合 v 记录已经遍历过的密码, 且初始化旋转次数 cnt 为 1
  - 然后从 '0000' 出发, 将其加入队列 q 和集合 v 中
  - 接下来开始 BFS 的每轮遍历: 初始化一个新队列 nq, 对于当前队列 q 的密码, 遍历密码的每个位置的数字 digit, 将其替换成相邻的数字(即 digit-1 和 digit+1, 0 的前一位是 9, 9 的后一位是 0, 所以增减后需要对 10 取模), 从而组成新的密码, 如果某个密码不在已遍历集合 v 中, 且不在 deadends 中, 则将其加入新队列 nq 和集合 v 中, 等待下次遍历
  - 等到当前队列 q 遍历结束后, 旋转数目 cnt 加 1, 并将新队列 nq 赋值给当前队列 q, 开始新一轮的遍历, 直到 q 没有密码为止
  - 在遍历过程中, 如果 q 的密码恰好等于 target, 则说明找到了最短旋转序列, 直接返回当前旋转次数 cnt 即可
  - 如果直到遍历结束都没得到 target, 则说明不存在这样的旋转序列, 返回 -1
- 回顾上述过程, 可以发现: 当我们在判断新的密码是否有效时, 我们需要同时判断它是否在已遍历集合 v 和 deadends 中, 我们完全可以将两者合二为一, 刚开始就把所有 deadends 包含的密码加入 v 集合中, 这样后续只需要判断 v 集合即可
- 下面的代码对必要步骤有详细的解释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N+10^4*4*4)`: N 是 deadends 包含的密码数目, 将其加入 v 集合时需要 `O(N)` 时间, 后续的 BFS 最差情况下需要遍历 4 位 10 进制的所有密码, 然后生成新密码时, 需要遍历每一位, 然后再拼接, 所以这部分是`O(10^4*4*4)`
- 空间复杂度 `O(N+10^4*4)`: 队列和集合占用的空间

#### 代码

```python
class Solution:
    def openLock(self, deadends: List[str], target: str) -> int:
        # 先将deadends转成visit集合, 这里面的数字不能被遍历
        v = set(deadends)
        start = "0000"
        if start in v:
            # 起点属于deadends, 无法旋转
            return -1

        def getNext(s):
            # 返回s可以旋转得到的数字
            for i in range(len(s)):
                cur_digit = int(s[i])
                for nex_digit in ((cur_digit - 1 + 10) % 10, (cur_digit + 1) % 10):
                    # 当前位可以减1, 也可以加1, 注意0减1得到9, 9加1得到0, 需要对10取模
                    yield s[:i] + str(nex_digit) + s[i + 1 :]

        # 按层BFS
        q = [start]
        v.add(start)
        # 初始化旋转次数为0
        cnt = 0
        while q:
            nq = []
            for cur in q:
                if cur == target:
                    # 找到target了, 返回当前cnt, 它一定是最小旋转次数
                    return cnt
                for nex in getNext(cur):
                    if nex not in v:
                        # 如果nex尚未遍历, 则可以遍历它, 将其加入visit集合和新的队列中, 等待后续处理
                        v.add(nex)
                        nq.append(nex)
            cnt += 1
            q = nq
        # 尝试了所有旋转都没法得到target, 返回-1
        return -1
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
