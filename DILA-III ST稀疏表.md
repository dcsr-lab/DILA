# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分之Sparse Table（稀疏表） 



#### 1、[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)（ST）

**问题**：

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。

返回 *滑动窗口中的最大值* 。

例子：

```
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

- `1 <= nums.length <= 10^5`
- `-104 <= nums[i] <= 10^4`

- `1 <= k <= nums.length`

**思路**：稀疏表模板题

```java
class Solution {

    public int log2(int x) { // 计算x以2为底的对数
        return (int)(Math.log(x) / Math.log(2) + 1e-10);
    }

    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[][] f = new int[n + 1][22]; // Sparse Table, 下标从1开始, 2^22=4194304远大于10^5
        int[] lg = new int[n + 1];

        for(int i = 1; i <= n; i ++ ) f[i][0] = nums[i - 1]; // 一个数的区间的最大值就是其本身
        for(int i = 0; i <= n; i ++ ) lg[i] = log2(i);

        for(int j = 1; (1 << j) <= n; j ++) // 构建Sparse Table, 2, 4, 8, .., log2(n)
            for(int i = 1; i + (1 << j) - 1 <= n; i ++ ) // 枚举所有的左端点, 区间长度为2^j
                f[i][j] = Math.max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);

        int[] res = new int[n - (k - 1)];
        for(int i = 1; i + k - 1 <= n; i ++) {
            int x = i, y = i + k - 1; // 要查询的区间[x, y]
            int t = lg[y - x + 1]; // 
            res[i - 1] = Math.max(f[x][t], f[y - ((1 << t) - 1)][t]); // 查询
        }
        return res;
    }
}
```



#### 2、[2654. 使数组所有元素变成 1 的最少操作次数](https://leetcode.cn/problems/minimum-number-of-operations-to-make-all-array-elements-equal-to-1/)（二分+ST）

**问题**：

给你一个下标从 **0** 开始的 **正** 整数数组 `nums` 。你可以对数组执行以下操作 **任意** 次：

- 选择一个满足 `0 <= i < n - 1` 的下标 `i` ，将 `nums[i]` 或者 `nums[i+1]` 两者之一替换成它们的最大公约数。

请你返回使数组 `nums` 中所有元素都等于 `1` 的 **最少** 操作次数。如果无法让数组全部变成 `1` ，请你返回 `-1` 。

两个正整数的最大公约数指的是能整除这两个数的最大正整数。

例子：

```
输入：nums = [2,6,3,4]
输出：4
解释：我们可以执行以下操作：
- 选择下标 i = 2 ，将 nums[2] 替换为 gcd(3,4) = 1 ，得到 nums = [2,6,1,4] 。
- 选择下标 i = 1 ，将 nums[1] 替换为 gcd(6,1) = 1 ，得到 nums = [2,1,1,4] 。
- 选择下标 i = 0 ，将 nums[0] 替换为 gcd(2,1) = 1 ，得到 nums = [1,1,1,4] 。
- 选择下标 i = 2 ，将 nums[3] 替换为 gcd(1,4) = 1 ，得到 nums = [1,1,1,1] 。


输入：nums = [2,10,6,14]
输出：-1
解释：无法将所有元素都变成 1 。
```

**数据范围**：

- `2 <= nums.length <= 50`
- `1 <= nums[i] <= 10^6`

**思路**：

首先很容易发现每次只能改变一个数，而>1的数至少需要一次才能变成1，如果已经存在1，那么显然每个数都可以一次变成1，答案就是n-数组中1的数量。

如果数组中不存在1，就要先尝试创造出第一个1，在创造出第一个1后，由于其他数不是1，那么此时的最小次数同上，是n-1。现在考虑创造出1的最小次数，显然是**gcd为1的最小子数组**。因为对于这样的一个子数组，从左右到右取一遍gcd(len-1次操作)就能得到1了。而对于gcd不是1的子数组，显然怎么搞都搞不出1.

所以问题变成了求gcd=1的最小子数组，这里提供一个不需要脑子的做法

因为不带修，所以**ST表直接暴力维护区间GCD**就好了。然后既然要求最小长度，那就直接**二分子数组长度，二分以后暴力check所有这个长度的子数组是否存在gcd为1即可，利用ST表做区间查询**。

时间复杂度大概是O(nlog^2)瓶颈是预处理，但是OI-WIKI上似乎证明了是只有一个log的

```java
class Solution {

    public int gcd(int a, int b) {
        return b != 0 ? gcd(b, a % b) : a;
    }

    public boolean check(int[][] f, int[] lg, int k, int n) {
        for(int i = 1; i + k - 1 <= n; i ++) {
            int l = i, r = i + k - 1;
            int t = lg[r - l + 1];
            if(gcd(f[l][t], f[r - ((1 << t) - 1)][t]) == 1) return true;
        }
        return false;
    }

    public int minOperations(int[] nums) {
        // nums中存在1
        int cnt = 0, n = nums.length; // cnt表示数组中1的个数
        for(int i = 0; i < n; i ++) 
            if(nums[i] == 1) cnt ++ ;
        if(cnt > 0) return n - cnt; // 需要操作 n - cnt, 每一个不为1的元素都需要操作一次才能等于1

        int[][] f = new int[n + 1][22]; // ST,f[i][j]表示区间[i, (1 << j) -1]的最大公约数
        int[] lg = new int[n + 1];
        for(int i = 1; i <= n; i ++) f[i][0] = nums[i - 1]; // 单个数够成的区间的最大公约数是其本身
        for(int i = 2; i <= n; i ++) lg[i] = lg[i >> 1] + 1; // 预处理log2的对数

        for(int j = 1; (1 << j) <= n; j ++ )
            for(int i = 1; i + (1 << j) - 1 <= n; i ++)
                f[i][j] = gcd(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]); // 构建稀疏表

        int l = 1, r = n + 1;
        while(l < r) {
            int mid = (l + r) >> 1;
            if(check(f, lg, mid, n)) r = mid; // 长度为mid的子数组最大公约数为1，则大小大于mid的子数组也为1
            else l = mid + 1; // 否则，增加数组长度, 使得子数组最大公约数尽可能为1
        }
        int d = l;
        if(d == n + 1) return -1; // 无法将所有元素都变成 1
        return d - 1 + n - 1;
    }
}
```

#### 3、[2398. 预算内的最多机器人数目](https://leetcode.cn/problems/maximum-number-of-robots-within-budget/)（二分+ST+前缀和）

**问题**：

你有 `n` 个机器人，给你两个下标从 **0** 开始的整数数组 `chargeTimes` 和 `runningCosts` ，两者长度都为 `n` 。第 `i` 个机器人充电时间为 `chargeTimes[i]` 单位时间，花费 `runningCosts[i]` 单位时间运行。再给你一个整数 `budget` 。

运行 `k` 个机器人 **总开销** 是 `max(chargeTimes) + k * sum(runningCosts)` ，其中 `max(chargeTimes)` 是这 `k` 个机器人中最大充电时间，`sum(runningCosts)` 是这 `k` 个机器人的运行时间之和。

请你返回在 **不超过** `budget` 的前提下，你 **最多** 可以 **连续** 运行的机器人数目为多少。

例子：

```
输入：chargeTimes = [3,6,1,3,4], runningCosts = [2,1,3,4,5], budget = 25
输出：3
解释：
可以在 budget 以内运行所有单个机器人或者连续运行 2 个机器人。
选择前 3 个机器人，可以得到答案最大值 3 。总开销是 max(3,6,1) + 3 * sum(2,1,3) = 6 + 3 * 6 = 24 ，小于 25 。
可以看出无法在 budget 以内连续运行超过 3 个机器人，所以我们返回 3 。


输入：chargeTimes = [11,12,19], runningCosts = [10,8,7], budget = 19
输出：0
解释：即使运行任何一个单个机器人，还是会超出 budget，所以我们返回 0 。
```

**数据范围**：

- `chargeTimes.length == runningCosts.length == n`
- `1 <= n <= 5 * 10^4`
- `1 <= chargeTimes[i], runningCosts[i] <= 10^5`
- `1 <= budget <= 10^15`

**思路**：二分+系数表+前缀和

- 答案一定位于区间 $[0,n]$之间，因此可以采用二分搜索答案。
- 对于给定二分中间值 $mid$，我们需要快速判断**是否存在$mid$个机器人的总开销小于等于$budget$**，并更新二分的左右边界。
- 运行 `k` 个机器人 **总开销** 是 `max(chargeTimes) + k * sum(runningCosts)`。$max(chargeTimes)$ 是求区间的最大值，我们可以采用稀疏表来实现。$sum(runningCosts)$是求区间和，我们可以使用前缀和来实现。
- 二分的时间复杂度是 $O(logn)$；前缀和构造的时间复杂度是 $O(n)$，查询是$O(1)$；稀疏表构造的时间复杂度是 $O(nlogn)$，查询是$O(1)$。总的时间复杂度是 $O(nlogn)$。

```java
class Solution {
    
    public int log2(int x) {
        return (int)(Math.log(x) / (Math.log(2) + 1e-10));
    }
    
    // true: 存在k个机器人的总开销小于等于target
    public boolean check(int[][] f, long[] s, int[] lg, int k, long target) {
        long res = Integer.MAX_VALUE;
        for(int i = 1; i + k - 1 <= s.length - 1; i ++ ) {
            int l = i, r = i + k - 1;

            int t = lg[r - l + 1];
            int charge = Math.max(f[l][t], f[r - ((1 << t) - 1)][t]); // 稀疏表查询
            long sum = s[r] - s[l - 1]; // 前缀和查询

            if (charge + k * sum <= target) return true; // 只要找到存在k个机器人的总开销小于等于target，就返回true
        }
        return false; // 找不到k个机器人的总开销小于等于target, 返回false
    }

    public int maximumRobots(int[] chargeTimes, int[] runningCosts, long budget) {
        int n = chargeTimes.length;
        int[][] f = new int[n + 1][22]; // 稀疏表
        int[] lg = new int[n + 1]; // 预处理以2为底的对数
        long[] s = new long[n + 1]; // 前缀和数组

        for(int i = 1; i <= n; i ++) {
            f[i][0] = chargeTimes[i - 1]; // 一个数的区间的最大值就是其本身
            s[i] = s[i - 1] + runningCosts[i - 1]; // 构造签前缀和数组
            lg[i] = log2(i); // 预处理以2为底的对数
        }

        for(int j = 1; (1 << j) <= n; j ++ ) // 构造稀疏表
            for(int i = 1; i + (1 << j) - 1 <= n; i ++ )
                f[i][j] = Math.max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);

      	// 0表示即使运行任何一个单个机器人，还是会超出 budget
        int l = 0, r = n; // 答案一定位于区间[0,n]之间，因此可以二分搜索答案
        while(l < r) {
            int mid = (l + r + 1) >> 1;
            if(check(f, s, lg, mid, budget)) l = mid;
            else r = mid - 1; 
        }
        return l;
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
