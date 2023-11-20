# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分前后缀分解

### 1、前后缀分解

#### （1）[238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/) 

**问题**

给你一个整数数组 `nums`，返回 *数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积* 。

题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。

请 **不要使用除法，**且在 `O(*n*)` 时间复杂度内完成此题。

例子

```
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**数据范围**：

- `2 <= nums.length <= 10^5`
- `-30 <= nums[i] <= 30`
- **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内

**思路**：

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int prefix = 1, postfix = 1;
        int[] res = new int[n];

        for(int i = 0; i < n; i ++) res[i] = 1;
        for(int i = 0; i < n; i ++) {
            res[i] *= prefix;  // 乘以nums[i]左边的前缀
            prefix *= nums[i]; // 更新前缀
            res[n - 1 - i] *= postfix; // 乘以nums[j]右边的后缀
            postfix *= nums[n - 1 - i]; // 更新后缀
        }
        return res;
    }
}
```

#### （2）[2906. 构造乘积矩阵](https://leetcode.cn/problems/construct-product-matrix/)

**问题**

给你一个下标从 **0** 开始、大小为 `n * m` 的二维整数矩阵 `grid` ，定义一个下标从 **0** 开始、大小为 `n * m` 的的二维矩阵 `p`。如果满足以下条件，则称 `p` 为 `grid` 的 **乘积矩阵** ：

- 对于每个元素 `p[i][j]` ，它的值等于除了 `grid[i][j]` 外所有元素的乘积。乘积对 `12345` 取余数。

返回 `grid` 的乘积矩阵。

例子

```
输入：grid = [[1,2],[3,4]]
输出：[[24,12],[8,6]]
解释：p[0][0] = grid[0][1] * grid[1][0] * grid[1][1] = 2 * 3 * 4 = 24
p[0][1] = grid[0][0] * grid[1][0] * grid[1][1] = 1 * 3 * 4 = 12
p[1][0] = grid[0][0] * grid[0][1] * grid[1][1] = 1 * 2 * 4 = 8
p[1][1] = grid[0][0] * grid[0][1] * grid[1][0] = 1 * 2 * 3 = 6
所以答案是 [[24,12],[8,6]] 。
```

**数据范围**

- `1 <= n == grid.length <= 10^5`
- `1 <= m == grid[i].length <= 10^5`
- `2 <= n * m <= 10^5`
- `1 <= grid[i][j] <= 10^9`

**思路**：

```java
class Solution {
    public int[][] constructProductMatrix(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        int prefix = 1, suffix = 1;
        final int mod = 12345;

        int[][] res = new int[n][m];

        for(int i = 0; i < n; i ++) Arrays.fill(res[i], 1);

        for(int i = 0; i < n; i ++) {
            for(int j = 0; j < m; j ++) {
                res[i][j] = (int)(((long)res[i][j] * prefix) % mod); // 乘积矩阵
                prefix = (int)(((long)prefix * grid[i][j]) % mod); // 更新前缀
                res[n - 1 - i][m - 1 - j] = (int)(((long)suffix * res[n - 1 - i][m - 1 - j]) % mod);
                suffix = (int)(((long)suffix * grid[n - 1 - i][m - 1 - j]) % mod);
            }
        }
        return res;
    }
}
```

#### （3）[2256. 最小平均差](https://leetcode.cn/problems/minimum-average-difference/)

**问题**

给你一个下标从 **0** 开始长度为 `n` 的整数数组 `nums` 。

下标 `i` 处的 **平均差** 指的是 `nums` 中 **前** `i + 1` 个元素平均值和 **后** `n - i - 1` 个元素平均值的 **绝对差** 。两个平均值都需要 **向下取整** 到最近的整数。

请你返回产生 **最小平均差** 的下标。如果有多个下标最小平均差相等，请你返回 **最小** 的一个下标。

**注意：**

- 两个数的 **绝对差** 是两者差的绝对值。
- `n` 个元素的平均值是 `n` 个元素之 **和** 除以（整数除法） `n` 。
- `0` 个元素的平均值视为 `0` 。

**数据范围**：

- `1 <= nums.length <= 105`
- `0 <= nums[i] <= 105`

思路：

```java
class Solution {
    public int minimumAverageDifference(int[] nums) {
        int n = nums.length;
        long[] res = new long[n];

        long prefix = 0, suffix = 0;
        for(int i = 0; i < n; i ++) {
            prefix += nums[i]; 
            res[i] = Math.abs(prefix / (i + 1) - res[i]);
            res[n - 1 - i] = Math.abs((i == 0 ? 0 : suffix / i) - res[n - 1 - i]);
            suffix += nums[n - 1 - i];
        }

        int idx = 0;
        for(int i = 0; i < n; i ++) 
            if(res[i] < res[idx]) 
                idx = i;
            
        return idx;
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
