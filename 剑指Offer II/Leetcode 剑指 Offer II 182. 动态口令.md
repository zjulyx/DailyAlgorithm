> 题目难度: 简单

> [原题链接](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某公司门禁密码使用动态口令技术。初始密码为字符串 password，密码更新均遵循以下步骤：

设定一个正整数目标值 target
将 password 前 target 个字符按原顺序移动至字符串末尾
请返回更新后的密码字符串。

### 示例 1：

- 输入: password = "s3cur1tyC0d3", target = 4
- 输出: "r1tyC0d3s3cu"

### 示例 2：

- 输入: password = "lrloseumgh", target = 6
- 输出: "umghlrlose"

### 提示：

- 1 <= target < password.length <= 10000

## 题目思考

1. 如果要求不能使用语言内置函数, 该如何做?

## 解决方案

### 方案 1

#### 思路

- 根据题目描述, 使用 python 的话完全可以直接利用切片, 一行代码来搞定..

#### 复杂度

- 时间复杂度 O(N): 切片的时间复杂度
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def dynamicPassword(self, password: str, target: int) -> str:
        # 切片
        return password[target:] + password[0:target]
```

### 方案 2

#### 思路

- 如果要求不能使用语言内置函数, 又该怎么做呢?
- 很显然我们可以从下标为 k 的地方开始遍历, 将后面的字符依次加入结果中
- 遍历到末尾后, 再从 0 开始遍历到 k, 将这部分字符再加入结果中即可
- 这种方案跟语言无关, 比较通用, 只是通常情况下没必要, 因为内置操作会有一些优化, 一般会比自己实现的效率高..

#### 复杂度

- 时间复杂度 O(N): 每个字符只遍历一次
- 空间复杂度 O(1): 只使用了几个变量

#### 代码

```python
class Solution:
    def dynamicPassword(self, password: str, target: int) -> str:
        res = ''
        for i in range(target, len(password)):
            res += password[i]
        for i in range(target):
            res += password[i]
        return res
```

---

> 大家可以在下面这些地方找到我~😊

> [我的 GitHub](https://github.com/zjulyx)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的头条号](https://www.toutiao.com/c/user/1090304683804520/#mid=1671643017345028)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: [算法精选](https://mp.weixin.qq.com/message?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
