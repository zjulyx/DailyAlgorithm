> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/IDBivT/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

正整数 n 代表生成括号的对数，请设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

### 示例 1：

- 输入：n = 3
- 输出：["((()))","(()())","(())()","()(())","()()()"]

### 示例 2：

- 输入：n = 1
- 输出：["()"]

### 提示：

- 1 <= n <= 8

## 题目思考

1. 如何保证生成的括号对是合法的组合?

## 解决方案

- 分析并观察题目用例, 不难发现 n 对括号的合法组合有以下两个特征:
  1. 左括号和右括号数目都恰好为 n
  2. 每个右括号作为结尾的前缀, 其右括号数目总是小于等于左括号数目 (不然就会有单独多出来的右括号无法与前面的左括号匹配)
- 根据以上两个特征, 我们可以采用递归回溯的思路, 具体如下:
  1. 记录当前左括号和右括号的数目, 以及当前生成的字符串
  2. 判断接下来是否可以再添加一个左括号或右括号:
     1. 左括号数目小于 n 时可以添加左括号;
     2. 左括号数目大于右括号数目时可以添加右括号 (保证添加的右括号可以与前面的一个未匹配的左括号匹配)
  3. 最后当左右括号数目都达到 n 时说明找到一个合法组合, 将其加入最终结果列表
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(2^N)`: 对于每个左/右括号数目, 都需要两次递归调用其中一个数目加 1 的函数, 这里可以将其视为一个高度为 N 的满二叉树, 所以总时间为 2^N
- 空间复杂度 `O(N)`: 递归栈的消耗, 最大递归深度为 N

#### 代码

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        # 回溯, 使用左右两个括号的数目标记当前状态
        res = []

        def dfs(leftCnt, rightCnt, cur):
            if leftCnt == n and rightCnt == n:
                # 左右括号数目都达到n, 说明找到了一个有效组合
                # 由于前面递归的每次选择都各不相同, 所以这里生成的组合也是唯一的, 自动做到了去重
                res.append(cur)
                return
            if leftCnt < n:
                # 当左括号数目不超过n时, 可以追加左括号
                dfs(leftCnt + 1, rightCnt, cur + "(")
            if leftCnt > rightCnt:
                # 当左括号数目大于右括号时, 说明前面有尚未匹配的右括号, 可以追加右括号
                dfs(leftCnt, rightCnt + 1, cur + ")")

        dfs(0, 0, "")
        return list(res)
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
