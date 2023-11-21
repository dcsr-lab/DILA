# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之二分

### 1、二分

#### （1）[1170. 比较字符串最小字母出现频次](https://leetcode.cn/problems/compare-strings-by-frequency-of-the-smallest-character/)

**问题**：

定义一个函数 `f(s)`，统计 `s` 中**（按字典序比较）最小字母的出现频次** ，其中 `s` 是一个非空字符串。

例如，若 `s = "dcce"`，那么 `f(s) = 2`，因为字典序最小字母是 `"c"`，它出现了 2 次。

现在，给你两个字符串数组待查表 `queries` 和词汇表 `words` 。对于每次查询 `queries[i]` ，需统计 `words` 中满足 `f(queries[i])` < `f(W)` 的 **词的数目** ，`W` 表示词汇表 `words` 中的每个词。

请你返回一个整数数组 `answer` 作为答案，其中每个 `answer[i]` 是第 `i` 次查询的结果。

例子：

```
输入：queries = ["cbd"], words = ["zaaaz"]
输出：[1]
解释：查询 f("cbd") = 1，而 f("zaaaz") = 3 所以 f("cbd") < f("zaaaz")。
```

**数据范围**：

- `1 <= queries.length <= 2000`
- `1 <= words.length <= 2000`

- `1 <= queries[i].length, words[i].length <= 10`
- `queries[i][j]`、`words[i][j]` 都由小写英文字母组成

**思路**：排序+二分

```java
class Solution {
    
    public int f(String s) {
        char[] chs = s.toCharArray();
        Arrays.sort(chs);
        int i = 1;
        while(i < chs.length && chs[i] == chs[i - 1]) i ++ ;
        return i;
    }

    public int bsearch(int[] arr, int x) { // 二分用于加速寻找
        int l = 0, r = arr.length - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1; // 枚举右端点
            if(arr[mid] <= x) l = mid; // 因为是<=, 所以如果要查找的数不存在, l指向比x小的第一个数(从左往右)
            else r = mid - 1;
        }
        while(l < arr.length && arr[l] <= x) l += 1; // 找到第一个比它大的数
        return arr.length - l;
    }

    public int[] numSmallerByFrequency(String[] queries, String[] words) {
        int n = queries.length, m = words.length;  
        int[] q = new int[n], w = new int[m];

        for(int i = 0; i < n; i ++ ) q[i] = f(queries[i]);
        for(int i = 0; i < m; i ++ ) w[i] = f(words[i]);

        Arrays.sort(w);

        int[] res = new int[n];

        for(int i = 0; i < n; i ++ )
            res[i] = bsearch(w, q[i]);

        return res;
    }
}
```

#### （2）[2187. 完成旅途的最少时间](https://leetcode.cn/problems/minimum-time-to-complete-trips/)

**问题**：

给你一个数组 `time` ，其中 `time[i]` 表示第 `i` 辆公交车完成 **一趟旅途** 所需要花费的时间。

每辆公交车可以 **连续** 完成多趟旅途，也就是说，一辆公交车当前旅途完成后，可以 **立马开始** 下一趟旅途。每辆公交车 **独立** 运行，也就是说可以同时有多辆公交车在运行且互不影响。

给你一个整数 `totalTrips` ，表示所有公交车 **总共** 需要完成的旅途数目。请你返回完成 **至少** `totalTrips` 趟旅途需要花费的 **最少** 时间。

例子：

```
输入：time = [1,2,3], totalTrips = 5
输出：3
解释：
- 时刻 t = 1 ，每辆公交车完成的旅途数分别为 [1,0,0] 。
  已完成的总旅途数为 1 + 0 + 0 = 1 。
- 时刻 t = 2 ，每辆公交车完成的旅途数分别为 [2,1,0] 。
  已完成的总旅途数为 2 + 1 + 0 = 3 。
- 时刻 t = 3 ，每辆公交车完成的旅途数分别为 [3,1,1] 。
  已完成的总旅途数为 3 + 1 + 1 = 5 。
所以总共完成至少 5 趟旅途的最少时间为 3 。
```

**数据范围**：

- `1 <= time.length <= 10^5`
- `1 <= time[i], totalTrips <= 10^7`

**思路**：

设所有公交车中完成一趟旅途所需花费的最小时间为 $t$。那么完成$totalTrips$趟旅途的最小时间一定位于区间$[0, t * totalTrips]$中。

 $t * totalTrips$ 表示花费最小的时间的公交车跑$totalTrips$多趟所需的总时间。

因此，我们可以尝试从区间$[0, t * totalTrips]$中二分出来。设二分的中点为 $mid$。

- 如果在$mid$ 时间内完成的旅途数严格小于 $totalTrips$，更新左边界 $l = mid + 1$。
- 如果在 $mid$ 时间内完成的旅途数大于等于 $totalTrips$，更新右边界 $r = mid$

那么，在 $mid$ 时间内，我们如何计算出所有的公交车能够完成的旅途数？

考虑一种暴力的做法，设在 $x$ 时间内，第 $i$ 辆公交车车完成旅途所需的时间为 $time[i]$，那么第$i$辆公交车能够完成的旅途数为 $x/time[i]$。我们将所有公交车完成的旅途数加起来。

时间复杂度：二分的时间复杂度为$logD$，$D=t*totalTrips$是二分的区间长度。计算所有车能完成的旅途数的复杂度是 $O(n)$。总的时间复杂度为 $O(nlogD)$。

注意：实现时需要考虑`int`溢出问题

```java
class Solution {

    public long check(int[] time, long x) { // 计算在时间x内，所有的公交车一共跑了多少趟
        // 暴力的做法：在时间x内，第i辆公家车跑了 x/time[i]趟，将所有公交车跑的趟数加起来，时间复杂度是 O(n)
        long cnt = 0;
        for(int i = 0; i < time.length; i ++ )
            cnt += x / time[i];
        return cnt;
    }

    public long bsearch(int[] time, long l, long r, int x) { // log(l - r)
        while(l < r) {
            long mid = (l + r) >> 1;
            if(check(time, mid) < x) l = mid + 1;
            else r = mid;
        }
        return l;
    }

    public long minimumTime(int[] time, int totalTrips) {
        int t = Integer.MAX_VALUE; 
        for(int i = 0; i < time.length; i ++ ) // 从所有公交车中找出一趟旅途所需花费的最小时间
            t = Math.min(t, time[i]);
        // 完成totalTrips趟旅途的最小时间一定位于区间[0, t * totalTrips]中
        // t * totalTrips 表示花费最小的时间的公交车跑totalTrips多趟
        return bsearch(time, 0, (long)t * totalTips, totalTrips);
    }
}
```

#### （3）1064. 不动点

**问题**：

给定已经按 **升序** 排列、由**不同整数**组成的数组 `arr`，返回满足 `arr[i] == i` 的最小索引 `i`。如果不存在这样的 `i`，返回 `-1`。

- `1 <= arr.length < 104`
- `-109 <= arr[i] <= 109`

 **思路**：**二分**，**查找左边界**

```java
class Solution {

    public int bsearch(int[] a){ // 二分枚举左边界
        int l = 0, r = a.length - 1;
        while(l < r){
            int mid = l + r >> 1;
            if(mid <= a[mid]) r = mid;
            else l = mid + 1;
        }
        if(l == r && a[l] == l) return l;
        else return -1;
    }

    public int fixedPoint(int[] arr) {
        return bsearch(arr);
    }
}
```

#### （4）[436. 寻找右区间](https://leetcode.cn/problems/find-right-interval/)

**问题**

给你一个区间数组 `intervals` ，其中 `intervals[i] = [starti, endi]` ，且每个 `starti` 都 **不同** 。

区间 `i` 的 **右侧区间** 可以记作区间 `j` ，并满足 `startj >= endi` ，且 `startj` **最小化** 。注意 `i` 可能等于 `j` 。

返回一个由每个区间 `i` 的 **右侧区间** 在 `intervals` 中对应下标组成的数组。如果某个区间 `i` 不存在对应的 **右侧区间** ，则下标 `i` 处的值设为 `-1` 。

例子

```
输入：intervals = [[1,2]]
输出：[-1]
解释：集合中只有一个区间，所以输出-1。
```

**数据范围**

- `1 <= intervals.length <= 2 * 10^4`
- `intervals[i].length == 2`
- `-10^6 <= starti <= endi <= 10^6`
- 每个间隔的起点都 **不相同**

**思路**：

设当前考虑的区间为 $intervals_i=[start_i, end_i]$。

所有区间的**左端点的值$start_i$都不同**，可以用哈希表存储区间区间左端点$start_i$到索引$i$的映射关系。

将所有区间**按左端点进行排序**。

如何求区间 $interval_i$的右侧区间是谁？我们可以在**第$i$ 个区间到第$n-1$个区间**的左端点$start$构成的**升序数组**中，查找大于等于$end_i$的第一个$start_j$，其索引$j$就是区间$interval_i$的右侧区间。

特殊地，对于区间$[start_i,end_i]$，当 $start_i=end_i$时，其右侧区间就是自己。从第$i$ 个区间开始枚举，可以保证此特殊情况也可以被考虑到。

为了降低时间复杂度，我们可以采用二分查找。

总的时间复杂度是 $O(nlogn)$

```java
class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for(int i = 0; i < n; i ++)   map.put(intervals[i][0], i); // 将左端点映射到下标

        Arrays.sort(intervals, (a, b) -> a[0] - b[0]); // 按左端点从小到大排序区间

        int[] res = new int[n];
        for(int i = 0; i < n; i ++) {
            int st = intervals[i][0], ed = intervals[i][1];
            // 二分的左边界为i，例如 [[1,1],[3,4]] => [0,-1]; 当 st = ed时，其左侧区间还是自己
            int l = i, r = n - 1; // 二分查找左端点比ed大的最小的数
            while(l < r) {
                int mid = l + r >> 1;
                if(intervals[mid][0] >= ed) r = mid;
                else l = mid + 1;
            }
            if(intervals[l][0] >= ed) // 找到了
                res[map.get(intervals[i][0])] = map.get(intervals[l][0]);
            else  res[map.get(intervals[i][0])] = -1; // 不存在右侧区间
        }
        return res;
    }
}
```

#### （5）[69. x 的平方根 ](https://leetcode.cn/problems/sqrtx/):star2:

**问题**

给你一个非负整数 `x` ，计算并返回 `x` 的 **算术平方根** 。

由于返回类型是整数，结果只保留 **整数部分** ，小数部分将被 **舍去 。**

**注意：**不允许使用任何内置指数函数和算符，例如 `pow(x, 0.5)` 或者 `x ** 0.5` 。

例子

```
输入：x = 8
输出：2
解释：8 的算术平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```

**数据范围**

- `0 <= x <= 2^31 - 1`

**思路**

**方案一**：**实数二分** $O(logn)$

实数二分问题通常是**函数求根**、**开方**等问题，比较简单，$while$ 循环终止条件是精度 $e$，更新时 $l$ 和 $r$ 都更新为 $mid$ 即可。

在开方时需要注意，使用**右边界**作为答案的输出。

```java
class Solution {
    public int mySqrt(int x) {
        double l = 0, r = x;
        final double eps = 1e-6;
        while(r - l > eps) { // 实数二分，用eps作为判断条件，左右端点更新为mid
            double mid = (l + r) / 2;
            if(mid * mid - x >= eps) r = mid;
            else l = mid;
        }
        return (int)(Math.floor(r)); // 输出r
    }
}
```

**方案二**：**牛顿迭代法**

设 $a$ 的算术平方根为 $x$，求$a$ 的算术平方根 **等价于** 求方程 $x^2=a$的正根。 可以采用**[牛顿迭代法](https://baike.baidu.com/item/%E7%89%9B%E9%A1%BF%E8%BF%AD%E4%BB%A3%E6%B3%95/10887580)**快速求解方程的根。

牛顿迭代法的迭代公式：$x_{n}=x_{n-1}-\frac{f\left(x_{n-1}\right)}{f^{\prime}\left(x_{n-1}\right)}$

依据$f(x)=x^2-a$，则迭代公式为：
$$
x_n = x_{n-1} - \frac{x^2_{n-1}-a}{2x_{n-1}}
$$
迭代终止的条件是 $|x_n - x_{n-1}|<1 \times 10^{-6}$

```java
class Solution {
    public int mySqrt(int x) {
        
        double last = 0, a = 1;
        final double eps = 1e-6;
        while(Math.abs(a - last) >= eps) {
            last = a;
            a = a - (a * a - x)/(2 * a);
        }
        return (int)Math.floor(a);
    }
}
```

关于二分法和牛顿迭代法求解非线性方程根的问题，详见数值分析课本的内容。

#### （6）[74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/) :star2:

**问题**

给你一个满足下述两条属性的 `m x n` 整数矩阵：

- 每行中的整数从左到右按非递减顺序排列。
- 每行的第一个整数大于前一行的最后一个整数。

给你一个整数 `target` ，如果 `target` 在矩阵中，返回 `true` ；否则，返回 `false` 。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/05/mat.jpg" alt="img" style="zoom:50%;" />

```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```

**数据范围**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 100`
- `-10^4 <= matrix[i][j], target <= 10^4`

**思路**

此题考查二分。给定一个**「蛇形二维数组」**，从该数组中查找某一个数是否存在。

「蛇形二维数组」是指该数组每行中的整数从左到右按非递减顺序排列，每行的第一个整数大于前一行的最后一个整数。

该数组的特点是**每一行递增有序，每一列递增有序，且某一个行的数一定大于前一行的数**。

可以考虑采用**两次二分查找**：

- 在第一列即$matrix[0...n][0]$中，进行第一次二分查找$target$，目的是确定$target$ 属于哪一行。
- 设第一二分查找的结果是第 $i$ 行，在$matrix[0][0...m]$中进行第二次二分查找。

```java
class Solution {
    
    public boolean searchMatrix(int[][] matrix, int target) {
        int n = matrix.length, m = matrix[0].length;
        // 按列二分
        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(matrix[mid][0] <= target) l = mid;
            else r = mid - 1;
        }
        // 按行二分
        int t = l;
        l = 0; r = m - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(matrix[t][mid] <= target) l = mid;
            else r = mid - 1;
        }
        if(matrix[t][l] == target) return true;
        return false;
    }
}
```

#### （7）[875. 爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)

**问题**

珂珂喜欢吃香蕉。这里有 `n` 堆香蕉，第 `i` 堆中有 `piles[i]` 根香蕉。警卫已经离开了，将在 `h` 小时后回来。

珂珂可以决定她吃香蕉的速度 `k` （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 `k` 根。如果这堆香蕉少于 `k` 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 `h` 小时内吃掉所有香蕉的最小速度 `k`（`k` 为整数）。

例子

```
输入：piles = [3,6,7,11], h = 8
输出：4
```

**数据范围**

- `1 <= piles.length <= 10^4`
- `piles.length <= h <= 10^9`
- `1 <= piles[i] <= 10^9`

**思路**

```java
class Solution {

    public boolean check(int[] piles, int mid, int h) {
        int t = 0;
        for(int i = 0; i < piles.length; i ++) {
            t += piles[i] / mid;
            if(piles[i] % mid > 0) t += 1;
        }
        return t > h;
    }

    public int minEatingSpeed(int[] piles, int h) {
        int l = 1, r = 1;
        for(int i = 0; i < piles.length; i ++) 
            r = Math.max(r, piles[i]);

        while(l < r) {
            int mid = l + r >> 1;
            if(check(piles, mid, h)) l = mid + 1;
            else r = mid;
        }
        return l;
    }
}
```

#### （8）[1283. 使结果不超过阈值的最小除数](https://leetcode.cn/problems/find-the-smallest-divisor-given-a-threshold/)

**问题**

给你一个整数数组 `nums` 和一个正整数 `threshold` ，你需要选择一个正整数作为除数，然后将数组里每个数都除以它，并对除法结果求和。

请你找出能够使上述结果小于等于阈值 `threshold` 的除数中 **最小** 的那个。

每个数除以除数后都向上取整，比方说 7/3 = 3 ， 10/2 = 5 。

题目保证一定有解。

示例

```
输入：nums = [2,3,5,7,11], threshold = 11
输出：3
```

**数据范围**

- `1 <= nums.length <= 5 * 10^4`
- `1 <= nums[i] <= 10^6`
- `nums.length <= threshold <= 10^6`

**思路**

```java
class Solution {

    public boolean check(int[] nums, int mid, int threshold) {
        int sum = 0;
        for(int i = 0; i < nums.length; i ++) {
            sum += (nums[i] / mid);
            if(nums[i] % mid > 0) sum ++;
        }
        return sum > threshold;
    }

    public int smallestDivisor(int[] nums, int threshold) {
        int n = nums.length;
        int l = 1, r = (int)1e7;
        while(l < r) {
            int mid = l + r >> 1;
            if(check(nums, mid, threshold)) l = mid + 1;
            else r = mid;
        }
        return l;
    }
}
```



#### （9）[778. 水位上升的泳池中游泳](https://leetcode.cn/problems/swim-in-rising-water/)（二分+BFS）



#### （10）[2258. 逃离火灾](https://leetcode.cn/problems/escape-the-spreading-fire/)（二分+二次BFS）





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
