> 题目难度: 中等

> [原题链接](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)

> 今天继续更新剑指 offer 系列, 这道题虽然难度是中等, 但通过率巨低, 应该是边界情况比较多的原因. 而各种边界条件也是面试比较爱考察的部分, 所以很值得一做

> 老样子**晚上 6 点 45 分**准时更新公众号 **每日精选算法题**, 大家记得关注哦~ 另外在公众号里回复 **offer** 就能看到剑指 offer 系列当前连载的所有文章了

## 题目描述

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100"、"5e2"、"-123"、"3.1416"、"0123"都表示数值，但"12e"、"1a3.14"、"1.2.3"、"+-5"、"-1E-16"及"12e+5.4"都不是。

## 题目样例

### 示例

```
"0" => true
" 0.1 " => true
"abc" => false
"1 a" => false
"2e10" => true
" -90e3   " => true
" 1e" => false
"e3" => false
" 6e-1" => true
" 99e2.5 " => false
"53.5e93" => true
" --6 " => false
"-+3" => false
"95a54e53" => false
```

## 题目思考

1. 如何高效地处理各类情况?
2. 边界情况分为几类?

## 解决方案

### 思路

#### 分析

- 首先确定字符串中允许有哪些字符, 以及各自需要满足什么条件 (如果面试遇到这种问题, 在模糊的地方一定要多沟通细节, 比如前导 0, 小数点位置等):
  - 数字
    - 数量没有上限, 但至少需要有 1 个
    - 无需处理前导 0 (因为题目用例里 0123 是有效的)
  - `e`
    - 只能有 1 个
    - 不能是大写 E
    - **左右都必须有至少一个数字**
  - `+/-`
    - `e` 的两边可以每边最多出现 1 个
    - 都必须在开头
  - `.`
    - 只能有 1 个
    - 只能出现在 `e` 之前
    - 可以出现在开头符号之后或数字之间
  - 空格
    - 只能出现在开头和末尾
- 根据上面分析, 一个自然的思路就是按照 `e` 将数字分为左右两部分, 其中右边唯一要求是不能有小数点, 别的和左边一样
- 这样我们就可以将分析数字的部分提取成一个方法, 额外传入一个 flag 表示是否可以有小数点, 然后分左右两部分判断即可~ 当然如果没有 `e` 的话那直接判断一次就行

#### 实现

- 下面代码完全基于上述分析实现, 必要步骤都有详细注释

### 复杂度

- 时间复杂度 `O(N)`
  - 只需要对每个字符判断一次
- 空间复杂度 `O(1)`
  - 只使用了常数个变量

### 代码

```python
class Solution:
    def isNumber(self, s: str) -> bool:
        # 先要把首尾空格去掉
        # 然后判断是否有e
        # 有的话分成左右两部分check, 左边可以有小数, 右边不能有
        # 注意小数点可以位于任意位置..
        # 注意e之后不能有小数点
        # 注意每一部分判断时需要保证至少有一个数字
        s = s.strip()

        def isValid(s, allowdot):
            hasNum = False
            hasDot = False
            for i, c in enumerate(s):
                if c == '+' or c == '-':
                    # 先判断符号, 如果不是在开头, 直接返回False
                    if i != 0:
                        return False
                elif c == '.':
                    if not allowdot:
                        # 当前不能有小数点, 返回False
                        return False
                    if hasDot:
                        # 已经有一个小数点了, 返回False
                        return False
                    hasDot = True
                elif c.isnumeric():
                    # 注意这里至少需要满足有一个数字
                    hasNum = True
                else:
                    # 其他字符都不满足, 直接返回False (包括e, 因为e只允许有一个, 有的话已经作为分隔符用掉了)
                    return False
            return hasNum

        e = s.find('e')
        if e == -1:
            # 当前字符串没有e, 直接判断整体即可, 也允许小数点
            return isValid(s, True)
        else:
            # 分为左右部分分别判断, 左边可以有小数点, 右边不行
            return isValid(s[0:e], True) and isValid(s[e + 1:], False)
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> [我的牛客网博客](https://blog.nowcoder.net/zjulyx)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
