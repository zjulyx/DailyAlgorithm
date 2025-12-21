> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

请实现一个函数来判断整数数组 postorder 是否为二叉搜索树的后序遍历结果。

### 示例 1：

![](https://pic.leetcode.cn/1706665328-rfvWhs-%E6%88%AA%E5%B1%8F2024-01-31%2009.41.48.png)

- 输入: postorder = [4,9,6,5,8]
- 输出: false
- 解释：从上图可以看出这不是一颗二叉搜索树

### 示例 2：

![](https://pic.leetcode.cn/1694762510-vVpTic-%E5%89%91%E6%8C%8733.png)

- 输入: postorder = [4,6,5,9,8]
- 输出: true
- 解释：可构建的二叉搜索树如上图

### 提示：

- 数组长度 <= 1000
- postorder 中无重复数字

## 题目思考

1. 如何利用二叉搜索树的性质?
2. 如何利用后序遍历的性质?

## 解决方案

### 方案 1

#### 思路

- 先考虑后序遍历的性质: `先左子树, 然后右子树, 最后根节点`
- 然后结合二叉搜索树性质: `左子树的任何值 < 根节点 < 右子树的任何值`
- 所以我们可以把左子树和右子树各当成一个整体, 先验证当前根节点和左右子树的关系, 之后再递归验证左子树和右子树自身, 这就是分治的思想
- 假设当前我们验证的节点区间是`[s,e]`
- 首先是递归出口的设计, 很显然当 `s>=e` 的时候都是满足条件(对应空节点或只有一个节点), 直接返回 true
- 验证当前关系的方法也很简单, 分为下面两步:
  - 从当前第一个节点 s 开始遍历, 找到第一个大于根节点的 m, 那么`[s,m)`之间的节点自然就属于左子树(当然也有可能 s 就大于根节点);
  - 然后继续往下找, 后面的节点`[m,e)`如果满足要求的话, 肯定都是右子树, 也即大于根节点, 那么如果这个过程中有一个小于根节点的节点的话, 那么肯定说明这个序列不满足要求, 直接返回 false 就行
- 如果上面验证的关系都通过的话, 就递归调用判断左子树(`[s,m)`)和右子树(`[m,e)`)是否也满足要求

#### 复杂度

- 时间复杂度 `O(NlogN)`
  - 非常类似快速排序的过程, 根据`T(N) = 2*T(N/2) + N`的递推公式可得, 平均`O(NlogN)`, 最差`O(N^2)`(每次都分为单独一个和剩下的)
- 空间复杂度 `O(N)`
  - 递归栈的深度

#### 代码

```python
class Solution:
    def verifyTreeOrder(self, postorder: List[int]) -> bool:
        def isValid(s, e):
            if s >= e:
                # 递归出口, 空节点或单个节点
                return True
            i = s
            # 先遍历左子树部分
            while postorder[i] < postorder[e]:
                i += 1
            # 当前的i是第一个大于末尾根节点的
            m = i
            while postorder[i] > postorder[e]:
                i += 1
            if i < e:
                # 意味着右子树上发现了一个小于根节点的节点, 直接返回False
                return False
            # 左右子树整体没问题, 接下来进入它们内部检测
            return isValid(s, m - 1) and isValid(m, e - 1)

        return isValid(0, len(postorder) - 1)
```

### 方案 2

#### 思路

- 方案 1 虽然比较容易理解, 但会访问每个节点很多次, 有很多重复的比较, 有没有更好的方案, 可以访问每个节点一遍就能得出结论呢?
- 答案也是有的, 很多这种树的序列问题都可以尝试单调栈的思路 (例如之前的[Leetcode 剑指 Offer II 124. 推理二叉树](https://mp.weixin.qq.com/s/W9uOucdSrI4ZT3r39W5ZuQ)), 这道题也不例外
- 重新观察后序遍历序列, 如果我们倒着遍历, 那么先遍历到的是根节点, 然后是右子树, 最后是左子树
- 我们可以利用这一性质维护一个根节点, **要求其右子树为空或者已经全被遍历过**, 并构建一个单调递增栈, 栈里面存放前面遍历的节点, 然后针对当前节点:
  - **如果它大于当前根节点**, 则不满足根节点右子树为空或全被遍历过的前提条件, 说明序列无效
  - **如果栈为空**, 则说明是树自身的根节点, 它的右子树可能还有未遍历的, 所以先加入栈中
  - **如果它大于栈顶**, 则说明还是栈顶的右子树部分, 直接加入栈中; 同时根节点无需更新, 因为栈顶的右子树还没遍历完, 栈中的元素还不满足根节点的前提条件
  - **如果它小于栈顶**, 就说明进入了栈顶的左子树部分, 此时就需要依次弹出栈顶元素, 直到栈为空或者栈顶小于当前节点, 然后再把当前节点加入栈中, 保持递增性质. 而最后一次弹出的栈顶节点 lastTop 就为新的根节点: 因为更早弹出的节点大于 lastTop, 一定在 lastTop 的右子树; 而当前节点小于 lastTop, 在 lastTop 的左子树上, 也即 lastTop 的右子树已经全被遍历过了
- 另外初始化的时候根节点需要是无穷大, 这样才能保证二叉树自身的根在该节点左子树上
- 最后如果遍历完仍没遇到无效情况, 则说明这个序列有效, 返回 true 即可

#### 复杂度

- 时间复杂度 `O(N)`
  - 每个节点只需要入栈和出栈一遍
- 空间复杂度 `O(N)`
  - 单调栈的大小

#### 代码

```python
class Solution:
    def verifyTreeOrder(self, postorder: List[int]) -> bool:
        stack, root = [], float('inf')
        # 注意需要从后向前遍历
        for p in postorder[::-1]:
            if p > root:
                # 当前节点错误地大于了根节点, 序列无效
                return False
            while stack and stack[-1] > p:
                # 如果当前节点值小于栈顶, 则依次弹出, 并用最后一次弹出的栈顶元素作为新的根节点
                root = stack.pop()
            # 将当前节点加入栈中, 等待后续处理
            stack.append(p)
        return True
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
