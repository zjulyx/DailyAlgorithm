> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某班级学号记录系统发生错乱，原整数学号序列 [1,2,3,4,...] 分隔符丢失后变为 1234... 的字符序列。请实现一个函数返回该字符序列中的第 k 位数字。

### 示例 1：

- 输入：k = 5
- 输出：5

### 示例 2：

- 输入：k = 12
- 输出：1
- 解释：第 12 位数字在序列 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... 里是 1 ，它是 11 的一部分。

### 提示：

- 0 <= k < 2^31

## 题目思考

1. 能否找到什么规律?

## 解决方案

### 思路

- 观察序列本身:
  - 0~9 只占 1 位, 共 10 个
  - 10~99 占 2 位, 共 90 个
  - 100~999 占 3 位, 共 900 个
  - 1000~9999 占 4 位, 共 9000 个
  - ...
- **为了保持一致, 我们可以将 1 位的时候改成从 1 开始, 把 k=0 作为特殊情况, 这样 1 位也只有 9 个, 即 1~9**
- 这样我们就很容易发现规律, 假设当前位数是 le:
  - 每一位的开始值 start 是`pow(10, le-1)`
  - 每一位的数字数目是`9*start`
  - 每一位的字符总数则是`9*start*le` (因为每个数字有 le 个字符)
- 所以我们可以逐渐增加位数, 统计当前总共的字符个数
- 假设 le 位数下的字符总数是 cnt, le+1 下的字符总数是 nextcnt, 那么如果 k 在`[cnt, nextcnt)`范围内, 那就说明 k 落在的数字一定有 le 位
- 这样一来, 我们只需要定位 k 具体对应到的数字, 以及所在数字的第几位即可
- 而由于每个数字占有 le 位, 所以 k 对应的数字就是 `start+(k-cnt)/le`, 而具体 k 是在该数字的第几位, 则是`(k-cnt)%le`
- 下面的代码对必要步骤有详细的解释, 方便大家理解

### 复杂度

- 时间复杂度 `O(logN)`
  - 只需要按位数遍历即可, k 的位数是 logN
- 空间复杂度 `O(1)`
  - 只使用了常数个变量

### 代码

```python
class Solution:
    def findKthNumber(self, k: int) -> int:
        if k == 0:
            return 0
        # 初始化计数值为1, 因为start最开始是1, 此时已经有1个字符了
        cnt = 1
        # 初始化位数为1位
        le = 1
        while k >= cnt:
            # 求当前位数下的start
            start = 10**(le - 1)
            # 求当前位数+1情况下的字符总数
            nexcnt = cnt + 9 * start * le
            if k <= nexcnt:
                # 当前n落在范围内, 找对应的数字和该数字中n对应的位(偏移量)
                i, offset = divmod(k - cnt, le)
                num = start + i
                # 将数字转成字符串, 其偏移量下标对应的位即为所求
                return int(str(num)[offset])
            # 更新字符总数, 同时位数加1, 继续循环
            cnt = nexcnt
            le += 1
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
