# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之单调队列



#### 1、单调队列

##### （1）[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)（单减队列）

**问题**：

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

返回 *滑动窗口中的最大值* 。

例子：

```java
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**数据范围**：

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`
- `1 <= k <= nums.length`

**思路**：单减队列

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;

        // 用单调减队列维护滑动窗口中的最大值，队头表示滑动窗口的内的最大值
        int[] q = new int[n]; // 单减队列, 存储下标
        int hh = 0, tt = -1; // hh表示队头指针, tt表示队尾指针
        int[] res = new int[n - (k - 1)];

        for(int i = 0; i < n ; i ++ ) { // i += 1表示窗口右移1位
            if(hh <= tt && i - (k - 1) > q[hh]) hh ++; // num[i]滑入窗口, 队头元素滑出窗口
            while(hh <= tt && nums[q[tt]] <= nums[i]) tt --; // 队尾元素出队，直到在队列里找到一个严格大于num[i]的数
            q[++ tt] = i; // 入队

            if(i >= k - 1) res[i - (k - 1)] = nums[q[hh]];
        }
        return res;
    }
}
```

##### （2）[1499. 满足不等式的最大值](https://leetcode.cn/problems/max-value-of-equation/) （单减队列）

**问题**：

给你一个数组 `points` 和一个整数 `k` 。数组中每个元素都表示二维平面上的点的坐标，并**按照横坐标 x 的值从小到大排序**。

也就是说 `points[i] = [xi, yi]` ，并且在 `1 <= i < j <= points.length` 的前提下， `xi < xj` 总成立。

请你找出 `yi + yj + |xi - xj|` 的 **最大值**，其中 `|xi - xj| <= k` 且 `1 <= i < j <= points.length`。

题目测试数据保证至少存在一对能够满足 `|xi - xj| <= k` 的点。

例子

```
输入：points = [[1,3],[2,0],[5,10],[6,-10]], k = 1
输出：4
解释：前两个点满足 |xi - xj| <= 1 ，代入方程计算，则得到值 3 + 0 + |1 - 2| = 4 。第三个和第四个点也满足条件，得到值 10 + -10 + |5 - 6| = 1 。
没有其他满足条件的点，所以返回 4 和 1 中最大的那个。
```

**数据范围**

- `2 <= points.length <= 10^5`
- `points[i].length == 2`
- `-10^8 <= points[i][0], points[i][1] <= 10^8`
- `0 <= k <= 2 * 10^8`
- 对于所有的`1 <= i < j <= points.length` ，`points[i][0] < points[j][0]` 都成立。也就是说，`xi` 是严格递增的。

**思路**：

设$points$数组的大小为 $n$，题目要求找出 $y_i + y_j + |x_i - x_j|$ 的 **最大值**，其中$|x_i - x_j| <= k$ 且$1 <= i < j <= n$。

上述问等于于价求解 $max(y_i + y_j + x_j - x_i)=max(y_i-x_i+y_j+x_j)$。

设数组 $a$，$a[i]=y_i-x_i$。用单减队列求解该数组上滑动窗口的最大值，滑动窗口的大小由参数 $k$ 决定， $x_j-x_i<=k$。

因此，在实现时无需构造$a$数组，可以遍历 $points$ 数组，求出当前滑动窗口内的最大值 $max(y_i - x_i)$，并且当前即将要加入滑动窗口的元素求和 $(y_j+x_j)$，然后取最大值。

```java
for j:= range(0, n)
      res = max(res, max(yi - xi) + (yj + xj)) 且满足 xj - xi <= k, i < j
```

此题其实与[滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)只有微妙区别，相似之处在于：

- 都有一个固定窗口
- 都求的是窗口内的最大值

差异之处在于：

- 本题的窗口其实**不与下标直接关联**，而是呈一种非线性关系，但仍然随下标增加而单调增加
- 本题的**滑动窗口的大小不是固定大小**，其大小与 $k$ 有关，即滑动窗口内 $x$ 的最大值与最小值之差要小于等于 $k$。

因为这个单调性，所以我们才能只检查队首元素是否已经脱离窗口，已经脱离窗口的势必也不可能与后续元素在同一窗口。

```java
class Solution {
    public int findMaxValueOfEquation(int[][] points, int k) {
        int[][] p = points;
        
        int n = p.length;
        int[] q = new int[n + 10];
        int hh = 0, tt = -1;
        
        int res = Integer.MIN_VALUE;
        for(int i = 0; i < n; i ++ ) { // 构造单调队列
            // 队列不为空, 队头元素q[hh]的索引小于滑动窗口左边第一个元素的索引i-(k-1), 则队头元素滑出窗口
            while(hh <= tt && p[i][0] - p[q[hh]][0] > k) hh ++;
            if(hh <= tt) // 用滑动窗口内的最大值和当前元素更新最值
                res = Math.max(res, p[q[hh]][1] - p[q[hh]][0] + p[i][1] + p[i][0]);
            while(hh <= tt && p[q[tt]][1] - p[q[tt]][0] <= p[i][1] - p[i][0]) tt --; // 将i加入滑动窗口, 取最大值要在这一步之前执行，否则i可能会更新滑动窗口内的最大值，导致被更新之前的滑动窗口最大值没有参与到取最值运算
            q[++ tt] = i;
        }
        return res;
    }
}
```



#### 2、单调队列优化DP

##### （1）[2944. 购买水果需要的最少金币数](https://leetcode.cn/problems/minimum-number-of-coins-for-fruits/)

**问题**



思路

```java
class Solution {
    public int minimumCoins(int[] prices) {
        // f[i] 表示获得[i,n]内所有苹果的最小值
        // f[i] = price[i] + f[j],  i+1 <= j <= i+i

        int n = prices.length;

        int[] f = new int[n + 10];
        f[n] = prices[n - 1];
        for(int i = n - 1; i >= 1; i --) {
            
            if(i + i >= n) {
                f[i] = prices[i - 1];
                continue;
            }

            int t = 0x3f3f3f3f;
            for(int j = i + 1; j <= i + 1 + i; j ++)
                t = Math.min(t, f[j]);
            f[i] = prices[i - 1] + t;
        }

        return f[1];
    }
}
```

单调递增队列维护不定长滑动窗口最小值优化二重循环

```java
class Solution {
    public int minimumCoins(int[] prices) {
        // f[i] 表示获得[i,n]内所有苹果的最小值
        // f[i] = price[i] + f[j],  i+1 <= j <= i+i

        int n = prices.length;
        int[] f = new int[n + 10];
        
        // for(int i = n; i >= 1; i --) {
            
        //     if(i + i >= n) {
        //         f[i] = prices[i - 1];
        //         continue;
        //     }

        //     int t = 0x3f3f3f3f;
        //     for(int j = i + 1; j <= i + 1 + i; j ++)
        //         t = Math.min(t, f[j]);
        //     f[i] = prices[i - 1] + t;
        // }

        // 单调递增队列优化二重循环
        int[] q = new int[n + 10];
        int hh = 0, tt = -1;

        for(int i = n; i >= 1; i --) { // 滑动窗口从右往左滑
            while(hh <= tt && i + 1 + i < q[hh]) hh ++; // 判断队头元素是否滑出窗口
            
            if(i + i >= n) f[i] = prices[i - 1];
            else f[i] = prices[i - 1] + f[q[hh]]; // 队头元素就是当前滑动窗口的最小值f[q[hh]]
            
            while(hh <= tt && q[tt] <= i + i - 1 && f[q[tt]] >= f[i]) tt --; // 入队

            q[++ tt] = i;
        }

        return f[1];
    }
}
```



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
