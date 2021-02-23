# [1450] 在既定时间做作业的学生人数

## 题目思考

1. 题目很直白, 直接模拟可以吗?

## 解决方案

### 思路

- 遍历数组, 检查当前区间满不满足要求即可

### 复杂度

- 时间复杂度 O(N): 需要遍历每个元素一次
- 空间复杂度 O(1): 只使用了几个变量

### 代码

```python3 []
class Solution:
    def busyStudent(self, startTime: List[int], endTime: List[int],
                    queryTime: int) -> int:
        # 简单的区间判断
        res = 0
        for i in range(len(startTime)):
            if startTime[i] <= queryTime <= endTime[i]:
                res += 1
        return res
```

```cpp []
class Solution {
public:
    int busyStudent(vector<int>& startTime, vector<int>& endTime, int queryTime) {
        int res = 0;
        for (int i = 0; i < startTime.size(); ++i) {
            if (startTime[i] <= queryTime && queryTime <= endTime[i]) {
                ++res;
            }
        }
        return res;
    }
};
```

---

# [1451] 重新排列句子中的单词

## 题目思考

1. 可以使用什么数据结构?

## 解决方案

### 思路

- 先将原字符串转换成字符串集合
- 然后使用{len:[s]}字典, 把字符串长度作为 key, 字符串按照其遍历顺序加入到 value 列表中
- 注意开头字符串需要先转全小写, 最后生成好了之后开头字符串再转首字母大写
- 最后再将字符串集合拼接成字符串即可

### 复杂度

- 时间复杂度 O(NlogN): 遍历需要 O(N), 排序 key 需要 O(NlogN) (最差情况是每个字符串的长度都不一样)
- 空间复杂度 O(N): 使用了一个字典存所有字符串

### 代码

```python3 []
class Solution:
    def arrangeWords(self, text: str) -> str:
        a = text.split()
        a[0] = a[0].lower()
        d = collections.defaultdict(list)
        for s in a:
            d[len(s)].append(s)
        res = []
        for k in sorted(d.keys()):
            res.extend(d[k])
        res[0] = res[0].title()
        return ' '.join(res)
```

```cpp []
class Solution {
public:
    string arrangeWords(string text) {
        string substr;
        multimap<int, string> len2str;

        transform(text.begin(), text.end(), text.begin(),::tolower);
        istringstream stream(text);

        while (getline(stream, substr, ' ')) {
            len2str.emplace(substr.size(), substr);
        }

        string res;
        for_each(len2str.begin(), len2str.end(), [&](const pair<int, string>& entry){ res += entry.second + ' '; });
        res.pop_back();
        res[0] = toupper(res[0]);

        return res;
    }
};
```

---

# [1452] 收藏清单

## 题目思考

1. 是否可以模拟整个过程?
2. 需要用到什么数据结构和操作?

## 解决方案

### 思路

- 模拟整个过程
- 先将每个清单转换成集合
- 然后双重循环, 判断集合关系, 把确定不是其他任何一个子集的元素加入结果中即可

### 复杂度

- 时间复杂度 O(N^2\*M): N 表示清单数目, M 表示单个清单的长度
- 空间复杂度 O(N\*M): 将列表转化为了集合, 方便进行集合操作

### 代码

```python3 []
class Solution:
    def peopleIndexes(self, favoriteCompanies: List[List[str]]) -> List[int]:
        res = []
        n = len(favoriteCompanies)
        favoriteCompanies = list(set(f) for f in favoriteCompanies)
        for i in range(n):
            for j in range(n):
                if i != j and favoriteCompanies[i].issubset(
                        favoriteCompanies[j]):
                    break
            else:
                res.append(i)
        return res
```

```cpp []
class Solution {
public:
    vector<int> peopleIndexes(vector<vector<string>>& favoriteCompanies) {
        vector<int> res;
        auto& v = favoriteCompanies;

        for_each(v.begin(), v.end(), [](vector<string>& vec) { sort(vec.begin(), vec.end()); });

        for(int i = 0; i < v.size(); ++i) {
            bool isSubset = false;
            for (int j = 0; j < v.size(); ++j) {
                isSubset = i != j && includes(v[j].begin(), v[j].end(), v[i].begin(), v[i].end());
                if (isSubset) {
                    break;
                }
            }
            if (!isSubset) {
                res.push_back(i);
            }
        }

        return res;
    }
};
```

---

# [1453] 圆形靶内的最大飞镖数量

## 题目思考

1. 是否可以找到所有可能的圆的圆心?
2. 可以用到什么几何性质/公式吗?

## 解决方案

### 思路

- 推导
  - 假设一个圆包含至少两个点, 那么它可以通过平移变换使得至少有两个点在圆上 (先平移固定一个点在圆上, 再旋转, 总可以再把另一个点放在圆上的)
  - 所以两重循环遍历所有点的组合, 如果这两点的距离小于等于圆的直径, 则说明这两个点再加上半径能确定一个圆
  - 然后根据确定出来的圆判断多少个点在该圆内, 找出包含点最多的圆即可
  - 此题难点在于如何通过两个点和一个半径确定圆心, 其实就是二元二次方程, 通过定义一些合理的中间变量简化最终的表达式...
- 步骤

  - 设两点是(x1,y1)和(x2,y2), 两点中心坐标是(px,py), 则 `px=(x1+x2)/2`, `py=(y1+y2)/2`
  - 设两点之间距离为 d, 圆心到中点的距离为 h, 那么可以根据勾股定理求得 `h=sqrt(r^2-(d/2)^2)`
  - 这样就可以得到下面两个等式:

    1. 圆心到中点的距离为 h, 即`(cx-px)^2+(cy-py)^2=h^2`
    2. 圆心到两点的中心的连线与两点之间连线是垂直的(因为圆心和两点构成了等腰三角形, 腰就是半径 r), 根据向量关系, 垂直的充要条件是两个向量的点积为 0, 即`(x1-x2)*(cx-px)+(y1-y2)*(cy-py)=0`, 可以把(x1-x2)和(y1-y2)定义成新的变量 dx 和 dy 简化方程, 即 `dx*(cx-px)+dy*(cy-py)=0`

  - 联立两个方程可以解得:
    - `cx = (+/-)h*dy/sqrt(dx^2+dy^2)+px`
    - `cy = (-/+)h*dx/sqrt(dx^2+dy^2)+py`
  - 注意一对解中的 cx 和 cy 的 sqrt 前面的符号是相反的

### 复杂度

- 时间复杂度 O(N^3): 两重循环确定所有点的组合 - O(N^2), 检查圆包含多少个点 - O(N), 所以总时间复杂度就是 O(N^3)
- 空间复杂度 O(1): 只使用了几个变量

### 代码

```python3 []
import math


class Solution:
    def numPoints(self, points: List[List[int]], r: int) -> int:
        n = len(points)

        def getinsidecount(cx, cy):
            res = 0
            for p in points:
                x, y = p
                if (x - cx)**2 + (y - cy)**2 <= r**2:
                    res += 1
            return res

        res = 1
        for i in range(n):
            for j in range(i + 1, n):
                x1, y1 = points[i]
                x2, y2 = points[j]
                d2 = (x1 - x2)**2 + (y1 - y2)**2
                if d2 > (2 * r)**2:
                    continue
                px, py = (x1 + x2) / 2, (y1 + y2) / 2
                dx, dy = x1 - x2, y1 - y2
                h = math.sqrt(r**2 - d2 / 4)
                for fx, fy in ((1, -1), (-1, 1)):
                    # 解有两个, 符号正好相反
                    cx = fx * h * dy / math.sqrt(dx**2 + dy**2) + px
                    cy = fy * h * dx / math.sqrt(dx**2 + dy**2) + py
                    res = max(res, getinsidecount(cx, cy))
        return res
```

```cpp []
class Solution {
public:
    int numPoints(vector<vector<int>>& points, int r) {
        int rSquare = r * r;
        int rSquare4 = 4 * rSquare;

        auto middle = [](const vector<int>& lhs, const vector<int>& rhs) {
            vector<double> res(2);
            res[0] = 1.0 * (lhs[0] + rhs[0]) / 2;
            res[1] = 1.0 * (lhs[1] + rhs[1]) / 2;
            return res;
        };

        auto difference = [](const vector<int>& lhs, const vector<int>& rhs) {
            vector<double> res(2);
            res[0] = lhs[0] - rhs[0];
            res[1] = lhs[1] - rhs[1];
            return res;
        };

        auto getDistance = [](const vector<double>& lhs) {
            return lhs[0] * lhs[0] + lhs[1] * lhs[1];
        };

        auto getInsideCount = [&](double cx, double cy) {
            int res = 0;
            for_each(points.begin(), points.end(), [&](vector<int>& p) {
               double dx = p[0] - cx;
               double dy = p[1] - cy;
               if (dx * dx + dy * dy <= rSquare) {
                   ++res;
               }
            });
            return res;
        };

        int res = 1;
        for (int i = 0; i < points.size(); ++i) {
            for (int j = i + 1; j < points.size(); ++j) {
                auto diff = difference(points[i], points[j]);
                double distance = getDistance(diff);
                if (distance > rSquare4) {
                    continue;
                }

                auto mid = middle(points[i], points[j]);
                double h = sqrt(1.0 * rSquare - 1.0 * distance / 4);

                double dx = h * diff[1] / sqrt(1.0 * distance);
                double dy = h * diff[0] / sqrt(1.0 * distance);

                res = max(res, getInsideCount(mid[0] + dx , mid[1] - dy));
                res = max(res, getInsideCount(mid[0] - dx , mid[1] + dy));
            }
        }

        return res;
    }
};
```

---

> 大家可以在下面这些地方找到我~😊

> [我的知乎专栏](https://zhuanlan.zhihu.com/c_1242508721932464128)

> [我的 CSDN](https://me.csdn.net/zjulyx1993)

> [我的简书](https://www.jianshu.com/u/3a17f1fdfd67)

> [我的 Leetcode](https://leetcode-cn.com/u/suibianfahui/)

> 我的公众号: 每日精选算法题, 欢迎大家扫码关注~😊

![每日精选算法题 - 微信扫一扫关注我](https://mmbiz.qpic.cn/mmbiz_jpg/1KjZicMlYPMgZWmoL4eYcs6UcfmvsetDWME2YJyaCp9oT9z3U573FWENBNhyOByxYI0epew6O37hiaOhdh90QeJg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
