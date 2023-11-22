# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分之区间合并（贪心）



#### 1、252. 会议室

**问题**：

给定一个会议时间安排的数组 `intervals` ，每个会议时间都会包括开始和结束的时间`intervals[i] = [starti, endi]` ，请你判断一个人是否能够参加这里面的全部会议。

**思路**：**合并区间**，取**并集**

```python
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        intervals.sort() # 按会议的起始时间从小到大排序
        
        res = [] 
        st, ed = -2e9, -2e9 # [st,ed]表示当前维护的区间,[st,ed]表示并集
        
        for i in range(len(intervals)): # 从左往右合并区间, 取并集
            l, r = intervals[i][0], intervals[i][1]
            
            if ed <= l: # [st,ed]与[l,r]不相交, 例如[[13,15],[1,13]]视为不相交
                if st != -2e9:
                    res.append([st,ed])
                st, ed = l, r
            else: # [st,ed]与[l,r]相交
                ed = max(ed, r) # 合并两个区间, 取并集

        if st != -2e9: 
            res.append([st, ed]) # 将最后一个区间加进去
        return len(res) == len(intervals)
```

#### 2、452. 用最少数量的箭引爆气球

**问题**：

在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以纵坐标并不重要，因此只要知道开始和结束的横坐标就足够了。开始坐标总是小于结束坐标。

一支弓箭可以沿着 $x$ 轴从不同点完全垂直地射出。在坐标 $x$ 处射出一支箭，若有一个气球的直径的开始和结束坐标为$x_{start}$，$x_{end}$， 且满足  $x_{start} ≤ x ≤ x_{end}$，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

给你一个数组 `points` ，其中 $points[i] = [x_{start},x_{end}]$，返回引爆所有气球所必须射出的最小弓箭数。

**思路**：**合并区间**，取**交集**，求不重叠区间的个数

```python
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        points.sort()

        st, ed, cnt = -2e10, -2e10, 0 #[st,ed]表示交集, cnt表示交集的个数
        for i in range(len(points)): # 从左往右合并区间, 取交集
            l, r = points[i][0], points[i][1]
            if ed < l:
                if st != -2e10: # [st,ed]与[l,r]不相交
                    cnt += 1
                st, ed = l, r
            else:
                st, ed = max(st, l), min(ed, r) # [st,ed]与[l,r]相交, 取交集
        if st != -2e10: # 将最后一个区间加进去
            cnt += 1
        return cnt
```

#### 3、435. 无重叠区间

**问题**：

给定一个区间的集合 `intervals` ，其中 `intervals[i] = [starti, endi]` 。返回 *需要移除区间的最小数量，使剩余区间互不重叠* 。

**思路**：**合并区间**，取**交集**，求重叠区间的个数

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        intervals.sort()

        st, ed, cnt = -2e10, -2e10, 0 #[st,ed]表示交集, cnt表示交集的个数,即不重叠区间的个数
        for i in range(len(intervals)): # 从左往右合并区间, 取交集
            l, r = intervals[i][0], intervals[i][1]
            if ed <= l:
                if st != -2e10: # [st,ed]与[l,r]不相交
                    cnt += 1
                st, ed = l, r
            else:
                st, ed = max(st, l), min(ed, r) # [st,ed]与[l,r]不交, 取交集
        if st != -2e10: # 将最后一个区间加进去
            cnt += 1
        return len(intervals) - cnt # 求重叠区间的个数
```

#### 4、[646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/)

**问题**

给你一个由 `n` 个数对组成的数对数组 `pairs` ，其中 `pairs[i] = [lefti, righti]` 且 `lefti < righti` 。

现在，我们定义一种 **跟随** 关系，当且仅当 `b < c` 时，数对 `p2 = [c, d]` 才可以跟在 `p1 = [a, b]` 后面。我们用这种形式来构造 **数对链** 。

找出并返回能够形成的 **最长数对链的长度** 。

你不需要用到所有的数对，你可以以任何顺序选择其中的一些数对来构造。

例子

```
输入：pairs = [[1,2], [2,3], [3,4]]
输出：2
解释：最长的数对链是 [1,2] -> [3,4] 。
```

**数据范围**

- `n == pairs.length`
- `1 <= n <= 1000`
- `-1000 <= lefti < righti <= 1000`

**思路**

此题与 *给定一堆课的开始时间和结束时间，问怎么选课可以使得上的课的数量最多* 非常类似。数对的 $left_i$ 可以看作是起始时间，$right_i$ 可以看作是结束时间，起始时间 $left_i$ < 结束时间 $right_i$。数对链的最大长度就等于可以上的数量最多的课。

#### 5、[986. 区间列表的交集](https://leetcode.cn/problems/interval-list-intersections/)

**问题**

给定两个由一些 **闭区间** 组成的列表，`firstList` 和 `secondList` ，其中 `firstList[i] = [starti, endi]` 而 `secondList[j] = [startj, endj]` 。每个区间列表都是成对 **不相交** 的，并且 **已经排序** 。

返回这 **两个区间列表的交集** 。

形式上，**闭区间** `[a, b]`（其中 `a <= b`）表示实数 `x` 的集合，而 `a <= x <= b` 。

两个闭区间的 **交集** 是一组实数，要么为空集，要么为闭区间。例如，`[1, 3]` 和 `[2, 4]` 的交集为 `[2, 3]` 。

例子

<img src="https://assets.leetcode.com/uploads/2019/01/30/interval1.png" alt="img" style="zoom: 50%;" />

```
输入：firstList = [[0,2],[5,10],[13,23],[24,25]], secondList = [[1,5],[8,12],[15,24],[25,26]]
输出：[[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
```

数据范围：

- `0 <= firstList.length, secondList.length <= 1000`
- `firstList.length + secondList.length >= 1`
- `0 <= starti < endi <= 10^9`
- `endi < starti+1`
- `0 <= startj < endj <= 10^9`

**思路**：

**二路归并**与**区间合并取交集**相结合，细节见代码注释

时间复杂度是 $O(N)$

```java
class Solution {
    public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        int[][] a = firstList, b = secondList;
        int n = a.length, m = b.length;

        int i = 0, j = 0;
        List<int[]> t = new ArrayList<>();
        int st = -0x3f3f3f3f, ed = -0x3f3f3f3f;
        while(i < n && j < m) { // 二路归并的思想
            // 以区间的左端点作为二路归并的比较依据
            int l = a[i][0] <= b[j][0] ? a[i][0] : b[j][0];
            int r = a[i][0] <= b[j][0] ? a[i ++ ][1] : b[j ++][1];
            if(ed < l) { // [l, r]与[st,ed]不相交
                st = l; ed = r; // 直接更新[st,ed]为[l,r]
            } else { // [l, r] 与 [st, ed] 存在交集
                // intersection 是[l,r]与[st,ed]相交的部分
                int[] intersection = new int[]{Math.max(st, l), Math.min(ed, r)};
                // 由于firstList和secondList都是已经排好序，且相邻区间是不相交的
                // st 更新为[l,r]与[st,ed]右端点的最小值, ed 更新为[l,r]与[st,ed]右端点的最大值
                // 这样更新的原因是firstList和secondList的下一个区间的左端点一定大于ed或者r
                st = Math.min(ed, r); ed = Math.max(ed, r);
                t.add(intersection);
            }
        }

        while(i < n) { // firstList还存在部分区间没有考虑
            int l = a[i][0], r = a[i ++][1];
            if(ed < l) break;
            else {
                int[] intersection = new int[]{Math.max(st, l), Math.min(ed, r)};
                st = Math.min(ed, r); ed = Math.max(ed, r);
                t.add(intersection);
            }
        }

        while(j < m) { // secondList还存在部分区间没有考虑
            int l = b[j][0], r = b[j ++][1];
            if(ed < l) break;
            else {
                int[] intersection = new int[]{Math.max(st, l), Math.min(ed, r)};
                st = Math.min(ed, r); ed = Math.max(ed, r);
                t.add(intersection);
            }
        }

        int[][] res = new int[t.size()][2];
        for(int k = 0; k < t.size(); k ++)
            res[k] = t.get(k);
        return res;
    }
}
```

#### 6、[2848. 与车相交的点](https://leetcode.cn/problems/points-that-intersect-with-cars/)



### :mag_right: 参考文献 

[1] https://leetcode.cn/

[2] https://www.acwing.com/

### :closed_lock_with_key: License

本文内容不是将网上的资料随意拼凑而来，除了少部分引用书上和技术文档的原文（这部分内容都在末尾的参考文献中加了出处），其余都是作者的原创。在您引用本文内容或者对内容进行修改演绎时，请署名并以相同方式共享，谢谢。

转载文章请在开头明显处标明该页面地址，公众号等其它转载请联系 [1246550576@qq.com](mailto:1246550576@qq.com)

### 📝排版申明

笔记内容按照一定标准进行排版，以保证内容的可读性。

不使用 `![]()` 这种方式来引用图片，而是用 `<img>` 标签。为了能够控制图片以合适的大小显示，使用 `<div align="center"> <img src=""/> </div>` 达到居中的效果。

排版美化工具：[Typora-Purple](
