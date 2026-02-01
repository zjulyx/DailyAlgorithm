> 题目难度: 中等

> [原题链接](https://leetcode.cn/problems/zi-fu-chuan-de-pai-lie-lcof/description/)

> 今天继续更新 Leetcode 的剑指 Offer（专项突击版）系列, 大家在公众号 **算法精选** 里回复 `剑指offer2` 就能看到该系列当前连载的所有文章了, 记得关注哦~

## 题目描述

某店铺将用于组成套餐的商品记作字符串 goods，其中 goods[i] 表示对应商品。请返回该套餐内所含商品的 全部排列方式 。

返回结果 无顺序要求，但不能含有重复的元素。

### 示例 1：

- 输入：goods = "agew"
- 输出：["aegw","aewg","agew","agwe","aweg","awge","eagw","eawg","egaw","egwa","ewag","ewga","gaew","gawe","geaw","gewa","gwae","gwea","waeg","wage","weag","wega","wgae","wgea"]

### 提示：

- 1 <= goods.length <= 8

## 题目思考

1. 如何不重复地得到所有排列?

## 解决方案

#### 思路

- 要想不重复地得到所有排列, 我们可以定义一个方法, 来求字典序的下一个排列, 正如题目示例中的输出数组的顺序那样
- 初始化为最小的字典序, 然后当遍历到最大的字典序的时候, 就说明所有排列都被找到了
- 所以算法的核心就是如何通过一个字符串找它的字典序的下一个排列
- 下一个排列一定是所有排列中大于当前字符串且最接近它的, 所以我们可以利用贪心算法, 具体步骤如下:
  1. 从后向前找第一个**小于后一个字符**的字符 i (因为如果大于等于后一个字符的话, 就没法与后面的字符交换来使得整体字符串更大了)
  2. 找刚才遍历的部分的大于且最接近 i 的字符 j
  3. 将它们两个互换
  4. 然后 i 往后的部分都按字典序从小到大排列
- 这样就保证了新的字符串一定是大于当前字符串且最接近它的, 不可能有更小的了
- 下面的代码中有详细的注释, 方便大家理解

#### 复杂度

- 时间复杂度 `O(N*N!)`
  - 最差情况下 N 个字符各不相同, 排列就有 `N!` 种, 从当前字符串转入下一个字符串最坏情况下需要 `O(N)` 时间
- 空间复杂度 `O(1)`
  - 只使用了几个变量 (不考虑输出结果集)

#### 代码

```python
class Solution:
    def goodsOrder(self, goods: str) -> List[str]:
        res = []

        def getNext(goods):
            for i in range(len(goods) - 1)[::-1]:
                # 从后向前查找
                if goods[i] < goods[i + 1]:
                    # 找到目标字符了, 接下来找后面部分中大于goods[i]且最接近它的字符
                    j = i + 1
                    while j < len(goods) and goods[j] > goods[i]:
                        j += 1
                    # goods[j-1]就是后面部分中大于goods[i]且最接近它的字符
                    j -= 1
                    # 单独拿出右边部分, 肯定严格按照降序排列
                    right = goods[i + 1:j] + goods[i] + goods[j + 1:]
                    # 交换字符后, 并加上右边的翻转部分(增序, 最小字典序), 就是下一个字典序的字符串
                    return goods[0:i] + goods[j] + right[::-1]
            # 没找到下一个字符串, 说明当前字符串就是字典序最大的了, 直接返回None
            return None

        # 先拿到字典序最小的字符串
        goods = ''.join(sorted(goods))
        while goods is not None:
            res.append(goods)
            goods = getNext(goods)
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

> 我的公众号: [算法精选](https://mp.weixin.qq.com/s?__biz=MzA5MDk1MjI5MA==&mid=2247484158&idx=1&sn=90176bac32cf7af40e4074c721fd8a95&chksm=900285f3a7750ce5a068c9c9773781461819633f2fd60533732637ec9520c908371ebc218d49&scene=178&cur_album_id=1386231241346859009#rd), 欢迎大家扫码关注~😊

![算法精选 - 微信扫一扫关注我](https://pic1.zhimg.com/80/v2-7c988a7b35886df51596ef23616764ac_1440w.jpg)
