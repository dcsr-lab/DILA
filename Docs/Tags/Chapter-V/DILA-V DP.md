# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之动态规划

### 1、线性 DP

#### （1）[45. 跳跃游戏 II](https://leetcode.cn/problems/jump-game-ii/) 

**问题**：

给定一个长度为 `n` 的 **0 索引**整数数组 `nums`。初始位置为 `nums[0]`。

每个元素 `nums[i]` 表示从索引 `i` 向前跳转的最大长度。换句话说，如果你在 `nums[i]` 处，你可以跳转到任意 `nums[i + j]` 处:

- `0 <= j <= nums[i]` 
- `i + j < n`

返回到达 `nums[n - 1]` 的最小跳跃次数。生成的测试用例可以到达 `nums[n - 1]`。

例子：

```java
输入: nums = [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
```

**数据范围**：

- `1 <= nums.length <= 104`
- `0 <= nums[i] <= 1000`
- 题目保证可以到达 `nums[n-1]`

**思路**：

- 转态表示：$f[i]$ 表示从$nums[0]$跳到$nums[i]$所需要的最小跳数。
- $nums[i]$表示在 $i$位置的最大跳跃数，表示$i$后面的$nums[i]$个位置都可以作为起跳点。设该起跳点为$j$，用$f[i]$去更新$f[j]$，可以表示为$f[j] = Math.min(f[j], f[i] + 1)$

```java
class Solution {
    public int jump(int[] nums) {
        if (nums.length == 1) return 0; // 特判
        // 状态
        int[] f = new int[nums.length];
        // 初始化状态
        for(int i = 0; i < nums.length; i ++) f[i] = Integer.MAX_VALUE;
        f[0] = 0;

        for(int i = 0; i < nums.length; i ++ ) {
            for(int j = i + 1; j <= i + nums[i] && j < nums.length; j ++ ) {
                f[j] = Math.min(f[j], f[i] + 1);
            }
        }
        return f[nums.length - 1];
    }
}
```

#### （2）[1262. 可被三整除的最大和](https://leetcode.cn/problems/greatest-sum-divisible-by-three/) 

**问题**：

给你一个整数数组 `nums`，请你找出并返回能被三整除的元素最大和。

例子：

```
输入：nums = [3,6,5,1,8]
输出：18
解释：选出数字 3, 6, 1 和 8，它们的和是 18（可被 3 整除的最大和）。
```

**数据范围**：

- `1 <= nums.length <= 4 * 10^4`
- `1 <= nums[i] <= 10^4`

**思路**：

状态表示：$f[i][j]$ 表示$nums[:j - 1]$中除以$3$余数为$i$的子序列的**集合**，值表示**和的最大值**

状态更新：

$f[i][j]$表示的集合可以划分为两个部分：不包含最后一个元素$nums[j-1]$的子集$f[i][j-1]$；包含最后一个元素$nums[j-1]$的子集

第一部分：$f[i][j] = Math.max(f[i][j], f[i][j - 1]$);

第二部分：从包含$nums[j-1]$的子集中去除$nums[j-1]$后，剩余元素之和模3的余数为 $t = (i + 3 - nums[j] \ \% \ 3) \ \% \ 3$ 

如果$nums[j-1]$恰好模3的余数为$i$时： $if(nums[j - 1] \ \% 3 == i) \ \ f[i][j] = Math.max(f[i][j], f[0][j - 1] + nums[j - 1])$;

否则，**存在**剩余元素之和模3的余数为$t$这样的集合 $else \ \ if(f[t][j - 1] != 0) \ \ f[i][j] = Math.max(f[i][j], f[t][j - 1] + nums[j - 1])$;

```java
class Solution {
    public int maxSumDivThree(int[] nums) {
        int n = nums.length;
        int[][] f = new int[3][n + 1]; // f[i][j] 表示nums[:j - 1]中除以3余数为i的子序列的集合，值表示和的最大值

        for(int j = 1; j <= n; j ++) {
            for(int i = 0; i < 3; i ++) {
                f[i][j] = Math.max(f[i][j], f[i][j - 1]);
                if(nums[j - 1] % 3 == i) f[i][j] = Math.max(f[i][j], f[0][j - 1] + nums[j - 1]);
                else {
                    int t = (i + 3 - nums[j - 1] % 3) % 3;
                    if(f[t][j - 1] != 0) f[i][j] = Math.max(f[i][j], f[t][j - 1] + nums[j - 1]);
                }
            }
        }
        return f[0][n];
    }
}
```

#### （3）[1449. 数位成本和为目标值的最大数字](https://leetcode.cn/problems/form-largest-integer-with-digits-that-add-up-to-target/)（完全背包问题）

**问题：**

给你一个整数数组 `cost` 和一个整数 `target` 。请你返回满足如下规则可以得到的 **最大** 整数：

- 给当前结果添加一个数位（`i + 1`）的成本为 `cost[i]` （`cost` 数组下标从 0 开始）。
- 总成本必须恰好等于 `target` 。
- 添加的数位中没有数字 0 。

由于答案可能会很大，请你以字符串形式返回。

如果按照上述要求无法得到任何整数，请你返回 "0" 。

例子：

```
输入：cost = [4,3,2,5,6,7,2,5,5], target = 9
输出："7772"
解释：添加数位 '7' 的成本为 2 ，添加数位 '2' 的成本为 3 。所以 "7772" 的代价为 2*3+ 3*1 = 9 。 "977" 也是满足要求的数字，但 "7772" 是较大的数字。
 数字     成本
  1  ->   4
  2  ->   3
  3  ->   2
  4  ->   5
  5  ->   6
  6  ->   7
  7  ->   2
  8  ->   5
  9  ->   5
```

**数据范围**：

- `cost.length == 9`
- `1 <= cost[i] <= 5000`
- `1 <= target <= 5000`

**思路**：此题是经典的完全背包问题

因此题目可以转换为 从 $cost.length$ 个物品中选，总体积不超过$target$，$cost[i]$表示第$i$ 个物品的体积。

每个物品可以选择无限次，求出**最大的「价值」**，这里的价值是指**所选出的数构成成的整数数值最大**。

状态表示 $f[i][j]$ 表示从前 $i$ 个物品中选，总体积**恰好是** $j$ 的方案的集合。属性表示最大「价值」。

需要注意一下问题：

- 因为数位$i$遍历的顺序是从小到大，保证**整数的高位一定大于等于整数的低位**，也就保证了整数最大；
- 为了保证我们 $dp[j]$ 中的数位的 $cost$ 相加一定等于 $j$，而没有小于 $j$ 的情况，我们特地**用字符串"0"来表示无法得到的目标值**，这样我们在进行状态转移的时候，就**不会进行不合法的转移**；
- 而为了得到我们的目标值，进行正确的状态转移，我们又需要把 $dp[0]$ 初始化为**“”**,因为**对于任何$cost$的值来说，加上0都是刚好花费$cost$得到的目标字符串，正好是合法的情况**。

```java
class Solution {

    public boolean check(String a, String b) {
        if(a.length() == b.length()) return a.compareTo(b) < 0;
        return a.length() < b.length(); 
    }

    public String largestNumber(int[] cost, int target) {
        final int N = 15, V = 5010;
        String[][] f = new String[N][V]; // f[i][j]表示从前i个物品中选，总体积恰好是j的方案
        int n = cost.length, m = target;

        for(int i = 0; i < N; i ++ ) Arrays.fill(f[i], "0");
        f[0][0] = "";

        for(int i = 1; i <= n; i ++) 
            for(int j = 0; j <= m; j ++) {
                f[i][j] = f[i - 1][j];
              	// 在转换的时候，必须使用条件进行特判 !f[i][j - cost[i - 1]].equals("0")
                if(j >= cost[i - 1] && !f[i][j - cost[i - 1]].equals("0")) { // 
                    if(f[i][j].equals("0") || check(f[i][j], i + f[i][j - cost[i - 1]])) 
                        f[i][j] = i + f[i][j - cost[i - 1]];
                }   
            }
        return f[n][m];
    }
}
```

**滚动数组优化**

```java
class Solution {

    public boolean check(String a, String b) {
        if(a.length() == b.length()) return a.compareTo(b) < 0;
        return a.length() < b.length(); 
    }

    public String largestNumber(int[] cost, int target) {
        final int N = 15, V = 5010;
        String[] f = new String[V]; // f[i][j]表示从前i个物品中选，总体积恰好是j的方案
        int n = cost.length, m = target;

        Arrays.fill(f, "0");
        f[0] = "";

        for(int i = 1; i <= n; i ++) 
            for(int j = 0; j <= m; j ++) {
                if(j >= cost[i - 1] && !f[j - cost[i - 1]].equals("0")) {
                    if(f[j].equals("0") || check(f[j], i + f[j - cost[i - 1]])) 
                        f[j] = i + f[j - cost[i - 1]];
                }   
            }
        return f[m];
    }
}
```

#### （4）[931. 下降路径最小和](https://leetcode.cn/problems/minimum-falling-path-sum/)（路径问题）

**问题**：

给你一个 `n x n` 的 **方形** 整数数组 `matrix` ，请你找出并返回通过 `matrix` 的**下降路径** 的 **最小和** 。

**下降路径** 可以从第一行中的任何元素开始，并从每一行中选择一个元素。在下一行选择的元素和当前行所选元素最多相隔一列（即位于正下方或者沿对角线向左或者向右的第一个元素）。具体来说，位置 `(row, col)` 的下一个元素应当是 `(row + 1, col - 1)`、`(row + 1, col)` 或者 `(row + 1, col + 1)` 。

例子

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/failing1-grid.jpg" alt="img" style="zoom:67%;" />



```
输入：matrix = [[2,1,3],[6,5,4],[7,8,9]]
输出：13
解释：如图所示，为和最小的两条下降路径
```

**数据范围**：

- `n == matrix.length == matrix[i].length`
- `1 <= n <= 100`
- `-100 <= matrix[i][j] <= 100`

**思路**：

状态表示 $f[i][j]$表示所有从位置$[n-1][0,...,n-1]$到达 $[i][j]$的路径集合，属性是路径和的最小值。

集合划分：其中，只能由 $(i+1,j),(i+1,j-1),(i+1,j+1)$ 位置达到位置 $(i,j)$。
$$
f[i][j] = min(f[i+1][j], f[i+1][j-1],f[i+1][j+1])+matrix[i][j+1]
$$


最后，$res=min(f[0][0,...,n-1])$

```java
class Solution {
    public int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        int[][] f = new int[n][n];

        for(int i = n - 1; i >= 0; i --)
            for(int j = n - 1; j >= 0; j --) {
                f[i][j] = (i == n - 1? matrix[i][j] : 0x3f3f3f3f);
                if(i + 1 < n) f[i][j] = Math.min(f[i][j], matrix[i][j] + f[i + 1][j]);
                if(i + 1 < n && j - 1 >= 0) f[i][j] = Math.min(f[i][j], matrix[i][j] + f[i + 1][j - 1]);
                if(i + 1 < n && j + 1 < n) f[i][j] = Math.min(f[i][j], matrix[i][j] + f[i + 1][j + 1]);
            }

        int res = 0x3f3f3f3f;
        for(int j = 0; j < n; j ++) res = Math.min(res, f[0][j]);
        return res;
    }
}
```

#### （5）[64. 最小路径和](https://leetcode.cn/problems/minimum-path-sum/)（路径问题）

**问题**

给定一个包含非负整数的 `*m* x *n*` 网格 `grid` ，请找出一条从左上角到右下角的路径，**使得路径上的数字总和为最小**。

**说明：**每次只能向下或者向右移动一步。

例子

<img src="https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg" alt="img" style="zoom: 67%;" />

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**数据范围**

- `m == grid.length`
- `n == grid[i].length`

- `1 <= m, n <= 200`
- `0 <= grid[i][j] <= 200`

**思路**

状态表示 $f[i][j]$ 从起点$(0,0)$到$(i,j)$所有路径的集合，属性是**路径上的数字总和的最小值**。

初始化：$f[0...n][0] = 0x3f3f3f3f,f[0][0...m]=0x3f3f3f3f$。

状态转移 $f[i][j]=Math.min(f[i-1][j],f[i][j-1])+grid[i][j]$

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        int[][] f = new int[n + 1][m + 1];
        
        // init
        for(int i = 0; i < n + 1; i ++) f[i][0] = 0x3f3f3f3f;
        for(int j = 0; j < m + 1; j ++) f[0][j] = 0x3f3f3f3f;
        f[1][1] = grid[0][0];

        for(int i = 1; i <= n; i ++ )
            for(int j = 1; j <= m; j ++) {
                if(i == 1 && j == 1) continue;
                f[i][j] = Math.min(f[i - 1][j], f[i][j - 1]) + grid[i - 1][j - 1];
            }

        return f[n][m];
    }
}
```



#### （6）[62. 不同路径](https://leetcode.cn/problems/unique-paths/)（路径问题）

**问题**

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

例子

<img src="https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png" alt="img" style="zoom:67%;" />

```
输入：m = 3, n = 7
输出：28
```

**数据范围:**

- `1 <= m, n <= 100`
- 题目数据保证答案小于等于 `2 * 10^9`

**思路**：

状态表示 $f[i][j]$ 表示从起点 $(0,0)$到位置 $(i,j)$的不同路径数量。

状态转移 $f[i][j] = Math.max(f[i - 1][j],f[i][j - 1])$

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] f = new int[m][n];
        for(int i = 0; i < m; i ++) f[i][0] = 1;
        for(int i = 0; i < n; i ++) f[0][i] = 1;

        for(int i = 1; i < m; i ++)     
            for(int j = 1; j < n; j ++)   
                f[i][j] += (f[i - 1][j] + f[i][j - 1]);

        return f[m - 1][n - 1];
    }
}
```

#### （7）[63. 不同路径 II](https://leetcode.cn/problems/unique-paths-ii/)（路径问题）

**问题**

一个机器人位于一个 $m x n$ 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

例子

<img src="https://assets.leetcode.com/uploads/2020/11/04/robot1.jpg" alt="img" style="zoom:67%;" />

```
输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
输出：2
解释：3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

**数据范围**

- `m == obstacleGrid.length`
- `n == obstacleGrid[i].length`
- `1 <= m, n <= 100`
- `obstacleGrid[i][j]` 为 `0` 或 `1`

**思路**：

状态表示 $f[i][j]$ 表示从起点 $(0,0)$到位置 $(i,j)$的不同路径数量。

状态转移 :

- 如果 $(i，j)$ 位置不是障碍物，$f[i][j] = Math.max(f[i - 1][j],f[i][j - 1])$
- 如果 $(i，j)$ 位置是障碍物， 则 $f[i][j] = 0$

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int n = obstacleGrid.length, m = obstacleGrid[0].length;
        int[][] f = new int[n][m];

        for(int i = 0; i < n && obstacleGrid[i][0] == 0; i ++) f[i][0] = 1;
        for(int i = 0; i < m && obstacleGrid[0][i] == 0; i ++) f[0][i] = 1;

        for(int i = 1; i < n; i ++)
            for(int j = 1; j < m; j ++)
                if(obstacleGrid[i][j] == 1) f[i][0] = 0;
                else f[i][j] = (f[i - 1][j] + f[i][j - 1]);

        return f[n - 1][m - 1];
    }
}
```

#### （8）[1035. 不相交的线](https://leetcode.cn/problems/uncrossed-lines/)（最长公共子序列）

**问题**

在两条独立的水平线上按给定的顺序写下 `nums1` 和 `nums2` 中的整数。

现在，可以绘制一些连接两个数字 `nums1[i]` 和 `nums2[j]` 的直线，这些直线需要同时满足满足：

-  `nums1[i] == nums2[j] ` 
-  且绘制的直线不与任何其他连线（非水平线）相交。

请注意，连线即使在端点也不能相交：每个数字只能属于一条连线。

以这种方法绘制线条，并返回可以绘制的最大连线数。

例子

<img src="https://assets.leetcode.com/uploads/2019/04/26/142.png" alt="img" style="zoom: 25%;" />

```
输入：nums1 = [1,4,2], nums2 = [1,2,4]
输出：2
解释：可以画出两条不交叉的线，如上图所示。 
但无法画出第三条不相交的直线，因为从 nums1[1]=4 到 nums2[2]=4 的直线将与从 nums1[2]=2 到 nums2[1]=2 的直线相交。
```

**数据范围**：

- `1 <= nums1.length, nums2.length <= 500`
- `1 <= nums1[i], nums2[j] <= 2000`

**思路**: 最长公共子序列模板题

```java
class Solution {
    public int maxUncrossedLines(int[] nums1, int[] nums2) {
        int n = nums1.length, m = nums2.length;
        // f[i][j]表示考虑nums1中的前i位，nums2中的前j位中的公共子序列的集合，属性是公共子序列的最大长度
        int[][] f = new int[n + 1][m + 1];

        for(int i = 1; i <= n; i ++)
            for(int j = 1; j <= m; j ++) {
                f[i][j] = Math.max(f[i - 1][j], f[i][j - 1]);
                if(nums1[i - 1] == nums2[j - 1]) 
                    f[i][j] = Math.max(f[i][j], f[i - 1][j - 1] + 1);
            }
        return f[n][m];
    }
}
```

#### （9）[583. 两个字符串的删除操作](https://leetcode.cn/problems/delete-operation-for-two-strings/)（最长公共子序列）

**问题**

给定两个单词 `word1` 和 `word2` ，返回使得 `word1` 和 `word2` **相同**所需的**最小步数**。

**每步** 可以删除任意一个字符串中的一个字符。

例子

```
输入: word1 = "sea", word2 = "eat"
输出: 2
解释: 第一步将 "sea" 变为 "ea" ，第二步将 "eat "变为 "ea"
```

**数据范围** 

- `1 <= word1.length, word2.length <= 500`
- `word1` 和 `word2` 只包含小写英文字母

**思路** 最长公共子序列模板题

求 $word1$ 和 $word2$ 的**最长公共子序列**，不妨设最长公共子序列的长度为 $len$。

**每步** 可以删除任意一个字符串中的一个字符，使得 `word1` 和 `word2` **相同**所需的**最小步数**为 $word1.length + word2.length - 2 * len$。

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int n = word1.length(), m = word2.length();
        // f[i][j]表示考虑nums1中的前i位，nums2中的前j位中的公共子序列的集合，属性是公共子序列的最大长度
        int[][] f = new int[n + 1][m + 1];

        for(int i = 1; i <= n; i ++) 
            for(int j = 1; j <= m; j ++) {
                f[i][j] = Math.max(f[i - 1][j], f[i][j - 1]);
                if(word1.charAt(i - 1) == word2.charAt(j - 1)) 
                    f[i][j] = Math.max(f[i][j], f[i - 1][j - 1] + 1);
            }
        return n - f[n][m] + m - f[n][m];
    }
}
```

#### （10）[712. 两个字符串的最小ASCII删除和](https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/) （最长公共子序列）

**问题**

给定两个字符串`s1` 和 `s2`，返回 *使两个字符串相等所需删除字符的 **ASCII** 值的最小和* 。

例子

```
输入: s1 = "sea", s2 = "eat"
输出: 231
解释: 在 "sea" 中删除 "s" 并将 "s" 的值(115)加入总和。
在 "eat" 中删除 "t" 并将 116 加入总和。
结束时，两个字符串相等，115 + 116 = 231 就是符合条件的最小和。
```

**数据范围**

- `0 <= s1.length, s2.length <= 1000`
- `s1` 和 `s2` 由小写英文字母组成

**思路**

求 $s1$ 和 $s2$ 的**最长公共子序列**，不妨设最长公共子序列的ASCII之和为 $S$，字符串$s1$的所有字符的ASCII之和为 $S1$，字符串$s2$的所有字符的ASCII之和为 $S2$。则*使两个字符串相等所需删除字符的 **ASCII** 值的最小和* 为 $S1+S2-2\times S$。

所以，此题实际上是要**求具体的方案**，即最长公共子序列是什么。

```java
class Solution {
    public int minimumDeleteSum(String s1, String s2) {
        int n = s1.length(), m = s2.length();
      	// f[i][j]表示考虑nums1中的前i位，nums2中的前j位中的公共子序列的集合，属性是公共子序列的最大长度
        int[][] f = new int[n + 1][m + 1];
      	// f[i][j]表示考虑nums1中的前i位，nums2中的前j位中的公共子序列的集合，属性是公共子序列的最大ASCII之和
        int[][] s = new int[n + 1][m + 1];

        int a = 0, b = 0;
        for(int i = 0; i < Math.max(n, m); i ++) {
            if(i < n) a += (int)s1.charAt(i);
            if(i < m) b += (int)s2.charAt(i);
        }

        for(int i = 1; i <= n; i ++) 
            for(int j = 1; j <= m; j ++) {
                if(f[i - 1][j] < f[i][j - 1]) {
                    f[i][j] = f[i][j - 1];
                    s[i][j] = s[i][j - 1];
                } else if(f[i - 1][j] > f[i][j - 1]){
                    f[i][j] = f[i - 1][j];
                    s[i][j] = s[i - 1][j];
                } else {
                    f[i][j] = f[i - 1][j];
                    s[i][j] = Math.max(s[i - 1][j], s[i][j - 1]);
                }
                if(s1.charAt(i - 1) == s2.charAt(j - 1)) {
                    if(f[i][j] < f[i - 1][j - 1] + 1) {
                        f[i][j] = f[i - 1][j - 1] + 1;
                        s[i][j] = s[i - 1][j - 1] + (int)s1.charAt(i - 1);
                    } else if(f[i][j] == f[i - 1][j - 1] + 1) 
                         s[i][j] = Math.max(s[i][j], s[i - 1][j - 1] + (int)s1.charAt(i - 1));
                }
            }
        return a - s[n][m] + b - s[n][m];
    }
}
```

#### （11）[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)（最长上升子序列）

**问题**

给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

**子序列** 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]` 是数组 `[0,3,1,6,2,2,7]` 的子序列。

 例子

```
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

**数据范围**

- `1 <= nums.length <= 2500`
- `-10^4 <= nums[i] <= 10^4`

**思路** 最长上升子序列模板题

$f[i]$表示所有以$nums[i]$结尾的单增子序列的集合，属性是长度的最大值

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] f = new int[n + 1]; // f[i]表示所有以nums[i]结尾的单增子序列的集合，属性是长度的最大值

        for(int i = 1; i <= n; i ++) 
            for(int j = 0; j < i; j ++) 
                if((j == 0 || nums[j - 1] < nums[i - 1]) && f[i] < f[j] + 1) f[i] = f[j] + 1;

        int k = 0;
        for(int i = 1; i <= n; i ++ ) 
            if(f[k] < f[i]) k = i;
        return f[k];
    }
}
```

#### （12）[673. 最长递增子序列的个数](https://leetcode.cn/problems/number-of-longest-increasing-subsequence/)（最长上升子序列）

**问题**

给定一个未排序的整数数组 `nums` ， *返回最长递增子序列的个数* 。**注意** 这个数列必须是 **严格** 递增的。

例子

```
输入: [1,3,5,4,7]
输出: 2
解释: 有两个最长递增子序列，分别是 [1, 3, 4, 7] 和[1, 3, 5, 7]。
```

**数据范围**

- `1 <= nums.length <= 2000`
- `-10^6 <= nums[i] <= 10^6`

**思路** 最长上升子序列**求方案数**模板题

$f[i]$表示所有以$nums[i]$结尾的单增子序列的集合，属性是长度的最大值

```java
class Solution {
    public int findNumberOfLIS(int[] nums) {
        int n = nums.length;
        int[] f = new int[n + 1]; // f[i]表示所有以nums[i]结尾的单增子序列的集合，属性是长度的最大值
        int[] s = new int[n + 1];
        
        s[0] = 1; // 初始化
        for(int i = 1; i <= n; i ++)
            for(int j = 0; j < i; j ++)
                if(j == 0 || nums[j - 1] < nums[i - 1])
                    if(f[i] < f[j] + 1) {
                        f[i] = f[j] + 1;
                        s[i] = s[j];
                    } else if(f[i] == f[j] + 1) s[i] += s[j];

        int k = 0;
        for(int i = 1; i <= n; i ++) 
            if(f[k] < f[i]) k = i;

        int cnt = 0;
        for(int i = 1; i <= n; i ++)
            if(f[k] == f[i]) cnt += s[i];
        return cnt;            
    }
}
```

#### （13）[646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/)（最长上升子序列）

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

已知$p1 = [a, b]$ 和 $p2 = [c, d]$，当且仅当 $b < c$ 时，我们定义 $p2$ 可以 **跟随** $p1$。求用这样的方式构造的**数对链的最大长度**。

首先将所有的数对 $pairs[i] = [lefti, righti]$ 按 $left_i$ 从小达到排序。

然后使用**最长上升子序列**的方式求数对链的最大长度。

$f[i]$ 表示所有以$pair[i]$结尾的数对链的集合，属性是数对链的长度的最大值。

其中，相邻两个数对需要满足 $pairs[j - 1][1] < pairs[i - 1][0]$

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        int n = pairs.length;
        Arrays.sort(pairs, (a, b) -> a[0] - b[0]); // 使得开始时间严格单调递增

        int[] f = new int[n + 1];

        for(int i = 1; i <= n; i ++)
            for(int j = 0; j < i; j ++)
                if(j == 0 || pairs[j - 1][1] < pairs[i - 1][0])
                    f[i] = Math.max(f[i], f[j] + 1);
        
        int k = 0;
        for(int i = 1; i <= n; i ++)
            if(f[k] < f[i]) k = i;
        return f[k];
    }
}
```



#### （14）[70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

**问题**

假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。

每次你可以爬 `1` 或 `2` 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**数据范围**：

- `1 <= n <= 45`

**思路**

```java
class Solution {
    public int climbStairs(int n) {
        int[] f = new int[n + 10];
        f[1] = 1; f[2] = 2;
        for(int i = 3; i <= n; i ++) f[i] = f[i - 1] + f[i - 2];
        return f[n];
    }
}
```

#### （15）[377. 组合总和 Ⅳ](https://leetcode.cn/problems/combination-sum-iv/)（爬楼梯模型）

**问题**

给你一个由 **不同** 整数组成的数组 `nums` ，和一个目标整数 `target` 。请你从 `nums` 中找出并返回总和为 `target` 的元素组合的个数。

题目数据保证答案符合 32 位整数范围。

**数据范围**：

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 1000`
- `nums` 中的所有元素 **互不相同**
- `1 <= target <= 1000`

**思路**

问题转换： **楼梯的长度为target,每次爬楼梯可选的层数从nums数组中挑选，问有几种爬法？**

```java
class Solution {

    public int combinationSum4(int[] nums, int target) {
        Arrays.sort(nums);
        
        int[] f = new int[target + 10];
        f[0] = 1;

        for(int i = 1; i <= target; i ++ ) 
            for(int j = 1; j <= nums.length; j ++ ) 
                if(nums[j - 1] <= i)
                    f[i] += f[i - nums[j - 1]];
        return f[target];
    }
}
```



#### （16）[828. 统计子串中的唯一字符](https://leetcode.cn/problems/count-unique-characters-of-all-substrings-of-a-given-string/)（哈希表）

**问题**

我们定义了一个函数 `countUniqueChars(s)` 来统计字符串 `s` 中的唯一字符，并返回唯一字符的个数。

例如：`s = "LEETCODE"` ，则其中 `"L"`, `"T"`,`"C"`,`"O"`,`"D"` 都是唯一字符，因为它们只出现一次，所以 `countUniqueChars(s) = 5` 。

本题将会给你一个字符串 `s` ，我们需要返回 `countUniqueChars(t)` 的总和，其中 `t` 是 `s` 的子字符串。输入用例保证返回值为 32 位整数。

注意，某些子字符串可能是重复的，但你统计时也必须算上这些重复的子字符串（也就是说，你必须统计 `s` 的所有子字符串中的唯一字符）。

**数据范围**

- `1 <= s.length <= 10^5`
- `s` 只包含大写英文字符

**思路**

题目要求给定字符串的所有**子字符串**的**唯一**字符的个数。

定义数组 `int[] f`, 其中 $f[i]$ 表示 字符$s[i]$ 只出现一次的所有子字符串的集合。其属性是集合元素的个数。$f[i]$的值表示了字符$s[i]$对唯一字符的个数的贡献值。

如何快速($O(n)$)求出 $f[i]$？

可以预处理出两个数组，一个 `int[] pre`，另一个是`int[] post`。其中，$pre[i]$表示在其左边第一次出现的位置，$post[i]$ 表示在其右边第一次出现的位置。

那么，$f[i] = (i - pre[i]) * (post[i] - i)$。

注意，初始化 $ pre[i] = 0,post[i] = n + 1$。数组从下标$1$开始枚举。

```java
class Solution {
    public int uniqueLetterString(String s) {
        int n = s.length();
        int[] f = new int[n + 1]; // 

        HashMap<Integer, Integer> map = new HashMap<>();

        int[] pre = new int[n + 1];
        int[] post = new int[n + 1];

        for(int i = 1; i <= n; i ++) {
            pre[i] = 0;
            post[i] = n + 1;
        }

        for(int i = 1; i <=n; i ++) {
            int t = map.getOrDefault(s.charAt(i - 1) - 'A', 0);
            if(t != 0) {
                pre[i] = t;
                post[t] = i;
            }
            map.put(s.charAt(i - 1) - 'A', i);
        }

        for(int i = 1; i <= n; i ++) 
            f[i] = (i - pre[i]) * (post[i] - i);

        int res = 0;
        for(int i = 1; i <= n; i ++)
            res += f[i];

        return res;
    }
}
```



#### （17）[907. 子数组的最小值之和](https://leetcode.cn/problems/sum-of-subarray-minimums/)（单增栈）

**问题**

给定一个整数数组 `arr`，找到 `min(b)` 的总和，其中 `b` 的范围为 `arr` 的每个（连续）子数组。

由于答案可能很大，因此 **返回答案模 `10^9 + 7`** 。

**数据范围**

- `1 <= arr.length <= 3 * 10^4`
- `1 <= arr[i] <= 3 * 10^4`

**思路**

题目要求求出一个整数数组的所有子数组的最小值的总和。

定义数组 `int[] f`, 其中$f[i]$ 表示以$arr[i]$为最小值的所有子数组构成的集合。其属性是集合元素的个数 $\times arr[i]$ 

那么，`f`数组之和就是我们要求的答案。

如何快速($O(n)$)求出以$arr[i]$为最小值的所有子数组的构成的集合？

可以考虑使用「单调栈」。单增栈可以快速求出每一个元素其最左边（或最右边）第一个比其小的元素。

不妨记当前元素为 $arr[i]$，那么其左边第一个比$arr[i]$小的元素的索引位置记为$j$，其右边第一个比$arr[i]$小的元素记为$k$。

所以，$[j+1,i-1]$ 和$[i + 1,k-1]$区间中的所有元素都比$arr[i]$大。

可知，以$arr[i]$为最小值的所有子数组构成的集合的元素格式为 $(i - j)\times(k-i)$。

注意，需要设置哨兵，$j$ 默认初始化为$-1$, $k$ 默认初始化为 $n$。数组从下标$0$开始枚举。

```c++
class Solution {
    public int sumSubarrayMins(int[] arr) {
        int n = arr.length;

        int mod = (int)1e9 + 7;

        int[] stk = new int[n + 10];
        int tt = 0;

        int[] l = new int[n]; // l[i] 表示arr[i]左边第一个比arr[i]小的元素的索引
        int[] r = new int[n]; // r[i] 表式arr[i]右边第一个比arr[i]小的元素的索引

        for(int i = 0; i < n; i ++) {
            while(tt != 0 && arr[stk[tt]] > arr[i]) tt --; // [71,55,82,55], 因为存在重复元素的原因, 重复计算子数组, 两次单调栈只取一次等号即可
            if(tt != 0) l[i] = stk[tt];
            else l[i] = -1;
            stk[++ tt] = i;
        }

        tt = 0;
        for(int i = n - 1; i >= 0; i --) {
            while(tt != 0 && arr[stk[tt]] >= arr[i]) tt --;
            if(tt != 0) r[i] = stk[tt];
            else r[i] = n;;
            stk[++ tt] = i;
        }

        long [] f = new long[n];
        for(int i = 0; i < n; i ++)
            f[i] = (((long)(i - l[i]) * (r[i] - i)) % mod * (long)arr[i]) % mod;

        long res = 0;
        for(int i = 0; i < n; i ++)
            res = (res + f[i]) % mod;
        
        return (int) res;
    }
}
```



### 2、树形DP

#### （1）[1130. 叶值的最小代价生成树](https://leetcode.cn/problems/minimum-cost-tree-from-leaf-values/)（树形DP+递归）

**问题**：

给你一个正整数数组 `arr`，考虑所有满足以下条件的二叉树：

- 每个节点都有 `0` 个或是 `2` 个子节点。
- 数组 `arr` 中的值与树的中序遍历中每个叶节点的值一一对应。
- 每个非叶节点的值等于其左子树和右子树中叶节点的最大值的乘积。

在所有这样的二叉树中，**返回每个非叶节点的值的最小可能总和**。这个和的值是一个 32 位整数。

如果一个节点有 0 个子节点，那么该节点为叶节点。

例子：

<img src="https://assets.leetcode.com/uploads/2021/08/10/tree1.jpg" alt="img" style="zoom:80%;" />



```
输入：arr = [6,2,4]
输出：32
解释：有两种可能的树，第一种的非叶节点的总和为 36 ，第二种非叶节点的总和为 32 。 
```

**数据范围**：

- `2 <= arr.length <= 40`
- `1 <= arr[i] <= 15`
- 答案保证是一个 32 位带符号整数，即小于 `2^31` 。

**思路**：

**动态规划+递归**：

集合表示：$f[i,j]$表示以$arr[i],...,arr[j]$为叶子节点构成**所有满足条件二叉树的集合**，其属性是**非叶子节点最小总和**。

集合划分：这种划分一定是**不遗漏的**，但是**会有重复划分**和搜素，可以采用**记忆化搜素提高速度**。

对于任意一个集合 $(i, j)$, 该集合中的所有树的左子树来自于集合$(i, k)$，右子树来自于集合$(k+1, j)$，$ i <= k <= j$。

$for \ \  each \ \ i <= k <= j$: 

$res = max(res, max(arr[i],...,arr[k]) * max(arr[k + 1], ..., arr[j]) + f(i, k) + f(k + 1, j))$

```java
class Solution {

    public int dfs(int l, int r, int[] arr, int[][] f) {
        if(r <= l) return f[l][r];
        if(f[l][r] != 0) return f[l][r]; // 记忆化搜素

        int res = Integer.MAX_VALUE;
        // 对于任意一个集合(i, j), 
        // 该集合中的所有树的左子树来自于集合(i, k),右子树来自于集合(k+1, j), i <= k <= j
        for(int k = l; k < r; k ++ ) {
            int a = dfs(l, k, arr, f);
            int b = dfs(k + 1, r, arr, f);

            int c = Integer.MIN_VALUE, d = Integer.MIN_VALUE;
            for(int i = l; i <= k; i ++) c = Math.max(c, arr[i]);
            for(int j = k + 1; j <= r; j ++ ) d = Math.max(d, arr[j]);
            res = Math.min(res, a + b + c * d);
        }
        f[l][r] = res; // 状态更新
        return res;
    }

    public int mctFromLeafValues(int[] arr) {
        int n = arr.length;
        // f[i,j]表示以arr[i],...,arr[j]为叶子节点构成所有满足条件二叉树的集合，其属性是非叶子节点最小总和。
        int[][] f = new int[n + 10][n + 10];

        dfs(0, n - 1, arr, f);
        return f[0][n - 1];
    }
}
```

**动态规划+遍历**

已知数组 $arr$ 与 二叉树的中序遍历的所有**叶子节点**对应，并且二叉树的每个节点都有 $0$个节点或2个节点。

考虑到数组 $arr$ 可以生成所有二叉树，我们可以将 $arr$ 切分成任意两个非空子数组，分别对应左子树和右子树，然后递归地对两个非空子子数组执行相同的操作，直到子数组的大小等于 1，即叶子节点，那么一种切分方案对应一个合法的二叉树。

使用$dp[i][j]$表示子数组$[i,j](i<j)$对应的子树所有非叶子节点的最小总和，那么 $dp[i][j]$可以通过切分子树求得，状态转移方程如下：
$$
p[i][j]= \begin{cases}0, & i=j \\ \min _{k \in[i, j)}\left(d p[i][k]+d p[k+1][j]+m_{i k} \times m_{(k+1) j}\right), & i<j\end{cases}
$$
其中，$m_{ik}$表示子数组$[i,k]$的最大值，可以预先计算并保存下来。

时间复杂度是 $O(n^3)$ 

```java
class Solution {
    public int mctFromLeafValues(int[] arr) {
        int n = arr.length;
        // 以arr[i],...,arr[j]为叶子节点构成所有满足条件二叉树的集合，其属性是非叶子节点最小总和
        int[][] f = new int[n + 10][n + 10]; 
        int[][] m = new int[n + 10][n + 10]; // m[i][j]表示数组arr中区间[i,j]的最大值

        // 初始化
        for(int i = 0; i < f.length; i ++ )
            Arrays.fill(f[i], Integer.MAX_VALUE/4);

        for(int j = 0; j < n; j ++ ) {
            f[j][j] = 0;
            m[j][j] = arr[j];
            for(int i = j - 1; i >= 0; i --) {
                m[i][j] = Math.max(arr[i], m[i + 1][j]);
                for(int k = i; k < j; k ++ )
                    f[i][j] = Math.min(f[i][j], f[i][k] + f[k + 1][j] + m[i][k] * m[k+1][j]);
            }
        }
        return f[0][n - 1];
    }
}
```

#### （2）[LCP 34. 二叉树染色](https://leetcode.cn/problems/er-cha-shu-ran-se-UGC/) 



#### （3）[LCP 64. 二叉树灯饰](https://leetcode.cn/problems/U7WvvU/) 



#### （4）[968. 监控二叉树](https://leetcode.cn/problems/binary-tree-cameras/)

**问题**：

给定一个二叉树，我们在树的节点上安装摄像头。

节点上的每个摄影头都可以监视**其父对象、自身及其直接子对象。**

计算监控树的所有节点所需的最小摄像头数量。

例子

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/29/bst_cameras_01.png)

```
输入：[0,0,null,0,0]
输出：1
解释：如图所示，一台摄像头足以监控所有节点。
```

**数据范围**：

- 给定树的节点数的范围是 `[1, 1000]`。
- 每个节点的值都是 0。

**思路**：**最小支配集**模板题

染色最少得节点，支配所有相邻的节点。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {

    Map<TreeNode, int[]> f = new HashMap<>();

    public void dfs(TreeNode u, int idx) {
        if(idx != 0 && u.left == null && u.right == null) {
            int[] a = new int[3];
            a[1] = 0x3f3f3f3f;
            a[2] = 1;
            f.put(u, a);
            return ;
        }

        int t = 0x3f3f3f3f;
        int[] a = new int[3];
        a[2] = 1;
        boolean flag = true;

        if(u.left != null) { 
            dfs(u.left, idx + 1);
            int[] b = f.get(u.left);
            a[0] += Math.min(b[1], b[2]);
            a[1] += Math.min(b[1], b[2]);
            t = Math.min(t, b[2] - b[1]);
            if(b[2] < b[1]) flag = false;
            a[2] += Math.min(Math.min(b[0], b[1]), b[2]);
        }
        
        if(u.right != null) {
            dfs(u.right, idx + 1);
            int[] b = f.get(u.right);
            a[0] += Math.min(b[1], b[2]);
            a[1] += Math.min(b[1], b[2]);
            t = Math.min(t, b[2] - b[1]);
            if(b[2] < b[1]) flag = false;
            a[2] += Math.min(Math.min(b[0], b[1]), b[2]);
        }

        if(flag) a[1] += t;
        f.put(u, a);  
    }

    public int minCameraCover(TreeNode root) {
        dfs(root, 0);
        return Math.min(f.get(root)[1], f.get(root)[2]);
    }
}
```



#### （5）[834. 树中距离之和](https://leetcode.cn/problems/sum-of-distances-in-tree/)





#### （6）[2304. 网格中的最小路径代价](https://leetcode.cn/problems/minimum-path-cost-in-a-grid/)



#### （7）[2925. 在树上执行操作以后得到的最大分数](https://leetcode.cn/problems/maximum-score-after-applying-operations-on-a-tree/)



### 3、状态压缩DP

[状压 DP - 特别的排列 - 力扣（LeetCode）](https://leetcode.cn/problems/special-permutations/solution/zhuang-ya-dp-by-endlesscheng-4jkr/) 

#### （1）并行课程 II



#### （2）[1595. 连通两组点的最小成本](https://leetcode.cn/problems/minimum-cost-to-connect-two-groups-of-points/) 



给你两组点，其中第一组中有 `size1` 个点，第二组中有 `size2` 个点，且 `size1 >= size2` 。

任意两点间的连接成本 `cost` 由大小为 `size1 x size2` 矩阵给出，其中 `cost[i][j]` 是第一组中的点 `i` 和第二组中的点 `j` 的连接成本。**如果两个组中的每个点都与另一组中的一个或多个点连接，则称这两组点是连通的。**

换言之，第一组中的每个点必须至少与第二组中的一个点连接，且第二组中的每个点必须至少与第一组中的一个点连接。

返回连通两组点所需的最小成本。



#### （3）[1659. 最大化网格幸福感](https://leetcode.cn/problems/maximize-grid-happiness/) 



#### （4）[1681. 最小不兼容性](https://leetcode.cn/problems/minimum-incompatibility/) 



#### （5）[2050. 并行课程 III](https://leetcode.cn/problems/parallel-courses-iii/) 



### 4、数位DP

此类题型的特点是给定一个区间$[l, r]$，统计**「某一个数位」**出现的次数 或**「某种特殊整数」**出现的次数。

#### （1）[面试题 17.06. 2出现的次数](https://leetcode.cn/problems/number-of-2s-in-range-lcci/):star2:

**问题**：

编写一个方法，计算从 0 到 n (含 n) 中数字 2 出现的次数。

例子

```
输入: 25
输出: 9
解释: (2, 12, 20, 21, 22, 23, 24, 25)(注意 22 应该算作两次)
```

**数据范围**：

- `n <= 10^9`

**思路**：模拟题

```java
class Solution {

    public int[] breakup(int x) { // 抠出x中的每一位
        int[] digit = new int[30];
        int idx = 0;
        while(x != 0) {
            digit[idx ++] = x % 10;
            x /= 10;
        }
        return Arrays.copyOfRange(digit, 0, idx);
    }

    public int power10(int x) { // 10的x的密
        int res = 1;
        while((x --) != 0)
            res *= 10;
        return res;
    }

    public int get(int[] a, int l, int r) { // a[l]...a[r]的十进制表示, l指向高位
        int res = 0;
        for(int i = l; i >= r; i -- ) 
            res = res * 10 + a[i];
        return res;
    }

    public int signal(int x) { // 示性函数
        return x == 0 ? 1 : 0;
    }

    public int count(int a, int x) { // 计算1~a之间，数位2出现的次数
        if(a == 0) return 0;

        int[] vec = breakup(a); // 低位在前，高位在后
        int n = vec.length, res = 0;
        // 减signal是因为x=0且在最高位没有统计的意义
        for(int i = n - 1 - signal(x); i >= 0; i -- ) { // 从高位到低位统计
            if(i < n - 1) { // x在最高位存在第一种情况
                res += get(vec, n - 1, i + 1) * power10(i);
                if(x == 0) res -= power10(i);
            }
            if(vec[i] == x) res += get(vec, i - 1, 0) + 1;
            if(vec[i] > x) res += power10(i);
        }
        return res;
    }

    public int numberOf2sInRange(int n) {
        return count(n, 2);
    }
}
```

#### （2）[233. 数字 1 的个数](https://leetcode.cn/problems/number-of-digit-one/)

**问题**：

给定一个整数 `n`，计算所有小于等于 `n` 的非负整数中数字 `1` 出现的个数。

例子

```
输入：n = 13
输出：6
```

**数据范围**：

- `0 <= n <= 10^9`

**思路**：模板题

```java
class Solution {

    public int[] breakup(int x) { // 抠出x中的每一位
        int[] digit = new int[30];
        int idx = 0;
        while(x != 0) {
            digit[idx ++] = x % 10;
            x /= 10;
        }
        return Arrays.copyOfRange(digit, 0, idx);
    }

    public int power10(int x) { // 10的x的密
        int res = 1;
        while((x --) != 0)
            res *= 10;
        return res;
    }

    public int get(int[] a, int l, int r) { // a[l]...a[r]的十进制表示, l指向高位
        int res = 0;
        for(int i = l; i >= r; i -- ) 
            res = res * 10 + a[i];
        return res;
    }

    public int signal(int x) { // 示性函数
        return x == 0 ? 1 : 0;
    }

    public int count(int a, int x) { // 计算1~a之间，数位2出现的次数
        if(a == 0) return 0;

        int[] vec = breakup(a); // 低位在前，高位在后
        int n = vec.length, res = 0;
        // 减signal是因为x=0且在最高位没有统计的意义
        for(int i = n - 1 - signal(x); i >= 0; i -- ) { // 从高位到低位统计
            if(i < n - 1) { // x在最高位存在第一种情况
                res += get(vec, n - 1, i + 1) * power10(i);
                if(x == 0) res -= power10(i);
            }
            if(vec[i] == x) res += get(vec, i - 1, 0) + 1;
            if(vec[i] > x) res += power10(i);
        }
        return res;
    }

    public int countDigitOne(int n) {
        return count(n, 1);
    }
}
```

#### （3）[2376. 统计特殊整数](https://leetcode.cn/problems/count-special-integers/):star2:

**问题**

如果一个正整数每一个数位都是 **互不相同** 的，我们称它是 **特殊整数**（**没有重复数字的正整数**）。

给你一个 **正** 整数 `n` ，请你返回区间 `[1, n]` 之间特殊整数的数目。

例子

```
输入：n = 20
输出：19
解释：1 到 20 之间所有整数除了 11 以外都是特殊整数。所以总共有 19 个特殊整数。
```

**数据范围**：

- `1 <= n <= 2 * 10^9`

**集合论知识**：集合可以用二进制表示，二进制从第低到高第 $i$ 位 $1$ 表示 $i$ 在集合中，位 $0$ 表示 $i$ 不在集合中。例如：集合 $\{0,2,3\}$ 对应的二进制数为 $1101_{(2)}$。设集合对应的二进制数为 $x$，本模板需要用到的两个位运算操作：

- **判断元素 $d$ 是否在集合**中：$x >> d \ \& \ 1$ 可以取出$x$的第 $d$ 个比特，如果是 $1$ 表示 $d$ 在集合中。
- **把元素 $d$ 添加到集合**中：将 $x$ 更新为 $x | \ \ (1 << d)$。

**思路**：将 $n$ 转换成字符数组 $chs$，定义 $f(i, x, limit, pre)$ 表示构造第 $i$ 位及其之后数位的合法方案数，其余参数的含义为：

- $x$ 表示前面选过的数字集合（**二进制表示集合**），用来判断 第 $i$ 位要选的数字能否再 $x$ 集合中。
- $limit$ 表示当前第 $i$ 位是否受到了 $n$ 的约束（注意要构造的数字不能超过 $n$）。若为真，则第 $i$ 位填入的数字至多为 $chs[i]$，否则可以至多是 $9$。如果在收到约束的情况下填了 $chs[i]$，那么后续填入的数字仍然会受到 $n$ 的约束。（例如：$n=123$，那么 $i=0$ 填的是1的话，$i=1$的这一位至多填 $2$。）
- $pre$ 表示 $i$ 前面的数位是否填了数字。若为假，则当前为可以跳过（不填数字），或者要填入的数字至少为 $1$；若为真，则要填入的数字可以从 $0$ 开始。（例如 $n=123$，在$i=0$时跳过的话，相当于后面要构造的是一个 $99$ 以内的数字；如果$i=1$不跳过，那么相当于构造一个 $10$~$99$的两位数；如果 $i=1$跳过，相当于构造一个 $9$ 以内的数字。）

**实现细节**：

递归入口：$f(0,0,true,false)$，表示

- 从$chs[0]$开始枚举；
- 一开始集合中没有数字；
- 一开始要受到 $n$ 的约束（否则就可以随意填了）
- 一开始没有填数字

递归中：

- 如果 $pre$ 为假，说明前面没有填数字，那么当前 $i$ 位也可以不用填数字。一旦从这里递归下去，$limit$就可以置为 $false$了，后面就不受到 $n$ 的约束了。也就是说，最高位不填数字，后面无论怎么填都比 $n$ 小。
- 如果 $pre$ 为真，那么当前必须填入一个数字。依据 $pre$ 和 $limit$ 来决定填入数字的范围。

递归终点：当 $i$ 等于 $s$ 长度时，如果 $pre$ 为真，则表示找到一个合法的数字，返回1；否则，返回0.

```java
class Solution {
    char[] chs;
    // 返回从第i个位置开始填数字, i前面填的数字的集合是a, 能构造出的特殊整数的数目
    // limit 为true表示前面填的数字都是n对应位置上，那么在i位置填数字会受到n的约束,至多填s[i];否则至多填9
    // pre为ture表示前面填过数字, i位可以从0开始; 否则, 表示前面没有填过数字, 可以跳过或者从1开始填数字
    public int f(int i, int x, boolean limit, boolean pre) {
        if(i == chs.length) return pre ? 1 : 0; // 找到一个特殊整数

        int res = 0;
        if(!pre) res = f(i + 1, x, false, false); // i前面没有填数字, i位也选择不填, 后面不受n约束
        int up = (limit ? chs[i] - '0' : 9);
        for(int j = 1 - (pre ? 1 : 0); j <= up; j ++ ) // 前面填过数字, i位可以从0开始; 否则，从0开始
            if((x >> j & 1) == 0) 
                res += f(i + 1, x | (1 << j), limit && j == up, true);
        return res;  
    }

    public int countSpecialNumbers(int n) {
        chs = ("" + n).toCharArray();
        return f(0, 0, true, false);
    }
}
```

**优化**

**优化点1**： **$pre$ 这个参数可以去掉吗？**在本题目中是可以去掉的。由于 $x$ 存储之前已经选择的数字，可以用过判断 $x$ 是否为 0来判断前面是否填了数字，所以 $pre$ **可以省略**。

**优化点2**：**可以使用记忆化搜索进行优化？**在本题中只需要记忆 $(i,x)$这两个状态，用 $memo[i][x]$记忆此状态对应的结果。如果递归到 第$i$ 个位置，前面选择的数字集合是 $x$。我们通过查看 $memo[i][x]$，发现这个状态之前计算过了，就可以直接返回结果。

本题可以记忆化搜索的原因是：如果当前递归到 第$i$ 个位置，前面选择的数字集合是 $x$。我们**无需关注前面的数字是如何组合或者排列**的。如果之前已经计算出此状态的方案数，那么就无需再重复计算。例如 $n=234$，第一位（最高位）填1，那么继续递归，后面就可以随便填。所以状态$memo[1][2]$就表示，第一位填1（对应$x$为2），从第二位往后随便填的方案数。

我们只记录不受 $limit$ 和 $pre$ 约束的状态。即有两类状态无需记忆（受到 $limit=true$ 和 $pre=false$影响的状态无需记忆），以 $n=234$ 为例，第一位填 2，第二位填 3，后面无论怎么递归，都不会再递归到第一位填2，第二位填3的情况，所以不需要记录。如果第一位不填，第二位不填，后面无论怎么递归也不会再递归到这种情况，所以也不需要记录。

```java
class Solution {
    char[] chs;
    int[][] memo;
    // 返回从第i个位置开始填数字, i前面填的数字的集合是a, 能构造出的特殊整数的数目
    // limit 为true表示前面填的数字都是n对应位置上，那么在i位置填数字会受到n的约束,至多填s[i];否则至多填9
    // pre为ture表示前面填过数字, i位可以从0开始; 否则, 表示前面没有填过数字, 可以跳过或者从1开始填数字
    public int f(int i, int x, boolean limit, boolean pre) {
        if(i == chs.length) return pre ? 1 : 0; // 找到一个特殊整数
        if(!limit && pre && memo[i][x] != -1) return memo[i][x]; // 已经计算过此状态

        int res = 0;
        if(!pre) res = f(i + 1, x, false, false); // i前面没有填数字, i位也选择不填, 后面不受n约束
        int up = (limit ? chs[i] - '0' : 9);
        for(int j = 1 - (pre ? 1 : 0); j <= up; j ++ ) // 前面填过数字, i位可以从0开始; 否则，从0开始
            if((x >> j & 1) == 0) 
                res += f(i + 1, x | (1 << j), limit && j == up, true);
        if(!limit && pre) memo[i][x] = res; // 在备忘录中记录
        return res;  
    }

    public int countSpecialNumbers(int n) {
        chs = ("" + n).toCharArray();
        int m = chs.length;
        memo = new int[m][1 << 10];
        for(int i = 0; i < m; i ++ ) Arrays.fill(memo[i], -1); // 初始化备忘录
        return f(0, 0, true, false);
    }
}
```

**能不能只记忆位置$i$？**这是不行的。想一想，我们为什么要用记忆化？如果递归到同一个状态时，计算出的结果是一样的，那么第二次递归到同一个状态，就可以直接返回第一次计算的结果了。**通过保存第一次计算的结果，来优化时间复杂度**。

由于前面选的数字会影响后面选的数字，**两次递归到相同的 $i$，如果前面选的数字不一样，计算出的结果就可能是不一样的**。如果只记忆化 $i$，就可能会算出错误的结果。也可以这样理解：记忆化搜索要求递归函数无副作用（除了修改 memo 数组），从而保证递归到同一个状态时，计算出的结果是一样的。

**时间复杂度**

时间复杂度： $O\left(m D 2^D\right)$, 其中 $m$ 为 $s$ 的长度, 即 $O(\log n) ; D=10$ 。由于每个 状态只会计算一次, 因此动态规划的时间复杂度 $=$ 状态个数 $\times$ 单个状态的计算 时间。本题状态个数为 $O\left(m 2^D\right)$, 单个状态的计算时间为 $O(D)$, 因此时间复杂

#### （4）[902. 最大为 N 的数字组合](https://leetcode.cn/problems/numbers-at-most-n-given-digit-set/)

**问题**：

给定一个按 **非递减顺序** 排列的数字数组 `digits` 。你可以用任意次数 `digits[i]` 来写的数字。例如，如果 `digits = ['1','3','5']`，我们可以写数字，如 `'13'`, `'551'`, 和 `'1351315'`。

返回 *可以生成的小于或等于给定整数 `n` 的正整数的个数* 。

例子

```
输入：digits = ["1","3","5","7"], n = 100
输出：20
解释：
可写出的 20 个数字是：
1, 3, 5, 7, 11, 13, 15, 17, 31, 33, 35, 37, 51, 53, 55, 57, 71, 73, 75, 77.
```

**数据范围**

- `1 <= digits.length <= 9`
- `digits[i].length == 1`
- `digits[i]` 是从 `'1'` 到 `'9'` 的数
- `digits` 中的所有值都 **不同** 
- `digits` 按 **非递减顺序** 排列
- `1 <= n <= 10^9`

**思路**

本题与「2376. 统计特殊整数」非常相似，「2376. 统计特殊整数」是在给定的**数位集合** $\{0,1,...,9\}$中构造小于 $n$ 的整数，**约束条件是构造的整数各个位不相同**，求可以构造的满足要求的整数个数。设构造的整数为 $x$，$1<=x<=n$。

本题是在**给定的数位集合** $digits=\{a_1,a_2,...,a_n  \}$ 中构造小于 $n$ 的整数，**约束是数位集合**，**构造的整数各个位可以相同**，求可以构造的满足要求的整数的个数。设构造的整数为 $x$，$1<=x<=n$。

版本一：

```java
class Solution {

    char[] chs; // chs表示整数n的字符数组形式
    int[][] memo; // memo[i][j]记忆化存储之前的状态(当前枚举的位置是i, 之前用到的数位集合是j)
    // 返回从第i个位置开始填数字, i前面填的数字的集合是a, 能构造出的特殊整数的数目
    // limit 为true表示前面填的数字都是n对应位置上，那么在i位置填数字会受到n的约束,至多填s[i];否则至多填9
    // pre为ture表示前面填过数字, i位可以从0开始; 否则, 表示前面没有填过数字, 可以跳过或者从1开始填数字
  	// a是给定的数位集合
    public int f(int i, int x, boolean limit, boolean pre, int a) {
        if(i == chs.length) return pre ? 1 : 0; // 找到一个特殊整数
        if(!limit && pre && memo[i][x] != -1) return memo[i][x]; // 已经计算过此状态

        int res = 0;
        if(!pre) res = f(i + 1, x, false, false, a); // i前面没有填数字, i位也选择不填, 后面不受n约束
        int up = (limit ? chs[i] - '0' : 9);
        for(int j = 1 - (pre ? 1 : 0); j <= up; j ++ ) // 前面填过数字, i位可以从0开始; 否则，从0开始
            if((a >> j & 1) == 1) // 判断 j 是否在给定的数位集合中
                res += f(i + 1, x | (1 << j), limit && j == up, true, a);
        if(!limit && pre) memo[i][x] = res; // 在备忘录中记录
        return res;
    }

    public int atMostNGivenDigitSet(String[] digits, int n) {
        chs = ("" + n).toCharArray();
        
        int a = 0; // a是给定的数位集合, 用二进制表示集合
        for(int i = 0; i < digits.length; i ++)
            a = a | (1 << (digits[i].charAt(0) - '0'));

        int m = chs.length;
        memo = new int[m][1 << 10];
        for(int i = 0; i < m; i ++ ) Arrays.fill(memo[i], -1); // 初始化备忘录

        return f(0, 0, true, false, a);
    }
}
```

版本二：

```java
class Solution {

    char[] chs; // chs表示整数n的字符数组形式
    int[][] memo; // memo[i][j]记忆化存储(当前枚举的位置是i, 之前用到的数位集合是j)
    // 返回从第i个位置开始填数字, i前面填的数字的集合是a, 能构造出的特殊整数的数目
    // limit 为true表示前面填的数字都是n对应位置上，那么在i位置填数字会受到n的约束,至多填s[i];否则至多填9
    // pre为ture表示前面填过数字, i位可以从0开始; 否则, 表示前面没有填过数字, 可以跳过或者从1开始填数字
    public int f(int i, int x, boolean limit, boolean pre, int[] a) {
        if(i == chs.length) return pre ? 1 : 0; // 找到一个特殊整数
        if(!limit && pre && memo[i][x] != -1) return memo[i][x]; // 已经计算过此状态

        int res = 0;
        if(!pre) res = f(i + 1, x, false, false, a); // i前面没有填数字, i位也选择不填, 后面不受n约束
        int up = (limit ? chs[i] - '0' : a[a.length - 1]); // 最大能使用的数字是a[a.length - 1]
        for(int j = 0; j < a.length && a[j] <= up; j ++ ) // 从给定的数位集合中选取数字, digits[i]是从 1 到 9 的数
                res += f(i + 1, x | (1 << a[j]), limit && a[j] == up, true, a);
        if(!limit && pre) memo[i][x] = res; // 在备忘录中记录
        return res;  
    }

    public int atMostNGivenDigitSet(String[] digits, int n) {
        chs = ("" + n).toCharArray();
        
        int a[] = new int[digits.length]; // 数组a表示给定的数位集合
        for(int i = 0; i < digits.length; i ++)
            a[i] =  digits[i].charAt(0) - '0';
        Arrays.sort(a); // 将所有数位从小到大排序

        int m = chs.length;
        memo = new int[m][1 << 10];
        for(int i = 0; i < m; i ++ ) Arrays.fill(memo[i], -1); // 初始化备忘录

        return f(0, 0, true, false, a);
    }
}
```

#### （5）[600. 不含连续1的非负整数](https://leetcode.cn/problems/non-negative-integers-without-consecutive-ones/)

**问题**：

给定一个正整数 `n` ，请你统计在 `[0, n]` 范围的非负整数中，有多少个整数的二进制表示中不存在 **连续的 1** 。

例子

```
输入: n = 5
输出: 5
解释: 
下面列出范围在 [0, 5] 的非负整数与其对应的二进制表示：
0 : 0
1 : 1
2 : 10
3 : 11
4 : 100
5 : 101
其中，只有整数 3 违反规则（有两个连续的 1 ），其他 5 个满足规则。
```

**数据范围**：

- `1 <= n <= 10^9`

**思路**：

本题是在给定的**数位集合** $\{0, 1\}$ 中构造小于 $n$ 的二进制整数，**约束条件是构造的整数的二进制表示不存在连续的1**，求可以构造的满足要求的整数的个数。设构造的整数为 $x$，$0<=x<=n$。

```java
class Solution {

    int[] chs = new int[32]; // 从chs[0]到chs[32]分别表示n的二进制表示的低为到高位
    int idx = 0;
    int[][] memo = new int[32][2]; // memo[i][j]记忆化存储之前的状态(当前位置是i,填充的数是j)

    // limit为true, 表示第i位受到n的约束，至多可以到达chs[i]; 否则，至多可以到达9
    // pre为true，表示第i位之前有数字，第i位从0开始枚举；否则，可以跳过或者从1开始枚举
  	// last是位置i-1所选择的数
    public int f(int i, int last, boolean limit, boolean pre) {
        if(i == -1) return pre ? 1 : 0;
        if(!limit && pre && memo[i][last] != -1) return memo[i][last]; 

        int res = 0;
        if(!pre) res = f(i - 1, 0, false, false); // 跳过第i位，相当于在位置i填充数0
        int up = limit ? chs[i] : 1;
        for(int j = (pre ? 0 : 1); j <= up; j ++ ) {
            if(last == 1 && j == last) continue; // 不能存在连续的1
            res += f(i - 1, j, limit && j == up, true);
        }
        if(!limit && pre) memo[i][last] = res;
        return res;
    }

    public int findIntegers(int n) {
        while(n != 0) {
            chs[idx ++] = (n & 1);
            n >>= 1;
        }
        for(int i = 0; i < 32; i ++ ) Arrays.fill(memo[i], -1);
        return f(idx - 1, 0, true, false) + 1; // 0是特判
    }
}
```

#### （6）[1012. 至少有 1 位重复的数字](https://leetcode.cn/problems/numbers-with-repeated-digits/)

**问题**：

给定正整数 `n`，返回在 `[1, n]` 范围内具有 **至少 1 位** 重复数字的正整数的个数。

例子

```
输入：n = 20
输出：1
解释：具有至少 1 位重复数字的正数（<= 20）只有 11 。
```

**数据范围**：

- `1 <= n <= 10^9`

**思路**：

本题是在给定的**数位集合** $\{0, 1,...,9\}$ 中构造小于 $n$ 的正整数，**约束条件是构造的整数至少存在1位重复数字**，求可以构造的满足要求的整数的个数。设构造的整数为 $x$，$1<=x<=n$。

直接求至少存在1位重复数字的整数比较复杂。**正难则反**，我能可以**求出所有不存在重复数字的整数**。

```java
class Solution {

    char[] chs; // chs表示整数n的字符数组形式
    int[][] memo = new int[10][1 << 10];

    // limit为true, 表示第i位受到n的约束，至多可以到达chs[i]; 否则，至多可以到达9
    // pre为true，表示第i位之前有数字，第i位从0开始枚举；否则，可以跳过或者从1开始枚举
    public int f(int i, int x, boolean limit, boolean pre) {
        if(i == chs.length) return pre ? 1 : 0;
        if(!limit && pre && memo[i][x] != -1) return memo[i][x]; 

        int res = 0;
        if(!pre) res = f(i + 1, x, false, false); // 跳过第i位
        int up = limit ? chs[i] - '0' : 9;
        for(int j = (pre ? 0 : 1); j <= up; j ++ ) 
            if((x >> j & 1) == 0)
                res += f(i + 1, x | (1 << j), limit && j == up, true);
        if(!limit && pre) memo[i][x] = res;
        return res;
    }

    public int numDupDigitsAtMostN(int n) {
        chs = ("" + n).toCharArray();
        for(int i = 0; i < 10; i ++) Arrays.fill(memo[i], -1);

        return n - f(0, 0, true, false);
    }
}
```

#### （7）[2719. 统计整数数目](https://leetcode.cn/problems/count-of-integers/)

**问题**：

给你两个数字字符串 `num1` 和 `num2` ，以及两个整数 `max_sum` 和 `min_sum` 。如果一个整数 `x` 满足以下条件，我们称它是一个**好整数**：

- `num1 <= x <= num2`
- `min_sum <= digit_sum(x) <= max_sum`.

请你返回好整数的数目。答案可能很大，请返回答案对 `10^9 + 7` 取余后的结果。

注意，`digit_sum(x)` 表示 `x` 各位数字之和。

例子

```
输入：num1 = "1", num2 = "12", min_num = 1, max_num = 8
输出：11
解释：总共有 11 个整数的数位和在 1 到 8 之间，分别是 1,2,3,4,5,6,7,8,10,11 和 12 。所以我们返回 11 。
```

**数据范围**：

- `1 <= num1 <= num2 <= 10^22`
- `1 <= min_sum <= max_sum <= 400`

**思路**：

本题是在给定的**数位集合** $\{0, 1,...,9\}$ 中构造小于 $n$ 的正整数，**约束条件是数位之和满足：$minSum <= digitSum(x) <= maxSum$**，求可以构造的满足要求的整数的个数。设构造的整数为 $x$，$1<=x<=n$。设$f(n)$可以求出 $[1,n]$之间的满足题目数位之和的整数个数。

依据前缀和的思想，$f(num2)-f(nums1)$可以求出在$[nums1,nums2]$之间的满足题目数位之和的整数个数，即好整数的个数。

```java
class Solution {

    final int MOD = (int)1e9 + 7;
    int[][] memo = new int[25][410]; // memo[i][j]记忆化存储之前的状态(当前位置是i,i之前所有数位之和为j)
    // 返回满足要求的方案数
    // limit为true,表示第i位填的数字会受到n的限制，最多填chs[i]; 否则，至多填9
    // pre为true, 表示第i之前填过数字, 第i位可以从0开始枚举; 否则，跳过或者从1开始枚举
  	// sum表示i之前所有数位之和
    // chs是n对应的字符数组，从chs[0]对应n的高位
    public int f(int i, int sum, boolean limit, boolean pre, char[] chs, int min_sum, int max_sum) {
        if(i == chs.length) return pre && sum >= min_sum ? 1 : 0; // sum >= min_sum判断是否满足数位之和要求
        if(!limit && pre && memo[i][sum] != -1) return memo[i][sum];

        int res = 0;
        if(!pre) res = f(i + 1, sum, false, false, chs, min_sum, max_sum);
        int up = limit ? chs[i] - '0' : 9;
        for(int j = (pre ? 0 : 1); j <= up; j ++ )
            if(sum + j <= max_sum) // sum >= min_sum判断是否满足数位之和要求
                res = (int)((res + (long)f(i + 1, sum + j, limit && j == up, true, chs, min_sum, max_sum)) % MOD);
            else {
                if(!limit && pre) memo[i][sum] = res;
                return res;
            }
        
        if(!limit && pre) memo[i][sum] = res;
        return res;
    }

    public int count(String num1, String num2, int min_sum, int max_sum) {
        char[] a = num1.toCharArray(), b = num2.toCharArray();
        
        for(int i = 0; i < 25; i ++) Arrays.fill(memo[i], -1);
        int x = f(0, 0, true, false, b, min_sum, max_sum);
        
        for(int i = 0; i < 25; i ++) Arrays.fill(memo[i], -1); // 需要重新初始化记忆化数组
        int y = f(0, 0, true, false, a, min_sum, max_sum);
        
        int res = (int)((long)x + MOD - y) % MOD; // 有可能x比y小，所以加上一个MOD

        int sum = 0;
        for(int i = 0; i < a.length; i ++) // nums1进行特判
            sum += (a[i] - '0');
        if(sum >= min_sum && sum <= max_sum) res = ((res + 1) % MOD);
        return res;  
    }
}
```

#### （8）[1397. 找到所有好字符串](https://leetcode.cn/problems/find-all-good-strings/)（结合KMP）:star2:

**问题**：

给你两个长度为 `n` 的字符串 `s1` 和 `s2` ，以及一个字符串 `evil` 。请你返回 **好字符串** 的数目。

**好字符串** 的定义为：**它的长度为 `n` ，字典序大于等于 `s1` ，字典序小于等于 `s2` ，且不包含 `evil` 为子字符串**。

由于答案可能很大，请你返回答案对 10^9 + 7 取余的结果。

例子

```
输入：n = 2, s1 = "aa", s2 = "da", evil = "b"
输出：51 
解释：总共有 25 个以 'a' 开头的好字符串："aa"，"ac"，"ad"，...，"az"。还有 25 个以 'c' 开头的好字符串："ca"，"cc"，"cd"，...，"cz"。最后，还有一个以 'd' 开头的好字符串："da"。
```

**数据范围**：

- `s1.length == n`
- `s2.length == n`
- `s1 <= s2`
- `1 <= n <= 500`
- `1 <= evil.length <= 50`
- 所有字符串都只包含小写英文字母。

**思路**：

所有的小写字母可以用 $[0,...,25]$之间的26个整数进行编码。设字符串为 $s, (s.length()=n)$，其唯一用一个**整数数组** $[a_1,a_2,...,a_n]$表示，其中，$ a_i∈[0,...,25]$ 且 $a_i = s.charAt(i)-'0'$。

本题是在给定的**数位集合** $\{0, 1,...,25\}$ 中构造小于 $n$ 的整数数组，**约束条件是：所构造的整数数组必须是好整数数组**，求可以构造的满足要求的整数数组的个数。

**好字符串（好整数数组）** 的定义为：**它的长度为 `n` ，字典序大于等于 `s1` ，字典序小于等于 `s2` ，且不包含 `evil` 为子字符串**。

我们设字符串 $s1$ 对应的整数数组是 $a$，字符串 $s2$ 对应的整数数组是 $b$，字符串 $evil$ 对应的整数数组是 $c$。

那么好整数数组 $x$ 定义可以转换为：满足 $a <= x <= b$，且 $c$ 不能是 $x$ 的子数组。

设构造的整数数组为 $x$，$1<=x<=n$。设$f(n)$可以求出 $[1,n]$之间的满足题目整数个数。

依据前缀和的思想，$f(b)-f(a)$可以求出在$[a,b]$之间的满足的整数个数，即好字符串的个数。最后，还需要对 $a$ 进行额外的特判。

判断一个字符串是否另一个字符串的子串，可以**采用 KMP算法**，其复杂度是 $O(m + n)$，$m$ 是字符串的长度， $n$ 是模板串的长度。

为了降低总的时间复杂度，我们还需要使用记忆化搜索（空间换时间）来优化。

```java
class Solution {
    final int MOD = (int)(1e9 + 7);
    int[][] memo = new int[510][55]; // memo[i][j]记忆化存储转态(i是当前填充的位置, j是位置i与最多模板串位置j匹配)
    int[] ne = new int[510]; // 设j = ne[i]。含义：p中以i为终点的后缀和从1开始的前缀的最大匹配长度是j

    public void next(int[] p, int n) { // 依据模板串p计算next数组, 本质上是进行一次kmp匹配
        for(int i = 2, j = 0; i <= n; i ++) { // 求 next 数组
            while(j != 0 && p[i] != p[j + 1]) j = ne[j]; // p没有从起点0开始, 且p[i]和p[j+1]不匹配,回退
            if(p[i] == p[j + 1]) j ++ ; // 尝试匹配
            ne[i] = j; // 更新ne[i]
        }
    }

    public boolean kmp(int[] s, int[] p, int n, int m) { // 字符串s和模板串p匹配的过程
        for(int i = 1, j = 0; i <= m; i ++) {
            while(j != 0 && s[i] != p[j + 1]) j = ne[j]; // p没有从起点0开始, 且p[i]和p[j+1]不匹配,回退
            if(s[i] == p[j + 1]) j ++; // 尝试匹配
            if(j == n) return true; // 判断是否匹配成功
        }
        return false;
    }

    // 返回从第i个位置开始填数字, 字符串s的i-1位置与模板串匹配的位置是last, 能构造出的整数的数目
    public int f(int i, int last, boolean limit, int[] s, int[] p) {
        if(last == p.length - 1) return 0; // 字符串s与p匹配成功
        if(i == s.length) return 1; // 找到一个好字符串
        if(!limit && memo[i][last] != -1) return memo[i][last]; // 已经计算过该状态，直接返回

        int res = 0;
        int up = limit ? s[i] : 25; // 求位置i可以填充的数的上限
        for(int j = 0; j <= up; j ++) { // 枚举所有可以填充的数
            // 用位置i填充的数j 与 p[k + 1] 进行kmp匹配过程
            int k = last;
            while(k != 0 && j != p[k + 1]) k = ne[k]; // p没有从起点0开始, 且j和p[k+1]不匹配,回退
            if(j == p[k + 1]) k ++; // 尝试匹配
            res = (int)(res + (long)f(i + 1, k, limit && j == up, s, p)) % MOD; // 匹配i+1与k+1
        }
        if(!limit) memo[i][last] = res; // 存储状态
        return res;
    }
    
    public int findGoodStrings(int n, String s1, String s2, String evil) {
        // kmp匹配要求所有字符串都从下标1开始
        int[] a = new int[n + 1], b = new int[n + 1], c = new int[evil.length() + 1];
        for(int i = 0; i < Math.max(n, evil.length()); i ++) {
            if(i < n) a[i + 1] = s1.charAt(i) - 'a';
            if(i < n) b[i + 1] = s2.charAt(i) - 'a';
            if(i < evil.length()) c[i + 1] = evil.charAt(i) - 'a';
        }
        next(c, c.length - 1); // 求next数组

        for(int i = 0; i < 510; i ++) Arrays.fill(memo[i], -1); // 初始化备忘录
        int x = f(1, 0, true, a, c);

        for(int i = 0; i < 510; i ++) Arrays.fill(memo[i], -1);
        int y = f(1, 0, true, b, c);
        // a需要特判
        return (int)((long)y + MOD - x + (kmp(a, c, c.length - 1, a.length - 1) ? 0 : 1)) % MOD;
    }
}
```

#### （9）[6957. 统计范围内的步进数字数目](https://leetcode.cn/problems/count-stepping-numbers-in-range/) 

**问题**

给你两个正整数 `low` 和 `high` ，都用字符串表示，请你统计闭区间 `[low, high]` 内的 **步进数字** 数目。

如果一个整数相邻数位之间差的绝对值都 **恰好** 是 `1` ，那么这个数字被称为 **步进数字** 。

请你返回一个整数，表示闭区间 `[low, high]` 之间步进数字的数目。

由于答案可能很大，请你将它对 `10^9 + 7` **取余** 后返回。

**注意：**步进数字不能有前导 0 。

例子

```
输入：low = "1", high = "11"
输出：10
解释：区间 [1,11] 内的步进数字为 1 ，2 ，3 ，4 ，5 ，6 ，7 ，8 ，9 和 10 。总共有 10 个步进数字。所以输出为 10 。
```

**数据范围**

- `1 <= int(low) <= int(high) < 10^100`
- `1 <= low.length, high.length <= 100`
- `low` 和 `high` 只包含数字。
- `low` 和 `high` 都不含前导 0 。

**思路**

此题与「2719. 统计整数数目」非常类似，都是统计给定某个区间的「特殊整数」的数目。

设给定的区间为 $[l,r]$，这类题目都通用思路是先求出$[1,l]$之间的「特殊整数」的数目$f(l)$，再求出$[1,r]$之间的特殊整数的数目$f(r)$，最后特判左边界$l$是否是特殊整数。依据前缀和的思想，最终$[l,r]$之间的特殊整数的数目为 $f(r)-f(l)+isSpecial(l)$。

求$[1,l]$之间的特殊整数的数目，属于「数位统计DP」的模板题目，但是在此题中需要注意进入递归的判定条件

```java
for(int j = (pre ? 0 : 1); j <= up; j ++ ) // 前面填过数字, i位可以从0开始; 否则，从0开始
   if(!pre || Math.abs(x - j) == 1)     
        res = (int)((long)res + f(i + 1, j, limit && j == up, true)) % MOD;
```

$!pre || Math.abs(x - j) == 1$ 表示当当前枚举的第 $i$ 为之前没有填数组即$!pre$或在第$i$ 之前填了数字，当前第$i$ 位填的数字与前一位数字的绝对值相差一。也就是说，如果第$i$ 位之前没有填过任何数字，第$i$ 填写的数字不受第$i-1$位数字的约束，但是不能超过上限$up$。如果第$i$位之前填过数字，第$i$ 位填写的数字就要受到第$i-1$位的约束，即整数相邻数位之间差的绝对值都 **恰好** 是 `1` 。

值得注意的是，此类型的题目往往都需要使用「记忆化搜索」来优化时间复杂度。同时，由于计算方案的个数比较多，需要与$1\times10^{9}+7$ 取模。

```java
class Solution {
    
    char[] chs; // chs[0]表示高位
    final int MOD = (int)(1e9+7);
    int[][] memo;
    // 返回从第i个位置开始填数字, i前面填的数字的集合是a, 能构造出的特殊整数的数目
    // limit 为true表示前面填的数字都是n对应位置上，那么在i位置填数字会受到n的约束,至多填s[i];否则至多填9
    // pre为ture表示前面填过数字, i位可以从0开始; 否则, 表示前面没有填过数字, 可以跳过或者从1开始填数字
    public int f(int i, int x, boolean limit, boolean pre) {
        if(i == chs.length) return pre ? 1 : 0; // 找到一个特殊整数
        if(!limit && pre && memo[i][x] != -1) return memo[i][x]; // 已经计算过此状态

        int res = 0;
        if(!pre) res = f(i + 1, 0, false, false) % MOD; // i前面没有填数字, i位也选择不填, 后面不受n约束
        int up = (limit ? chs[i] - '0' : 9);
        for(int j = (pre ? 0 : 1); j <= up; j ++ ) // 前面填过数字, i位可以从0开始; 否则，从0开始
           if(!pre || Math.abs(x - j) == 1)     
                res = (int)((long)res + f(i + 1, j, limit && j == up, true)) % MOD;
        if(!limit && pre) memo[i][x] = res; // 在备忘录中记录
        return res;  
    }
    
    public int countSteppingNumbers(String low, String high) {
        memo = new int[low.length()][10];
        chs = low.toCharArray();
        for(int i = 0; i < low.length(); i ++ ) Arrays.fill(memo[i], -1); // 初始化备忘录
        int a = f(0, 0, true, false);
        
        memo = new int[high.length()][10];
        chs = high.toCharArray();
        for(int i = 0; i < high.length(); i ++ ) Arrays.fill(memo[i], -1); // 初始化备忘录
        int b = f(0, 0, true, false);
        
        boolean flag = true;
        for(int i = 1; i < low.length(); i ++ )
            if(Math.abs(low.charAt(i) - low.charAt(i - 1)) != 1) {
                flag = false;
                break;
            }    
        
        int res = (int)((long)b + MOD - a) % MOD;
        return  (res + (flag ? 1 : 0)) % MOD;
    }
}
```



### 5、状态机DP

#### （1）[LCP 19. 秋叶收藏集](https://leetcode.cn/problems/UlBDOe/)

**问题**：

小扣出去秋游，途中收集了一些红叶和黄叶，他利用这些叶子初步整理了一份秋叶收藏集 `leaves`，字符串 `leaves` 仅包含小写字符 `r` 和 `y`， 其中字符 `r` 表示一片红叶，字符 `y` 表示一片黄叶。

出于美观整齐的考虑，小扣想要将收藏集中树叶的排列调整成**「红、黄、红」**三部分。

每部分树叶数量可以不相等，但均需大于等于 1。**每次调整操作，小扣可以将一片红叶替换成黄叶或者将一片黄叶替换成红叶**。

请问小扣**最少**需要多少次调整操作才能将秋叶收藏集调整完毕。

例子：

```
示例 1：

输入：leaves = "rrryyyrryyyrr"

输出：2

解释：调整两次，将中间的两片红叶替换成黄叶，得到 "rrryyyyyyyyrr"
```

**数据范围**：

- `3 <= leaves.length <= 10^5`
- `leaves` 中只包含字符 `'r'` 和字符 `'y'`

**思路**：

状态定义：

一片叶子有三种状态：0表示黄色前面的红色，1表示红色中间的黄色，2表示黄色后面的红色

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-07-11%2013.06.08.png" alt="截屏2023-07-11 13.06.08" style="zoom:47%;" />

集合表示：$f[i][j]$ 表示考虑前 $i$ 片叶子，第$i$ 片叶子的状态是 $j$ 的所有方案的集合。属性是最小的操作次数。

集合划分：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-07-11%2013.18.05.png" alt="截屏2023-07-11 13.18.05" style="zoom:37%;" />



状态初始化：

 $f[1][1] = 0x3f3f3f3f, f[1][2] = 0x3f3f3f3f$

$f[2][2] = 0x3f3f3f3f$

初始化：$f[1][0] <- f[0][0]$, 第一片叶子只有状态0，$ f[2][0], f[2][1]$  第二片叶子 只有状态0和1

```java
class Solution {
    public int minimumOperations(String leaves) {
        int n = leaves.length();
        int[][] f = new int[n + 10][3];

        f[1][1] = 0x3f3f3f3f; f[1][2] = 0x3f3f3f3f;
        f[2][2] = 0x3f3f3f3f;

        for(int i = 1; i <= n; i ++) {
            f[i][0] = f[i - 1][0] + (leaves.charAt(i - 1) == 'y' ? 1 : 0);
            if(i > 1)
                f[i][1] = Math.min(f[i - 1][0], f[i - 1][1]) + (leaves.charAt(i - 1) == 'r' ? 1 : 0);
            if(i > 2)
                f[i][2] = Math.min(f[i - 1][1], f[i - 1][2]) + (leaves.charAt(i - 1) == 'y' ? 1 : 0);
        }
        return f[n][2];
    }
}
// 状态表示： f[i][j] 考虑前i片树叶，且第i片树叶是状态j的最小操作次数的方案的集合
// j = 0 和 j = 2分别表示黄色前面和后面的红色，j=1表示中间的黄色

// f[1][1] = 0x3f3f3f3f, f[1][2] = 0x3f3f3f3f
// f[2][2] = 0x3f3f3f3f
// 初始化：f[1][0] <- f[0][0], 第一片叶子只有状态0
// f[2][0], f[2][1]  第二片叶子 只有状态0和1

// 集合划分：f[i][0] = f[i - 1][0] + is_Yelloy(i)
// f[i][1] = min(f[i - 1][0], f[i - 1][1]) + is_Red(i);
// f[i][2] = min(f[i - 1][1], f[i - 1][2]) + isYelloy(i);
```

#### （2）[1911. 最大子序列交替和](https://leetcode.cn/problems/maximum-alternating-subsequence-sum/)（股票买卖）

**问题**：

一个下标从 **0** 开始的数组的 **交替和** 定义为 **偶数** 下标处元素之 **和** 减去 **奇数** 下标处元素之 **和** 。

- 比方说，数组 `[4,2,5,3]` 的交替和为 `(4 + 5) - (2 + 3) = 4` 。

给你一个数组 `nums` ，请你返回 `nums` 中任意子序列的 **最大交替和** （子序列的下标 **重新** 从 0 开始编号）。

一个数组的 **子序列** 是从原数组中删除一些元素后（也可能一个也不删除）剩余元素不改变顺序组成的数组。

比方说，`[2,7,4]` 是 `[4,2,3,7,2,1,4]` 的一个子序列（加粗元素），但是 `[2,4,2]` 不是。

例子：

```
输入：nums = [4,2,5,3]
输出：7
解释：最优子序列为 [4,2,5] ，交替和为 (4 + 5) - 2 = 7 。
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^5`

**思路**：此题是买卖股票的最佳时机II的 变体

**贪心**

我们可以发 $nums$ 数组中任意子序列的**最大**交替和一定一个**奇数序列**。

证明：在其后面添加任意的一个正数，使得序列变成偶数序列（长度为偶数），该序列的交替和一定会变小。

不妨设最大交替和的序列为 ${a_1,a_2,...,a_n}$，最大交替和为 $sum$。其中，$n$ 是奇数。

则 $sum=(a_1-a_2)+...+(a_{n-2}-a_{n-1})+a_n$ 或者 $sum=(a_n-a_{n-1})+(a_{n-2}-a_{n-3}+...+(a_3-a_2)+a_1$，也就是说必有一个数无法配对。我们设这个无法配对的数是 $a_1$。

我们不妨引入一个数 $0$ 使其配对，即  $sum=(a_n-a_{n-1})+(a_{n-2}-a_{n-3}+...+(a_3-a_2)+(a_1-0)$

因此，我们所考虑的问题（ $nums$ 数组中任意子序列的**最大**交替和）就转换为：在$nums$ 数组的最开始位置加入数 $0$，找出若干对数对

$\{ (0,a_1),(a_2,a_3),(a_{n-1},a_n)\}$，并且这些数对元素 ${a_1,a_2,a_3,...,a_n}$满足相对顺序在元数组中不变，使得数对和$sum$最大。

不难发现，此问题就是一定不限交易次数的股票买卖问题。

贪心的思路是，我们可以在连续上涨交易日买入（尽可能的赚取利润），在连续下降交易日不买入（保证不亏）。

```java
class Solution {
    public long maxAlternatingSum(int[] nums) {
        long res = nums[0];
        for(int i = 1; i < nums.length; i ++)
            res += Math.max(0, nums[i] - nums[i - 1]);
        return res;
    }
}
```

**状态DP**

在$nums$ 数组的最开始位置加入数 $0$后，此问题就转换为了**不限交易次数**的股票买卖问题。因此，我们可以考虑使用状态机DP来解决此问题

```java
class Solution {
    public long maxAlternatingSum(int[] nums) {
        Scanner sc = new Scanner(System.in);

        final int N = 100010, INF = 0x3f3f3f3f;
        long[][] f = new long[N][2]; // f[i][j]表示考虑前i天的股票, 且第i持有股票的状态是j(j为0表示不持股, j为1表示持股)
        int[] prices = new int[N];
        int n = nums.length;
        for(int i = 0; i < nums.length; i ++) prices[i + 2] = nums[i]; // 在prices[1]的位置加入数0

        f[0][1] = -INF; // 初始化不合法情况
        for(int i = 1; i <= n + 1; i ++ ){ // 价格的个数就变成了n+1个
            f[i][0] = Math.max(f[i - 1][0], f[i - 1][1] + prices[i]); // max(不持股, 卖出)
            f[i][1] = Math.max(f[i - 1][1], f[i - 1][0] - prices[i]); // max(持股, 买入)
        }

        return f[n + 1][0];
    }
}
```

### 6、树状数组优化DP

#### （1）[2926. 平衡子序列的最大和](https://leetcode.cn/problems/maximum-balanced-subsequence-sum/)



#### （2）[2407. 最长递增子序列 II](https://leetcode.cn/problems/longest-increasing-subsequence-ii/)





### 7、Kadane's algorithm

#### （1）[53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

**问题**：

给你一个整数数组 `nums` ，请你找出一个具有**最大和**的**连续子数组**（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

例子：

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

**思路**：

**Kadane's algorithm** 是动态规划里面的一个经典的算法，作用是用来求 **「最大的子序列和」**的问题。设序列为 $nums=[x_0,x_1,...,x_n]$，序列的长度为 $n$。Kadane's 算法维护了一个数组 $f[]$：

- $f[i]$表示的是一个集合，该集合包含所有以$nums[i]$为右端点的子序列，其属性为集合中子序列和的最大值。
- $f[i]$所表示的集合划分，$f[i] = max(f[i - 1] + nums[i], nums[i])$。

时间复杂度：$O(n)$

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int n = nums.length;
        int[] f = new int[n];
        f[0] = nums[0];

        int res = f[0];
        for(int i = 1; i < n; i ++ ) {
            f[i] = Math.max(f[i - 1] + nums[i], nums[i]);
            res = Math.max(res, f[i]);    
        }

        return res;
    } 
}
```

#### （2）[918. 环形子数组的最大和](https://leetcode.cn/problems/maximum-sum-circular-subarray/)

**问题**

给定一个长度为 `n` 的**环形整数数组** `nums` ，返回 *`nums` 的非空 **子数组** 的最大可能和* 。

**环形数组** 意味着数组的末端将会与开头相连呈环状。形式上， `nums[i]` 的下一个元素是 `nums[(i + 1) % n]` ， `nums[i]` 的前一个元素是 `nums[(i - 1 + n) % n]` 。

**子数组** 最多只能包含固定缓冲区 `nums` 中的每个元素一次。形式上，对于子数组 `nums[i], nums[i + 1], ..., nums[j]` ，不存在 `i <= k1, k2 <= j` 其中 `k1 % n == k2 % n` 。

例子：

```
输入：nums = [1,-2,3,-2]
输出：3
解释：从子数组 [3] 得到最大和 3
```

**数据范围**：

- `n == nums.length`
- `1 <= n <= 3 * 10^4`

**思路**：Kadane 算法

此题是 「[53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/description/)」的进阶版。求解普通数组的最大子数组和是求解环形子数组和最大值的子问题。

设数组长度为 $n$，下标从 $0$ 开始，环形子数组最大和可能包含以下两种情况：

1. 设构成最大子数组和的子数组为 $nums[i:j]$，包括 $nums[i]$到$nums[j-1]$共 $j -i$个元素。其中 $0=<i<j<n$。
2. 构成最大子数组和的子数组由两部组成 $nums[0:i]$ 和 $nums[j:n]$，其中 $0=<i<j<n$。

<img src="https://assets.leetcode-cn.com/solution-static/918/918_2.png" alt="pic2" style="zoom:67%;" />

第一种情况的求解方法与求解普通数组的最大子数组和方法完全相同，记为 $maxsubarray_1$。

第二种情况，设任意一子数组和为 $subarray$，$frontsubarray$表示$nums[0:i]$，$backsubarray$表示$nums[j:n]$，则 $min(subarray)$表示**最小子数组和**。第二种最大子数组和可由最小子数组和求得，推导过程如下：
$$
max subarray_2 = max(frontsubarray + back subarray) \\
             = max(sum - subarray) \\
             = sum + max(-subarray) \\
             = sum - min(subarray) \\
$$
最小子数组和也可以采用Kadane算法求得。

那么，环形子数组的最大和：$max(maxsubarray_1, maxsubarray_2)=max(maxsubarray_1,sum-min(subarray))$。

需要注意的是，如果 $maxsubarray_1<0$，那么数组中不包含大于等于 $0$ 的元素，也意味着$min(subarray)$将包含数组中所有的元素，导致我们得到第二情况的最大子数组和对应的子数组为空。在这种情况下，我只能取 $maxsubarray_1$作为答案。

#### （3）[1186. 删除一次得到子数组最大和](https://leetcode.cn/problems/maximum-subarray-sum-with-one-deletion/)

**问题**：

给你一个整数数组，返回它的某个 **非空** 子数组（连续元素）在执行一次可选的删除操作后，所能得到的最大元素总和。换句话说，你**可以从原数组中选出一个子数组，并可以决定要不要从中删除一个元素（只能删一次哦），（删除后）子数组中至少应当有一个元素，然后该子数组（剩下）的元素总和是所有子数组之中最大**的。

注意，删除一个元素后，子数组 **不能为空**。

例子：

```
输入：arr = [1,-2,0,3]
输出：4
解释：我们可以选出 [1, -2, 0, 3]，然后删掉 -2，这样得到 [1, 0, 3]，和最大。
```

**数据范围**：

- `1 <= arr.length <= 10^5`
- `-10^4 <= arr[i] <= 10^4`

**思路**：

此题与**「最大子数组和」**非常类似，「最大子数组和」是求所子数组的最大和。「删除一次得到子数组最大和」是求子数组删除一个元素后的最大和。

「最大子数组和」可以采用Kadane's算法以$O(n)$的时间复杂度求解算法。Kadane's算法最后会求得一个数组$f$，$f[i]$表示的是一个集合，该集合表示所有以$nums[i]$为右端点的子序列和的最大值。

采用Kadane算法，我们很容易计算出以每个元素为右端点的子序列和的最大值。

对于一个子序列$[...,num[i-1],nums[i],nums[i+1],....]$，我们如何快速计算出删除$nums[i]$的子序列和的最大和？

可以发现，删除$nums[i]$后，原序列分为了两个子序列$[...,nums[i-1]]$和$[nums[i+1],...]$。其中，$[...,nums[i-1]]$表示以$nums[i-1]$为右端点的所有子序列，$[nums[i+1],...]$表示以$nums[i+1]$为左端点的所有子序列。

设$a[i-1$]为$nums[i-1]$为右端点的所有子序列的最大和，$b[i+1]$为以$nums[i+1]$为左端点的所有子序列的最大和，$c[i]$为删除$nums[i]$的子序列的最大和

很容易证明，

- $if(a[i-1]>0 \ \&\& \ \ b[i+1]>0 ) \ \ c[i]=a[i-1]+b[i+1]$
- $if(a[i-1]<0 \ \&\& \ \ b[i+1]>0 ) \ \ c[i]=b[i+1]$
- $if(a[i-1]>0 \ \&\& \ \ b[i+1]<0 ) \ \ c[i]=a[i-1]$
- $if(a[i-1]<0 \ \&\& \ \ b[i+1]<0 ) \ \ c[i]=max(a[i-1],b[i+1])$

我们可以采用Kadane算法快速求出数组$a$和数组$b$。

```java
class Solution {
    public int maximumSum(int[] arr) {
        int n = arr.length;
        int[] a = new int[n + 2], b = new int[n + 2];
        
        // 初始化
        a[0] = Integer.MIN_VALUE; b[0] = Integer.MIN_VALUE;
        a[1] = arr[0];
        b[1] = arr[n - 1]; // b[]
        
        // Kadane's algorithm 算法
        for(int i = 1; i < n; i ++ ) {
            a[i + 1] = Math.max(a[i] + arr[i], arr[i]);
            b[i + 1] = Math.max(b[i] + arr[n - i - 1], arr[n - i - 1]);
        }
        
        int res = Integer.MIN_VALUE;
        for(int i = 0; i < n; i ++ ) {
            int x = a[i], y = b[n - (i + 1)];

            if(x < 0 && y < 0) x = Math.max(x, y);
            else if(x < 0) x = y;
            else if(y > 0 && x > 0) x = x + y;

            res = Math.max(res, x);
        }
            
        res = Math.max(res, a[n]);
        return res;
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
