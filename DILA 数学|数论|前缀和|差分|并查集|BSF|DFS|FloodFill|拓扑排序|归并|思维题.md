# |Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>
> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分

### 1、数学

#### （1）504. 七进制数

给定一个整数 `num`，将其转化为 **7 进制**，并以字符串形式输出。

**思路**：

```java
class Solution {
    public String convertToBase7(int num) {
        // return Integer.toString(num, 7); // API
        if(num == 0) return "" + 0;

        String sym = num < 0 ? "-" : ""; // 符号
        Deque<Integer> stk = new ArrayDeque<>();
        while(num != 0){
            stk.push(Math.abs(num % 7));
            num /= 7;
        }

        String res = ""; // 栈顶元素存储高位, 从高位到低位拼接
        while(!stk.isEmpty()) res = res + stk.pop();
        
        return sym + res;
    }
}
```

#### （2）[1401. 圆和矩形是否有重叠](https://leetcode.cn/problems/circle-and-rectangle-overlapping/)

**问题**：

给你一个以 `(radius, xCenter, yCenter)` 表示的圆和一个与坐标轴平行的矩形 `(x1, y1, x2, y2)` ，其中 `(x1, y1)` 是矩形左下角的坐标，而 `(x2, y2)` 是右上角的坐标。

如果圆和矩形有重叠的部分，请你返回 `true` ，否则返回 `false` 。

换句话说，请你检测是否 **存在** 点 `(xi, yi)` ，它既在圆上也在矩形上（两者都包括点落在边界上的情况）。

例子：

![img](https://assets.leetcode.com/uploads/2020/02/20/sample_4_1728.png)

```
输入：radius = 1, xCenter = 0, yCenter = 0, x1 = 1, y1 = -1, x2 = 3, y2 = 1
输出：true
解释：圆和矩形存在公共点 (1,0) 。
```

**数据范围**：

- `1 <= radius <= 2000`
- `-10^4 <= xCenter, yCenter <= 10^4`
- `-10^4 <= x1 < x2 <= 10^4`
- `-10^4 <= y1 < y2 <= 10^4`

**思路**：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-06-25 22.50.29.png" alt="截屏2023-06-25 22.50.29" style="zoom: 20%;" />

```java
class Solution {
    public boolean checkOverlap(int radius, int xCenter, int yCenter, int x1, int y1, int x2, int y2) {
        double ux = Math.max((Math.abs(xCenter - (x1+x2)/2.0) - Math.abs((x1-x2)/2.0)), 0);
        double uy = Math.max((Math.abs(yCenter - (y1+y2)/2.0) - Math.abs((y1-y2)/2.0)), 0);
        return ux * ux + uy * uy <= radius * radius;
    }
}
```



### 2、数论

#### （I）质数

###### （1）204. 计数质数

**问题**：给定整数 `n` ，返回 *所有小于非负整数 `n` 的质数的数量* 。

**思路**：**线性筛质数**

```java
class Solution {

    int[] primes; // primes 存储所有的质数
    int cnt = 0; // 质数的个数

    boolean[] st; // st[i] 为true, 表示i是合数且被筛过
    int[] minp; // minp[i] 是i的最小质因子
    boolean[] isPrime; // isPrime[i] 为true表示i是质数

    public void getPrimes(int n) { // 线性筛质数
        for(int i = 2; i <= n; i ++ ) {
            if(!st[i]) { isPrime[i] = true; minp[i] = i; primes[cnt ++] = i; } // i还没有被筛过, i一定是质数
            for(int j = 0; primes[j] <= n / i; j ++ ) {
                st[primes[j] * i] = true; // primes[j] * i是合数
                minp[primes[j] * i] = primes[j]; // primes[j] * i的最小质因子数primes[j]
                if(i % primes[j] == 0) break;
            }
        }
    }

    public int countPrimes(int n) {
        if(n <= 2) return 0;

        primes = new int[n + 10];
        st = new boolean[n + 10];
        minp = new int[n + 10];
        isPrime = new boolean[n + 10];

        getPrimes(n - 1);
        return cnt;
    }
}
```

#### （II）约数

###### （1）1979. 找出数组的最大公约数

**问题**：给你一个整数数组 `nums` ，返回数组中最大数和最小数的 **最大公约数** 。两个数的 **最大公约数** 是能够被两个数整除的最大正整数。

**思路**：欧几里得算法求最大公约数

```java
class Solution {

    public int gcd(int a, int b) { // 欧几里得算法求a和b的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }

    public int findGCD(int[] nums) {
        int minv = Integer.MAX_VALUE, maxv = Integer.MIN_VALUE;
        for(int i = 0; i < nums.length; i ++ ) {
            maxv = Math.max(maxv, nums[i]);
            minv = Math.min(minv, nums[i]);
        }
        return gcd(minv, maxv);
    }
}
```

###### （2）6015. 统计可以被 K 整除的下标对数目:writing_hand: 

**问题**：

给你一个下标从 **0** 开始、长度为 `n` 的整数数组 `nums` 和一个整数 `k` ，返回满足下述条件的下标对 `(i, j)` 的数目：

- `0 <= i < j <= n - 1` 且
- `nums[i] * nums[j]` 能被 `k` 整除。

**思路**：**求最大公约数** 和 **求约数**

- 对于每一个$nums[i]$,  $(nums[i] \times nums[j]) \% k == 0$，即 $nums[i] \times nums[j]$ 至少包含一个$k$ 
- $d = gcd(nums[i],k)$，那么 $nums[j]$ 一定包含因数 $k/d$ 
- 求出 $nums$ 中包含因数 $k/d$ 数的个数，即是满足条件$(nums[i] \times nums[j]) \% k == 0$，下标对$(i,j)$的数目
- 将所有的下标对累加起来

```java
class Solution {
    int[] hash = new int[100010]; // hash[i]表示i出现的次数, 即整个nums数组中包含i为约数的数的个数
    
    public int gcd(int a, int b){ // 求a和b的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }
    
    public void getDivisors(int x){ // 求 x 所有约数
        for(int i = 1; i <= x / i; i ++)
            if(x % i == 0){
                hash[i] ++;
                if(x / i != i) hash[x / i] ++ ;
            }
    }
    
    public long coutPairs(int[] nums, int k) {
        long res = 0;
        
        for(int i = 0; i < nums.length; i ++){
            int d =  gcd(nums[i], k);
            res += hash[k / d];
            
            getDivisors(nums[i]);
        }
        return res;
    }
}
```

###### （3）5996. 统计数组中相等且可以被整除的数对

**问题**：

给你一个下标从 **0** 开始长度为 `n` 的整数数组 `nums` 和一个整数 `k` ，请你返回满足 `0 <= i < j < n` ，`nums[i] == nums[j]` 且 **`(i * j)` 能被 `k` 整除的数对 `(i, j)` 的 数目** 。

**思路**：**此题是上一题的扩展**

```java
class Solution {
    int[][] hash = new int[110][110]; // hash[i][j]表示nums中所有值等于i的数,它们的数组下标包含因数j的数的个数

    public int gcd(int a, int b){ // 求a和b的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }

    public void getDivisors(int num, int x){ // 求x的所有约数
        for(int i = 1; i <= x / i; i ++)
            if(x % i == 0){
                hash[num][i] ++ ;
                if(x / i != i) hash[num][x / i] ++;
            }
    }

    public int countPairs(int[] nums, int k) {
        int res = 0;
        Arrays.fill(hash[nums[0]], 1); // nums[0]的下标为0, 0包含所有的因数(0~k), 因此个数都初始化为1
        for(int i = 1; i < nums.length; i ++){
            int d = gcd(i, k);
            res += hash[nums[i]][k / d]; // 满足nums[i] == nums[j] 且 (i*j)%k==0, j的个数

            getDivisors(nums[i], i);
        }
        return res;
    }
}
```

#### （III）线性同余定理

###### （1）[523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)



###### （2）[523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)



###### （3）[560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)



###### （4）[2845. 统计趣味子数组的数目](https://leetcode.cn/problems/count-of-interesting-subarrays/)





### 3、前缀和

#### （1）2100. 适合打劫银行的日子:writing_hand: （前缀和+后缀和）

**问题**：

你和一群强盗准备打劫银行。给你一个下标从 **0** 开始的整数数组 `security` ，其中 `security[i]` 是第 `i` 天执勤警卫的数量。日子从 `0` 开始编号。同时给你一个整数 `time` 。如果第 `i` 天满足以下所有条件，我们称它为一个适合打劫银行的日子：

- 第 `i` 天前和后都分别至少有 `time` 天
- 第 `i` 天前连续 `time` 天警卫数目都是**非递增的**
- 第 `i` 天后连续 `time` 天警卫数目都是**非递减的**

更正式的，第 `i` 天是一个合适打劫银行的日子当且仅当：`security[i - time] >= security[i - time + 1] >= ... >=security[i] <= ... <= security[i + time - 1] <= security[i + time]`.

请你返回一个数组，包含 **所有** 适合打劫银行的日子（下标从 **0** 开始）。返回的日子可以 **任意** 顺序排列。

**思路**：**前缀和**与**后缀和**，$O(n)$ 

- `down` 数组，前缀和, `down[i]`表示以`i`为终点的连续非递增序列的最大长度
- `up`数组，后缀和, `up[i]` 表示以`i`为起点的连续非递减序列的最大长度 
- 当`i`同时满足`down[i] >= k + 1 && up[i] >= k + 1`条件时，即为一个解

```java
class Solution {
    public List<Integer> goodDaysToRobBank(int[] security, int time) {
        int n = security.length, k = time;
        int[] a = security;

        int[] down = new int[n]; // 前缀和, down[i]表示以i为终点的连续非递增序列的最大长度
        int[] up = new int[n]; // 后缀和, up[i] 表示以i为起点的连续非递减序列的最大长度 

        down[0] = up[n - 1] = 1; // 初始化前缀和数组和后缀和数组
        for(int i = 1; i < n; i ++){
            down[i] = a[i] <= a[i - 1] ? down[i - 1] + 1 : 1; // 用down[i - 1]更新down[i]
            up[n - i - 1] = a[n - i - 1] <= a[n - i] ? up[n - i] + 1 : 1; // 同理
        }

        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < n; i ++)
            if(down[i] >= k + 1 && up[i] >= k + 1) // 找出所有满足条件的 i
                res.add(i);   
        return res;
    }
}
```

#### （2）5203. 统计可以提取的工件:writing_hand: （二维前缀和）

**问题**：

存在一个 `n x n` 大小、下标从 **0** 开始的网格，网格中埋着一些工件。给你一个整数 `n` 和一个下标从 **0** 开始的二维整数数组 `artifacts` ，`artifacts` 描述了矩形工件的位置，其中 `artifacts[i] = [r1i, c1i, r2i, c2i]` 表示第 `i` 个工件在子网格中的填埋情况：

- `(r1i, c1i)` 是第 `i` 个工件 **左上** 单元格的坐标，且
- `(r2i, c2i)` 是第 `i` 个工件 **右下** 单元格的坐标。

你将会挖掘网格中的一些单元格，并清除其中的填埋物。如果单元格中埋着工件的一部分，那么该工件这一部分将会裸露出来。如果一个工件的所有部分都都裸露出来，你就可以提取该工件。

给你一个下标从 **0** 开始的二维整数数组 `dig` ，其中 `dig[i] = [ri, ci]`表示你将会挖掘单元格 `(ri, ci)` ，返回你可以提取的工件数目。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203140858112.jpeg" alt="img" width="230px" />

**注意**：生成的测试用例满足：

- 不存在重叠的两个工件。
- 每个工件最多只覆盖 `4` 个单元格。
- `dig` 中的元素互不相同。

**思路**：时间复杂度 $O(n^2)$ 

- 先遍历 `dig` 数组给 `n * n` 的矩阵左上标记，不妨标记为 `1`。

- 接下来遍历 `artifacts`，问题转化为求子矩阵 `[r1,c1, r2,c2]` 是否全是 `1` 即可。可以使用二维前缀和加速这个过程。

```java
class Solution {

    int[][] a, s; // a 表示原数组, s 是前缀和数组

    public int digArtifacts(int n, int[][] artifacts, int[][] dig) {
        a = new int[n + 1][n + 1]; // a和s都是从下标从1开始
        s = new int[n + 1][n + 1];

        for(int i = 0; i < dig.length; i ++) { // 构建元素组
            int x = dig[i][0] + 1, y = dig[i][1] + 1;
            a[x][y] = 1;
        }

        for(int i = 1; i <= n; i ++ ) // 求前缀和数组
            for(int j = 1; j <= n; j ++)
                s[i][j] = a[i][j] + s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1];
        
        int res = 0;
        for(int i = 0; i < artifacts.length; i ++ ) {
            int x1 = artifacts[i][0] + 1, y1 = artifacts[i][1] + 1;
            int x2 = artifacts[i][2] + 1, y2 = artifacts[i][3] + 1;

            int sum = s[x2][y2] - s[x2][y1 - 1] -s[x1 - 1][y2] + s[x1 - 1][y1 - 1]; // 求区域和
            if(sum == (x2 - x1 + 1) * (y2 - y1 + 1)) // 可以挖出一个工件
                res ++ ;
        }
        return res;
    }
}
```

#### （3）1413. 逐步求和得到正数的最小值

**问题**

给你一个整数数组 `nums` 。你可以选定任意的 **正数** startValue 作为初始值。

你需要从左到右遍历 `nums` 数组，并将 startValue 依次累加上 `nums` 数组中的值。

请你在确保**累加和**始终大于等于 1 的前提下，选出一个最小的 **正数** 作为 startValue 。

**思路**

该题等价于选择一个最小正整数 `startValue` 与数组 `nums` 凑成新的数组 `[startValue, ...nums]`，并且这个数组的**前缀和**都要大于等于 1。

那么就是 **startValue + nums 的前缀和中最小的 >= 1**。当然还需要注意 nums 的前缀和可能全都大于 0，这种情况结果是 1。

```java
class Solution {
    public int minStartValue(int[] nums) {
        int n = nums.length;
        int[] s = new int[n + 1]; // 前缀和数组

        boolean flag = true; // true 表示nums数组的前缀和都大于等于1
        int res = 0x3f3f3f3f; // 前缀和的最小值
        for(int i = 1; i <= n; i ++ ) {
            s[i] = s[i - 1] + nums[i - 1];
            if(s[i] <= 0) flag = false;
            res = Math.min(res, s[i]);
        }
            
        if(!flag) return Math.abs(res) + 1;
        return 1; // nums 的前缀和可能全都大于 0，这种情况结果是 1
    }
}
```

#### （4）[1093. 大样本统计](https://leetcode.cn/problems/statistics-from-a-large-sample/)（前缀和+二分）

**问题**：

我们对 `0` 到 `255` 之间的整数进行采样，并将结果存储在数组 `count` 中：`count[k]` 就是整数 `k` 在样本中出现的次数。

计算以下统计数据:

- `minimum` ：样本中的最小元素。
- `maximum` ：样品中的最大元素。
- `mean` ：样本的平均值，计算为所有元素的总和除以元素总数。
- `median` ：如果样本的元素个数是奇数，那么一旦样本排序后，中位数 `median` 就是中间的元素。如果样本中有偶数个元素，那么中位数`median` 就是样本排序后中间两个元素的平均值。
- `mode` ：样本中出现次数最多的数字。保众数是 **唯一** 的。

以浮点数数组的形式返回样本的统计信息 `[minimum, maximum, mean, median, mode]` 。与真实答案误差在 `10^-5` 内的答案都可以通过。

例子：

```
输入：count = [0,1,3,4,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
输出：[1.00000,3.00000,2.37500,2.50000,3.00000]
解释：用count表示的样本为[1,2,2,2,3,3,3,3]。
最小值和最大值分别为1和3。
均值是(1+2+2+2+3+3+3+3) / 8 = 19 / 8 = 2.375。
因为样本的大小是偶数，所以中位数是中间两个元素2和3的平均值，也就是2.5。
众数为3，因为它在样本中出现的次数最多。
```

**数据范围**：

- `count.length == 256`
- `0 <= count[i] <= 10^9`
- `1 <= sum(count) <= 10^9`
- `count` 的众数是 **唯一** 的

思路：

在直方图上面求中位数

- 方法1：用双指针
- 方法2：前缀和计数数组，中位数使用二分查找方法

```java
class Solution {

    public int search(int[] s, int l, int r, double x) { // 二分搜索左边界
        while(l < r) {
            int mid = l + r >> 1;
            if(s[mid] >= x) r = mid;
            else l = mid + 1;
        }
        return l;
    }

    public double[] sampleStats(int[] count) {
        int n = count.length;

        int minimum = Integer.MAX_VALUE, maximum = Integer.MIN_VALUE; // 最小值和最大值
        int mean = 0, mode = 0; // 均值和众数

        long sum = 0, cnt = 0;
        for(int i = 0; i < n; i ++ ) {
            cnt += count[i];
            sum += (long)i * count[i]; // 防止乘法溢出
            if(count[i] != 0) {
                minimum = Math.min(minimum, i);
                maximum = Math.max(maximum, i);
            }
            if(count[mode] < count[i]) mode = i;
        }
        // 在直方图上面求中位数
        // 方法1：用双指针
        // 方法2：前缀和计数数组，中位数使用二分查找方法
        int[] s = new int[n + 10]; // 从下标1开始
        for(int i = 1; i <= n; i ++ ) {
            int x = count[i - 1];
            s[i] = s[i - 1] + x;
        }

        int l = search(s, 1, n, Math.ceil(cnt / 2.0));
        
        double median = l - 1;
        if(cnt % 2 == 0) {
            median += search(s, 1, n, Math.ceil(cnt / 2.0) + 1) - 1;
            median = median / 2.0;
        }

        return new double[]{ minimum, maximum, sum / (double)cnt, median, mode };
    }
}
```

#### （5）[2559. 统计范围内的元音字符串数](https://leetcode.cn/problems/count-vowel-strings-in-ranges/) （前缀和）

**问题**：

给你一个下标从 **0** 开始的字符串数组 `words` 以及一个二维整数数组 `queries` 。

每个查询 `queries[i] = [li, ri]` 会要求我们统计在 `words` 中下标在 `li` 到 `ri` 范围内**包含** 这两个值）并且以元音开头和结尾的字符串的数目。

返回一个整数数组，其中数组的第 `i` 个元素对应第 `i` 个查询的答案。

**注意：**元音字母是 `'a'`、`'e'`、`'i'`、`'o'` 和 `'u'` 。

例子：

```
输入：words = ["aba","bcb","ece","aa","e"], queries = [[0,2],[1,4],[1,1]]
输出：[2,3,0]
解释：以元音开头和结尾的字符串是 "aba"、"ece"、"aa" 和 "e" 。
查询 [0,2] 结果为 2（字符串 "aba" 和 "ece"）。
查询 [1,4] 结果为 3（字符串 "ece"、"aa"、"e"）。
查询 [1,1] 结果为 0 。
返回结果 [2,3,0] 。
```

**数据范围**：

- `1 <= words.length <= 10^5`
- `1 <= words[i].length <= 40`
- `words[i]` 仅由小写英文字母组成
- `sum(words[i].length) <= 3 * 10^5`
- `1 <= queries.length <= 10^5`
- `0 <= queries[j][0] <= queries[j][1] < words.length`

**思路**：前缀和，时间复杂度为 $O(n)$

```java
class Solution {
    public boolean check(char ch) { // 判断ch是否是元音字母
        if(ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch == 'u') return true;
        return false;
    }

    public int[] vowelStrings(String[] words, int[][] queries) {
        int n = words.length, m = queries.length;
        int[] num = new int[n + 10];

        for(int i = 0; i < n; i ++ ) { // 将字符串数组转换成整数数组
            int len = words[i].length();
            char start = words[i].charAt(0), end = words[i].charAt(len - 1);
            if(check(start) && check(end)) num[i + 1] = 1;
        }

        int[] s = new int[n + 10]; // 计算前缀和数组
        for(int i = 1; i <= n; i ++ )
            s[i] = s[i - 1] + num[i];

        int[] res = new int[m];
        for(int i = 0; i < m ; i ++ ) { // 区间查询
            int l = queries[i][0] + 1, r = queries[i][1] + 1;
            res[i] = s[r] - s[l - 1];
        }

        return res;
    }
}
```

#### （6）[1171. 从链表中删去总和值为零的连续节点](https://leetcode.cn/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)（前缀和+哈希表）

**问题**：

给你一个链表的头节点 `head`，请你编写代码，反复删去链表中由 **总和** 值为 `0` 的连续节点组成的序列，直到不存在这样的序列为止。

删除完毕后，请你返回最终结果链表的头节点。

你可以返回任何满足题目要求的答案。

（注意，下面示例中的所有序列，都是对 `ListNode` 对象序列化的表示。）

例子：

```
输入：head = [1,2,-3,3,1]
输出：[3,1]
提示：答案 [1,2,1] 也是正确的。
```

**数据范围**：

- 给你的链表中可能有 `1` 到 `1000` 个节点。
- 对于链表中的每个节点，节点的值：`-1000 <= node.val <= 1000`.

**思路**：

- 将链表转换成数组（下标从1开始）
- 求数组的前缀和数组（下标从1开始）
- 对于前缀和数组的每一个元素$s[i]$，查找其左边第一个与其相等的元素$s[k]$，则子数组$sum(s[k+1],...,s[i])=0$应该被删除。
- 如何快速查找$s[k]$？用哈希表维护$i$之前每一个前缀和数组元素出现的位置 $(s[k],k)$。
- 当子数组${s[k+1],...,s[i]}$被删除时，哈希表中对应的键值对元素也应该被删除。同时将$(s[i],i)$加入哈希表
- 将所有为0的子数组删除后的数组转换成链表。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeZeroSumSublists(ListNode head) {
        List<Integer> arr = new ArrayList<>();
        arr.add(0x3f3f3f3f); // 站位
        for(ListNode i = head; i != null; i = i.next) arr.add(i.val); // 链表转换成数组, 下标从1开始

        int n  = arr.size();
        int[] s = new int[n]; // 前缀和, 下标从1开始
        for(int i = 1; i < n; i ++ )
            s[i] = s[i - 1] + arr.get(i);

        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i ++ ) {
            if(map.get(s[i]) == null) { 
                map.put(s[i], i); // 存储s[i]出现的下标i
                continue;
            }
            for(int j = map.get(s[i]) + 1; j < n && j <= i; j ++ ) {
                arr.set(j, 0x3f3f3f3f); // 删除和等于0的子数组
                map.remove(s[j]); // 将map中对应的元素去除
            }
            map.put(s[i], i);
        }

        ListNode h = new ListNode(), p = h; 
        for(int i = 1; i < arr.size(); i ++) {
            if(arr.get(i) == 0x3f3f3f3f) continue;
            p.next = new ListNode(arr.get(i));
            p = p.next;
        }
        return h.next;
    }
}
//   [1,2,-3,3,1]
// [0,1,3, 0,3,4]
```

#### （7）[1177. 构建回文串检测](https://leetcode.cn/problems/can-make-palindrome-from-substring/) （前缀和）

**问题**：

给你一个字符串 `s`，请你对 `s` 的子串进行检测。

每次检测，待检子串都可以表示为 `queries[i] = [left, right, k]`。我们可以 **重新排列** 子串 `s[left], ..., s[right]`，并从中选择 **最多** `k` 项替换成任何小写英文字母。 

如果在上述检测过程中，子串可以变成回文形式的字符串，那么检测结果为 `true`，否则结果为 `false`。

返回答案数组 `answer[]`，其中 `answer[i]` 是第 `i` 个待检子串 `queries[i]` 的检测结果。

注意：在替换时，子串中的每个字母都必须作为 **独立的** 项进行计数，也就是说，如果 `s[left..right] = "aaa"` 且 `k = 2`，我们只能替换其中的两个字母。（另外，任何检测都不会修改原始字符串 `s`，可以认为每次检测都是独立的）

例子：

```
输入：s = "abcda", queries = [[3,3,0],[1,2,0],[0,3,1],[0,3,2],[0,4,1]]
输出：[true,false,false,true,true]
解释：
queries[0] : 子串 = "d"，回文。
queries[1] : 子串 = "bc"，不是回文。
queries[2] : 子串 = "abcd"，只替换 1 个字符是变不成回文串的。
queries[3] : 子串 = "abcd"，可以变成回文的 "abba"。 也可以变成 "baab"，先重新排序变成 "bacd"，然后把 "cd" 替换为 "ab"。
queries[4] : 子串 = "abcda"，可以变成回文的 "abcba"。
```

**数据范围**：

- `1 <= s.length, queries.length <= 10^5`
- `0 <= queries[i][0] <= queries[i][1] < s.length`
- `0 <= queries[i][2] <= s.length`
- `s` 中只有小写英文字母

**思路**：

- 针对 `s` 中的每一个字母统计其前缀和。
- 针对每一个查询$(l,r,k)$，统计$[l,r]$的子串中出现次数为奇数的字母的个数 $odds$。
- 如果 $odds / 2 <= k$ ，那么子串$[l,r]$可以通过重排和替换变成回文串。

```java
class Solution {
    public List<Boolean> canMakePaliQueries(String s, int[][] queries) {
        int n = s.length(), m = queries.length;
        int[][] pre = new int[26][n + 1]; // 针对s中每一个字母计算其前缀和数组

        for(int i = 1; i <= n; i ++ ) {
            int ch = s.charAt(i - 1) - 'a';
            for(int j = 0; j < 26; j ++) 
                pre[j][i] = (j == ch ? pre[ch][i - 1] + 1 : pre[j][i - 1]); // 构造前缀和数组
        }

        List<Boolean> arr = new ArrayList<>();
        for(int i = 0; i < m; i ++) { // m个查询
            int l = queries[i][0] + 1, r = queries[i][1] + 1, k = queries[i][2];
            int odds = 0;
            for(int j = 0; j < 26; j ++ ) { // 统计[l,r]的子串中出现次数为奇数的字母的个数
                int cnt = pre[j][r] - pre[j][l - 1];
                if((cnt & 1) == 1) odds ++;
            }
            if(odds / 2 <= k) arr.add(true); // 子串[l,r]可以通过重排和替换变成回文串
            else arr.add(false);
        }
        return arr;
    }
}
```

#### （8）[238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/) 

**问题**

给你一个整数数组 `nums`，返回 *数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积* 。

题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。

请**不要使用除法，**且在 `O(*n*)` 时间复杂度内完成此题。

例子

```
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**数据范围**

- `2 <= nums.length <= 10^5`
- `-30 <= nums[i] <= 30`
- **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内

**思路**

**方案一**：时间复杂度$O(n)$，空间复杂度 $O(n)$

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] prefix = new int[n + 1], postfix = new int[n + 1]; // prefix[i-1]和postfix[i+1]分别表示nums[i]的前缀乘积和后缀乘积

        prefix[0] = postfix[n] = 1;
        for(int i = 1, j = n - 1; i <= n && j >= 0; i ++, j -- ) {
            prefix[i] = prefix[i - 1] * nums[i - 1];
            postfix[j] = postfix[j + 1] * nums[j];
        }

        int[] res = new int[n];
        for(int i = 0; i < n; i ++)
            res[i] = prefix[i - 1 + 1] * postfix[i + 1];
            
        return res;
    }
}
```

**方案二**：时间复杂度$O(n)$，空间复杂度 $O(1)$

设置两个变量$prefix$ 和 $postfix$。对于当前枚举的位置 $i$，$prefix$ 表示$nums[0]$~$nums[i-1]$之间所有数的乘积，$postfix$表示$nums[n-1-i]$ ~ $nums[n-1]$所有数的乘积，$res[i]$ 表示除 $nums[i]$数组所有数的乘积。

在当前枚举的位置 $i$，$res[i]$先乘以其前缀$prefix$，$res[n-1-i]$乘以其后缀$postfix$，再更新前缀 $prefix *= nums[i]$，

更新其后缀 $postfix*=nums[n-1-i]$。

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int prefix = 1, postfix = 1;
        int[] res = new int[n];
        Arrays.fill(res, 1);
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

### 4、差分

#### （1）370. 区间加法

**问题**：

假设你有一个长度为 **n** 的数组，初始情况下所有的数字均为 **0**，你将会被给出 **k** 个更新的操作。

其中，每个操作会被表示为一个三元组：**[startIndex, endIndex, inc]**，你需要将子数组 **A[startIndex ... endIndex]**（包括 startIndex 和 endIndex）增加 **inc**。

请你返回 **k** 次操作后的数组。

**思路**：**差分**

```python
class Solution:

    def __init__(self):
        self.a = [] # 原数组, 下标从1开始
        self.b = [] # 差分数组, 下标从1开始
        self.n = 0

    def insert(self, l, r, c):
        self.b[l] += c
        self.b[r + 1] -= c

    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
        self.n = length
        self.a = [0 for i in range(self.n + 10)] # 元组组所有数字为0
        self.b = [0 for i in range(self.n + 10)] # 差分数组所有数字也为0

        for i in range(len(updates)):
            l, r, c = updates[i][0], updates[i][1], updates[i][2]
            self.insert(l + 1, r + 1, c)

        for i in range(1, self.n + 1):
            self.a[i] = self.a[i - 1] + self.b[i]
        
        return self.a[1:self.n + 1] # 返回[1, n + 1)
```



#### （2）[2251. 花期内花的数目](https://leetcode.cn/problems/number-of-flowers-in-full-bloom/)

**问题**

给你一个下标从 **0** 开始的二维整数数组 `flowers` ，其中 `flowers[i] = [starti, endi]` 表示第 `i` 朵花的 **花期** 从 `starti` 到 `endi` （都 **包含**）。

给你一个下标从 **0** 开始大小为 `n` 的整数数组 `people` ，`people[i]` 是第 `i` 个人来看花的时间。

请你返回一个大小为 `n` 的整数数组 `answer` ，其中 `answer[i]`是第 `i` 个人到达时在花期内花的 **数目** 。

例子

<img src="https://assets.leetcode.com/uploads/2022/03/02/ex1new.jpg" alt="img" style="zoom:67%;" />

```
输入：flowers = [[1,6],[3,7],[9,12],[4,13]], people = [2,3,7,11]
输出：[1,2,2,2]
解释：上图展示了每朵花的花期时间，和每个人的到达时间。
对每个人，我们返回他们到达时在花期内花的数目。
```

**数据范围**

- `1 <= flowers.length <= 5 * 104`
- `flowers[i].length == 2`
- `1 <= starti <= endi <= 109`
- `1 <= people.length <= 5 * 104`
- `1 <= people[i] <= 109`

**思路**：离散化+差分

```java
class Solution {

    public void insert(int l, int r, int c, int[] b) {
        b[l] += c;
        b[r + 1] -= c;
    }

    public int[] fullBloomFlowers(int[][] flowers, int[] people) {
        // 离散化
        Set<Integer> set = new HashSet<>(); // 去重
        for(int i = 0; i < flowers.length; i ++ ) {
            int a = flowers[i][0], b = flowers[i][1];
            set.add(a); set.add(b);
        }
        for(int i = 0; i < people.length; i ++) 
            set.add(people[i]);

        ArrayList<Integer> arr = new ArrayList<>(set);
        Collections.sort(arr); // 排序

        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < arr.size(); i ++) 
            map.put(arr.get(i), i + 1);
        
        // 差分
        int n = arr.size();
        int[] b = new int[n + 10];

        for(int i = 0; i < flowers.length; i ++) {
            int x = flowers[i][0], y = flowers[i][1];
            insert(map.get(x), map.get(y), 1, b);
        }

        int[] a = new int[n + 10];
        for(int i = 1; i < a.length; i ++ )
            a[i] = a[i - 1] + b[i];
        
        int[] res = new int[people.length];
        for(int i = 0; i < people.length; i ++)
            res[i] = a[map.get(people[i])];

        return res;
    }
}
```



### 5、并查集

#### （1）[6464. 最大公约数遍历](https://leetcode.cn/problems/greatest-common-divisor-traversal/)（分解质因数+并查集）

**问题**：

给你一个下标从 **0** 开始的整数数组 `nums` ，你可以在一些下标之间遍历。对于两个下标 `i` 和 `j`（`i != j`），当且仅当 `gcd(nums[i], nums[j]) > 1` 时，我们可以在两个下标之间通行，其中 `gcd` 是两个数的 **最大公约数** 。

你需要判断 `nums` 数组中 **任意** 两个满足 `i < j` 的下标 `i` 和 `j` ，是否存在若干次通行可以从 `i` 遍历到 `j` 。

如果任意满足条件的下标对都可以遍历，那么返回 `true` ，否则返回 `false` 。

例子：

```
输入：nums = [2,3,6]
输出：true
解释：这个例子中，总共有 3 个下标对：(0, 1) ，(0, 2) 和 (1, 2) 。
从下标 0 到下标 1 ，我们可以遍历 0 -> 2 -> 1 ，我们可以从下标 0 到 2 是因为 gcd(nums[0], nums[2]) = gcd(2, 6) = 2 > 1 ，从下标 2 到 1 是因为 gcd(nums[2], nums[1]) = gcd(6, 3) = 3 > 1 。
从下标 0 到下标 2 ，我们可以直接遍历，因为 gcd(nums[0], nums[2]) = gcd(2, 6) = 2 > 1 。同理，我们也可以从下标 1 到 2 因为 gcd(nums[1], nums[2]) = gcd(3, 6) = 3 > 1 。
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^5`

**思路**：**分解质因数**+**并查集**

两个下标之间可以通行可以定义为：当且仅当 `gcd(nums[i], nums[j]) > 1` 时，我们可以在两个下标之间通行。

暴力的思想是：计算所有两两整数直接的最大公约数，并且判断其之间是否通行。考虑到数据范围，直接暴力枚举时间复杂较高。

它的等价定义是：如果$nums[i]$ 和 $nums[j]$ 的公约数大于1，那么两者的最大公约数也至少大于1。

我们不妨**分解每一个数的所有质因数**，将有公共质因数的数(下标)放到相同的集合中(这意味集合中的所有数都有相同大于1的公约数)，集合中的数(对应的下标)可以互相通行。实际上可以通行的两个下标 `i` 和 `j`仍然满足 `i < j` 的，即可以将集合中的下标从小到大排序，但是实现时是没有必要的。

整数数组中的 $n$ 个数构成了由 $n$ 个节点组成的图，任意满足条件的下标对都可以遍历意思是这个图是否是连通图。我们可以采**用并查集来判断一个图是否是连通图**。

```java
class Solution {

    HashMap<Integer, ArrayList<Integer>> map = new HashMap<>();
    int[] p;

    public void find_primes(int x, int index) { // 分解质因数
        for(int i = 2; i <= x / i; i ++ ) 
            if(x % i == 0) {
                while(x % i == 0) x /= i;

                ArrayList<Integer> arr = map.getOrDefault(i, new ArrayList<Integer>());
                arr.add(index);
                map.put(i, arr);
            }
        if(x > 1) {
            ArrayList<Integer> arr = map.getOrDefault(x, new ArrayList<Integer>());
            arr.add(index);
            map.put(x, arr);
        }
    }

    public int find(int x) {
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public boolean canTraverseAllPairs(int[] nums) { // 并查集判断连通图
        int n = nums.length;

        for(int i = 0; i < n; i ++ ) find_primes(nums[i], i); // 分解质因数

        p = new int[n];
        for(int i = 0; i < n; i ++ ) p[i] = i; // 初始化并查集

        Set<Integer> keySet = map.keySet();
        for(Integer key : keySet) {
            ArrayList<Integer> a = map.get(key);
            for(int i = 1; i < a.size(); i ++ )
                p[find(a.get(i))] = find(a.get(i - 1)); // 集合合并
        }

        int parent = find(0);
        for(int i = 0; i < n; i ++ ) 
            if(parent != find(i)) return false;

        return true;
    }
}
```

#### （2）261. 以图判树

**问题**：

给定编号从 `0` 到 `n - 1` 的 `n` 个结点。给定一个整数 `n` 和一个 `edges` 列表，其中 `edges[i] = [ai, bi]` 表示图中节点 `ai` 和 `bi` 之间存在一条无向边。

如果这些边能够形成一个合法有效的树结构，则返回 `true` ，否则返回 `false` 。

<img src="https://assets.leetcode.com/uploads/2021/03/12/tree2-graph.jpg" alt="img" width="255px" />

**思路**：**树的定义**，包含 `n` 个结点 `n-1` 条边的无向连通图是树

- 先判断是否包含`n`个结点`n-1`条边
- 如果是，合并所有的集合，判断是否是连通图

```java
class Solution {

    int[] p; // 并查集

    public int find(int x) { // 查找 x 的祖宗结点
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public boolean validTree(int n, int[][] edges) {
        if(edges.length != n - 1) return false; // 特判, 树是包含n个结点n-1条边的无向连通图

        p = new int[n];
        for(int i = 0; i < p.length; i ++) p[i] = i; // 初始化并查集
        
        int cnt = n;
        for(int i = 0; i < edges.length; i ++) {
            int x = find(edges[i][0]), y = find(edges[i][1]);
            if(x != y) {
                p[x] = y; // 合并, 两个集合
                cnt -- ;
            }
        }
        if(cnt > 1) return false; // 连通分量的数量>1, 非连通图
        return true;
    }
}
```

#### （3）323. 无向图中连通分量的数目

**问题**：

你有一个包含 `n` 个节点的图。给定一个整数 `n` 和一个数组 `edges` ，其中 `edges[i] = [ai, bi]` 表示图中 `ai` 和 `bi` 之间有一条边。

返回 *图中已连接分量的数目* 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203121420359.jpeg" alt="img" width="250px" />



**思路**：**并查集求连通块的数量**

```java
class Solution {

    int[] p; // 并查集

    public int find(int x) { // 查找x的祖宗结点
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public int countComponents(int n, int[][] edges) {
        p = new int[n];
        for(int i = 0; i < p.length; i ++) p[i] = i; // 初始化并查集
        int cnt = n; // 初始连通分量的数目

        for(int i = 0; i < edges.length; i ++) {
            int x = find(edges[i][0]), y = find(edges[i][1]);
            if(x != y) { 
                p[x] = y; // 合并两个集合
                cnt --; // 连通分量的数量减少1
            }
        }
        return cnt;
    }
}
```

#### （4）305. 岛屿数量 II

**问题**：

给你一个大小为 `m x n` 的二进制网格 `grid` 。网格表示一个地图，其中，`0` 表示水，`1` 表示陆地。最初，`grid` 中的所有单元格都是水单元格（即，所有单元格都是 `0`）。

可以通过执行 `addLand` 操作，将某个位置的水转换成陆地。给你一个数组其中 `positions[i] = [ri, ci]` 是要执行第 `i` 次操作的位置 `(ri, ci)` 。

返回一个整数数组 `answer` ，其中 `answer[i]` 是将单元格 `(ri, ci)` 转换为陆地后，地图中岛屿的数量。

**岛屿** 的定义是被「水」包围的「陆地」，通过水平方向或者垂直方向上相邻的陆地连接而成。你可以假设地图网格的四边均被无边无际的「水」所包围。

**示例**：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203031507452.jpeg" alt="img" width="500px" />

```
输入：m = 3, n = 3, positions = [[0,0],[0,1],[1,2],[2,1]]
输出：[1,1,2,3]
```

**思路**：**并查集**

- 用并查集维护所有的陆地，将水平方向或者垂直方向上相邻陆地合并成一个岛屿
- 刚开始岛屿的数量为 `cnt = 0` ，没增加一块陆地 `cnt ++`, 每次相邻的陆地合并 `cnt --`

```python
class Solution:

    def __init__(self):
        self.p = {} # p[x]存储x结点的父结点, 键和值都是二元组

    def find(self, x): # x是一个元组, 求x的祖宗结点
        if self.p[x] != x:
            self.p[x] = self.find(self.p[x])
        return self.p[x]

    def numIslands2(self, m: int, n: int, positions: List[List[int]]) -> List[int]:    
        res, cnt = [], 0 # cnt表示每次操作结束后连通块的数量
        dx, dy = [0, -1, 0, 1], [1, 0, -1, 0] # 偏移量
        
        for i in range(len(positions)):
            t = (positions[i][0], positions[i][1])
            if t in self.p: # positions含有重复元素, t此前已经处理过了
                res.append(cnt)
                continue
            
            self.p[t] = t # 用并查集维护这个集合
            cnt += 1 # 连通块数量加一

            for j in range(4): # 遍历上下左右四个方向
                tx, ty = t[0] + dx[j], t[1] + dy[j]
                if tx < 0 or tx >= m or ty < 0 or ty >= n: # (tx,ty) 越界
                    continue
                if (tx, ty) not in self.p: # g[tx][ty] == 0
                    continue
                a, b = t, (tx, ty)
                x, y = self.find(a), self.find(b) # 分别求a和b的祖宗结点
                if x != y: # a 与 b不是一个集合
                    self.p[y] = x # 合并a与b所在的集合
                    cnt -= 1 # 连通块数量减一
            res.append(cnt)
        return res
```



### 6、BFS

#### （1）[1091. 二进制矩阵中的最短路径](https://leetcode.cn/problems/shortest-path-in-binary-matrix/)

**问题**：

给你一个 `n x n` 的二进制矩阵 `grid` 中，返回矩阵中最短 **畅通路径** 的长度。如果不存在这样的路径，返回 `-1` 。二进制矩阵中的 畅通路径 是一条从 **左上角** 单元格（即，`(0, 0)`）到 右下角 单元格（即，`(n - 1, n - 1)`）的路径，该路径同时满足下述要求：

- 路径途经的所有单元格都的值都是 `0` 。
- 路径中所有相邻的单元格应当在 **8 个方向之一** 上连通（即，相邻两单元之间彼此不同且共享一条边或者一个角）。

**畅通路径的长度** 是该路径途经的单元格总数。

例子：

<img src="https://assets.leetcode.com/uploads/2021/02/18/example2_1.png" alt="img" style="zoom: 25%;" />

```
输入：grid = [[0,0,0],[1,1,0],[1,1,0]]
输出：4
```

**数据范围**：

- `n == grid.length`
- `n == grid[i].length`
- `1 <= n <= 100`
- `grid[i][j]` 为 `0` 或 `1`

**思路**：广度优先搜索

**8 个方向定义** 

```java
int[] dx = new int[]{1, 1, 0, -1, -1, -1, 0, 1};
int[] dy = new int[]{0, 1, 1, 1, 0, -1, -1, -1};
```

完整代码

```java
class Solution {

    public int bfs(int[][] g, int n) {

        if(g[0][0] == 1) return -1; // 特判
        if(g[0][0] == 0 && n == 1) return 1; // 特判

        int hh = 0, tt = 0;
        Pair<Integer, Integer>[] q = new Pair[n * n];
        q[0] = new Pair<Integer, Integer>(0, 0);
        Map<Pair<Integer, Integer>, Boolean> st = new HashMap<>();

        int[] dx = new int[]{1, 1, 0, -1, -1, -1, 0, 1};
        int[] dy = new int[]{0, 1, 1, 1, 0, -1, -1, -1};
        int layer = 1;
        while(hh <= tt) {
            int head = hh, tail = tt;
            layer += 1;
            while(hh <= tail) {
                Pair<Integer, Integer> t = q[hh ++];

                for(int i = 0; i < dx.length; i ++) {
                    int tx = t.getKey() + dx[i];
                    int ty = t.getValue() + dy[i];
                
                    if(tx >= n || tx < 0 || ty >= n || ty < 0) continue;
                    if(g[tx][ty] == 1) continue;
                    if(st.getOrDefault(new Pair<Integer, Integer>(tx, ty), false)) continue;

                    st.put(new Pair<Integer, Integer>(tx, ty), true);
                    q[++ tt] = new Pair<Integer, Integer>(tx, ty);

                    if(tx == n - 1 && ty == n - 1) return layer;
                }
            }
        }

        return -1;
    }

    public int shortestPathBinaryMatrix(int[][] grid) {
        return bfs(grid, grid.length);
    }
}
```

#### （2）[LCP 41. 黑白翻转棋](https://leetcode.cn/problems/fHi6rV/) （:o:）

**问题**：

在 `n*m` 大小的棋盘中，有黑白两种棋子，黑棋记作字母 `"X"`, 白棋记作字母 `"O"`，空余位置记作 `"."`。当落下的棋子与其他相同颜色的棋子在行、列或对角线完全包围（中间不存在空白位置）另一种颜色的棋子，则可以翻转这些棋子的颜色。

<img src="https://pic.leetcode-cn.com/1630396029-eTgzpN-6da662e67368466a96d203f67bb6e793.gif" alt="1.gif" style="zoom:40%;" />

<img src="https://pic.leetcode-cn.com/1630396240-nMvdcc-8e4261afe9f60e05a4f740694b439b6b.gif" alt="2.gif" style="zoom:40%;" />

<img src="https://pic.leetcode-cn.com/1630396291-kEtzLL-6fcb682daeecb5c3f56eb88b23c81d33.gif" alt="3.gif" style="zoom:40%;" />

「力扣挑战赛」黑白翻转棋项目中，将提供给选手一个未形成可翻转棋子的棋盘残局，其状态记作 `chessboard`。若下一步可放置一枚黑棋，请问选手最多能翻转多少枚白棋。

**注意**：

- 若翻转白棋成黑棋后，棋盘上仍存在可以翻转的白棋，将可以 **继续** 翻转白棋
- 输入数据保证初始棋盘状态无可以翻转的棋子且存在空余位置

例子：

```
输入：chessboard = [".......",".......",".......","X......",".O.....","..O....","....OOX"]

输出：4

解释：
可以选择下在 [6,3] 处，能够翻转白方四枚棋子。
```

![803f2f04098b6174397d6c696f54d709.gif](https://pic.leetcode-cn.com/1630393770-Puyked-803f2f04098b6174397d6c696f54d709.gif)

**数据范围**：

- `1 <= chessboard.length, chessboard[i].length <= 8`
- `chessboard[i]` 仅包含 `"."、"O"` 和 `"X"`

**思路**：**此题考查的是BFS的思想**

题目给出的是一个大小为 $n \times m$的黑白棋盘chessboard，其中黑棋用‘X’，白棋用'O'表示，空余位置用'.'表示。

现在我们可以在棋盘中的一个空余位置下一个黑棋，现在我们称「翻转操作」为：**若存在在放置黑棋的位置的行、列或者对角线有另一颗黑棋能完全包围（中间不存在空白位置）白棋，那么我们可以翻转这些白棋，使其变为黑棋，并且这些翻转后得到的新黑棋同样可以进行「翻转操作」。**现在我们需要求在放置一次黑棋的情况下最多能翻转多少颗白棋。

我们可以用「广度优先搜索」来解决这个问题，我们对每一个空余位置尝试黑棋放置，用一个队列来存储正在进行「翻转操作」的黑棋位置，若队列非空，我们**从队列中取出队首元素，进行行、列和对角线 8 个方向判断是否有可以翻转的白棋——判断沿着方向是否是连续的一段白棋并以另一颗黑棋结尾。若有可以翻转的白棋，则将这些白旗进行翻转，并加入队列中**。直至队列为空表示一次放置黑棋结束。

初始可以放置黑棋的全部位置中可以翻转的白棋个数最大值即为最后的答案。

```java
class Solution {

    public boolean check(char[][] g, boolean[][] st, int tx, int ty, int dx, int dy, List<Pair<Integer, Integer>> arr) {
        int n = g.length, m = g[0].length;
        int x = tx, y = ty;
        do {
            tx += dx; ty += dy;
            if(tx < 0 || tx >= n || ty < 0 || ty >= m) return false; // 越界
            if(g[tx][ty] == '.') return false; // 空白
            if(st[tx][ty] || g[tx][ty] == 'X') return true; // 遇到黑棋则返回
            arr.add(new Pair<Integer, Integer>(tx, ty)); // 将所有的连续的白棋放入arr中
            
        } while(true);
    }

    public int bfs(char[][] g, int x, int y) { // 返回以(x,y)位置作为落子的位置，能够翻转的白棋数
        int n = g.length, m = g[0].length;

        Pair<Integer, Integer>[] q = new Pair[n * m]; // 队列
        int hh = 0, tt = 0; // 队头指针和队尾指针
        q[0] = new Pair<Integer, Integer>(x, y); // 初始化队列

        boolean[][] st = new boolean[n][m]; // 如果(i,j)位置上的白棋被翻转为黑棋，则st[i][j]为true

        int[] dx = {0, 1, 1, 1, 0, -1, -1, -1}, dy = {1, 1, 0, -1, -1, -1, 0, 1};

        while(hh <= tt) { // 队列不为空
            var t = q[hh ++]; // 取出队头元素
            int tx = t.getKey(), ty = t.getValue();

            for(int i  = 0; i < dx.length; i ++ ) { // 枚举八个方向，从当前位置(dx,ty)出发，翻转白棋    
                List<Pair<Integer, Integer>> arr = new ArrayList<>();
                if(check(g, st, tx, ty, dx[i], dy[i], arr)) {
                    for(var e : arr) {
                        q[++ tt] = e; // 将该方向上所有可以翻转的白棋，入队
                        st[e.getKey()][e.getValue()] = true; // 更新判重数组
                    }
                }
            }
        }
        return tt;
    }

    public int flipChess(String[] chessboard) {
        int n = chessboard.length, m = chessboard[0].length();

        char[][] g = new char[n][m];
        for(int i = 0; i < n; i ++)
            g[i] = chessboard[i].toCharArray(); // 转换成字符数组

        int res = 0;
        for(int i = 0; i < n; i ++ ) { // 将棋盘中所有的空位置作为落子的位置，搜索最多能翻转的白棋数
            for(int j = 0; j < m ;j ++ )
                if(g[i][j] == '.') res = Math.max(res, bfs(g, i, j));
        }
        return res;
    }
}
```



### 7、DFS

#### （1）[980. 不同路径 III](https://leetcode.cn/problems/unique-paths-iii/)

**问题**：

在二维网格 `grid` 上，有 4 种类型的方格：

- `1` 表示起始方格。且只有一个起始方格。
- `2` 表示结束方格，且只有一个结束方格。
- `0` 表示我们可以走过的空方格。
- `-1` 表示我们无法跨越的障碍。

返回在四个方向（上、下、左、右）上行走时，从起始方格到结束方格的不同路径的数目**。**

**每一个无障碍方格都要通过一次，但是一条路径中不能重复通过同一个方格**。

 例子

```java
输入：[[1,0,0,0],[0,0,0,0],[0,0,2,-1]]
输出：2
解释：我们有以下两条路径：
1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2)
2. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2)
```

**数据范围**：

- `1 <= grid.length * grid[0].length <= 20`

**思路**：经典的DFS遍历矩阵问题的变体d

```java
class Solution {
    int[] start, end;
    int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
    boolean[][] st;
    int[][] g;
    int n = 0, m = 0, cnt = 0;

    public int dfs(int x, int y, int k) {
        if(x == end[0] && y == end[1]) return k - 1 == cnt ? 1 : 0; // 到达目的，并且核对所有空位置是否都走过

        int res = 0;
        for(int i = 0; i < dx.length; i ++) {
            int tx = x + dx[i], ty = y + dy[i];
            if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue;
            if(g[tx][ty] == -1) continue;
            if(st[tx][ty]) continue;

            st[tx][ty] = true;
            res += dfs(tx, ty, k + 1);
            st[tx][ty] = false;
        }
        return res;
    }
 
    public int uniquePathsIII(int[][] grid) {
        n = grid.length; m = grid[0].length;
        st = new boolean[n][m];
        g = grid;
        int barrier = 0;

        for(int i = 0; i < n; i ++)
            for(int j = 0; j < m; j ++) {
                if(grid[i][j] == 1) start = new int[]{i, j};
                if(grid[i][j] == 2) end = new int[]{i, j};
                if(grid[i][j] == -1) barrier ++;
            }
        
        cnt = n * m - 2 - barrier;
        st[start[0]][start[1]] = true;
        int res = dfs(start[0], start[1], 0);
        return res;
    }
}
// [[1,0,0,0],
//  [0,0,0,0],
//  [0,0,2,-1]]
```



### 8、FloodFill

#### （1）[1254. 统计封闭岛屿的数目](https://leetcode.cn/problems/number-of-closed-islands/) 

**问题**：

二维矩阵 `grid` 由 `0` （土地）和 `1` （水）组成。岛是由最大的4个方向连通的 `0` 组成的群，封闭岛是一个 `完全` 由1包围（左、上、右、下）的岛。

请返回 *封闭岛屿* 的数目。

例子：

<img src="https://assets.leetcode.com/uploads/2019/10/31/sample_3_1610.png" alt="img" style="zoom:60%;" />

```
输入：grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]
输出：2
解释：
灰色区域的岛屿是封闭岛屿，因为这座岛屿完全被水域包围（即被 1 区域包围）。
```

**数据范围**：

- `1 <= grid.length, grid[0].length <= 100`
- `0 <= grid[i][j] <=1`

**思路**：FloodFill模板题

注意：封闭岛要求四周都被水包围。超出边界时，此岛不是封闭岛

```java
class Solution {

    boolean[][] st = new boolean[110][110];

    public boolean floodfill(int[][] grid, int start, int end) {
        int n = grid.length, m = grid[0].length;

        Pair<Integer, Integer>[] q = new Pair[n * m];
        int hh = 0, tt = 0;
        q[0] = new Pair<Integer, Integer>(start, end);
        st[start][end] = true;

        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
        boolean res = true;
        while(hh <= tt) {
            Pair<Integer, Integer> t = q[hh ++];

            for(int i = 0; i < dx.length; i ++ ) {
                int tx = t.getKey() + dx[i], ty = t.getValue() + dy[i];

                if(tx < 0 || tx >= n || ty < 0 || ty >= m) { // 封闭岛要求四周都被水包围。超出边界时，此岛不是封闭岛
                    res = false;
                    continue;
                };
                if(grid[tx][ty] == 1) continue;
                if(st[tx][ty]) continue;

                st[tx][ty] = true;
                q[++ tt] = new Pair<Integer, Integer>(tx, ty);

            }
        }

        return res;
    }

    public int closedIsland(int[][] grid) {
        int res = 0, n = grid.length, m = grid[0].length;
        for(int i = 0; i < n; i ++)
            for(int j = 0; j < m; j ++ )
                if(grid[i][j] != 1 && !st[i][j] && floodfill(grid, i, j)) res ++;

        return res;
    }
}
```

#### （2）[面试题 16.19. 水域大小](https://leetcode.cn/problems/pond-sizes-lcci/) 

**问题**：

你有一个用于表示一片土地的整数矩阵`land`，该矩阵中每个点的值代表对应地点的海拔高度。若值为0则表示水域。

由垂直、水平或对角连接的水域为池塘。**池塘的大小是指相连接的水域的个数**。

编写一个方法来**计算矩阵中所有池塘的大小，返回值需要从小到大排序**。

例子：

```
输入：
[
  [0,2,1,0],
  [0,1,0,1],
  [1,1,0,1],
  [0,1,0,1]
]
输出： [1,2,4]
```

**数据范围**：

- `0 < len(land) <= 1000`
- `0 < len(land[i]) <= 1000`

**思路**：FloodFill 模板题

```java
class Solution {

    boolean[][] st = new boolean[1000][1000];

    public int floodfill(int[][] g, int s, int e) {
        int n = g.length, m = g[0].length;

        Pair<Integer, Integer>[] q = new Pair[n * m];
        int hh = 0, tt = 0;
        q[0] = new Pair<Integer, Integer>(s, e);
        st[s][e] = true;
    
        int[] dx = new int[]{0, 1, 1, 1, 0, -1, -1, -1}, dy = new int[]{1, 1, 0, -1, -1, -1, 0, 1};
        while(hh <= tt) {
            var t = q[hh ++ ];

            for(int i = 0; i < dx.length; i ++ ) {
                int tx = t.getKey() + dx[i], ty = t.getValue() + dy[i];
                if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue;
                if(g[tx][ty] != 0) continue;
                if(st[tx][ty]) continue;

                st[tx][ty] = true;
                q[++ tt] = new Pair<Integer, Integer>(tx, ty);
            }
        }
        return tt + 1;
    }

    public int[] pondSizes(int[][] land) {
        int n = land.length, m = land[0].length;

        List<Integer> arr = new ArrayList<>();
        for(int i = 0; i < n; i ++ )
            for(int j = 0; j < m; j ++)
                if(land[i][j] == 0 && !st[i][j] )
                    arr.add(floodfill(land, i, j));

        Collections.sort(arr);
        int[] res = new int[arr.size()];
        for(int i = 0; i < arr.size(); i ++ )
            res[i] = arr.get(i);
        return res;
    }
}
```

#### （3）200. 岛屿数量

**问题**：

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

例子：

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**数据范围**：

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` 的值为 `'0'` 或 `'1'`

**思路**：**求连通块的数量**，**FloodFill**

```python
from collections import deque

class Solution:

    def __init__(self):
        self.st = [] # 状态数组
        self.g = [] # 存储地图
        self.n, self.m = 0, 0 # 地图的行数和列数

    def bfs(self, x, y): # FloodFill
        q = deque([(x, y)]) # 用列表初始化一个队列, 队头元素入队列
        self.st[x][y] = True
       
        dx, dy = [0, 1, 0, -1], [1, 0, -1, 0]
        while q:
            t = q.popleft() # 队头元素出队列

            for i in range(4): # 遍历上下左右四个方向
                tx, ty = t[0] + dx[i], t[1] + dy[i]
                if tx < 0 or tx >= self.m or ty < 0 or ty >= self.n:
                    continue
                if self.st[tx][ty] or self.g[tx][ty] == "0":
                    continue
                q.append((tx, ty))               
                self.st[tx][ty] = True
        
    def numIslands(self, grid: List[List[str]]) -> int:
        self.m, self.n = len(grid), len(grid[0])
        self.g = grid
        self.st = [[False for j in range(self.n) ] for i in range(self.m)] # 初始化二维数组

        res = 0 # 记录岛屿的数量
        for i in range(self.m):
            for j in range(self.n):
                if (not self.st[i][j]) and self.g[i][j] == "1":
                    self.bfs(i, j)
                    res += 1 
        return res
```

#### （4）694. 不同岛屿的数量

**问题**：

给定一个非空 01 二维数组表示的网格，一个岛屿由四连通（上、下、左、右四个方向）的 `1` 组成，你可以认为网格的四周被海水包围。

请你计算这个网格中共有**多少个形状不同的岛屿**。两个岛屿被认为是相同的，当且仅当一个岛屿可以通过**平移变换**（不可以旋转、翻转）和另一个岛屿重合。

**示例**：

```python
11011
10000
00001
11011
```

给定上图，返回结果 `3` 。

**思路**：**FloodFill**和**HashSet**

- 每次`FloodFill`搜索一个岛屿时，将每个坐标点相对于**初始坐标点(x, y)**的**相对坐标**记录下来
- 如果两个岛屿的相对作坐标相同，则这两个岛屿是形状相同的岛屿
- 使用 `HashSet` 去重，最后`set`集合的大小就是不同形状的岛屿数量

```python
from collections import deque

class Solution:

    def __init__(self):
        self.st = [] # 判重数组
        self.g = [] # 地图
        self.m, self.n = 0, 0
        self.land = set()

    def bfs(self, x, y): # FloodFill
        q = deque([(x, y)]) # 队列
        self.st[x][y] = True

        res = [] # 存储岛屿所有坐标点与(x,y)的相对位置
        dx, dy = [0, 1, 0, -1], [1, 0, -1, 0] # 偏移量
        while q:
            t = q.popleft() # 队头元素出队列
            res.append((t[0] - x, t[1] - y))

            for i in range(4): # 遍历上下左右四个方向
                tx, ty = t[0] + dx[i], t[1] + dy[i]
                if tx < 0 or tx >= self.m or ty < 0 or ty >= self.n:
                    continue
                if self.st[tx][ty] or self.g[tx][ty] == 0:
                    continue
                q.append((tx, ty))
                self.st[tx][ty] = True

        self.land.add(tuple(res)) # 列表是可变序列不能用作哈希, 将列表转化为元组

    def numDistinctIslands(self, grid: List[List[int]]) -> int:
        self.m, self.n = len(grid), len(grid[0])
        self.g = grid
        self.st = [[False for j in range(self.n)] for i in range(self.m)]

        for i in range(self.m):
            for j in range(self.n):
                if (not self.st[i][j]) and self.g[i][j]:
                    self.bfs(i, j)

        return len(self.land)
```

#### （5）302. 包含全部黑色像素的最小矩形

**问题**：

图片在计算机处理中往往是使用二维矩阵来表示的。

给你一个大小为 `m x n` 的二进制矩阵 `image` 表示一张黑白图片，`0` 代表白色像素，`1` 代表黑色像素。

黑色像素相互连接，也就是说，图片中只会有一片连在一块儿的黑色像素。像素点是水平或竖直方向连接的。

给你两个整数 `x` 和 `y` 表示某一个黑色像素的位置，请你找出包含全部黑色像素的最小矩形（与坐标轴对齐），并返回该矩形的面积。

你必须设计并实现一个时间复杂度低于 `O(mn)` 的算法来解决此问题。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203032142399.jpeg" alt="img" width="240" />

**思路**：**FloodFill**

```python
from collections import deque

class Solution:

    def __init__(self):
        self.st = [] # 判重数组
        self.g = [] # 图
        self.m, self.n = 0, 0

    def bfs(self, x, y): # FloodFill, 返回包含全部黑色像素的最小矩形的面积
        q = deque([(x, y)]) # 初始化队列
        self.st[x][y] = True

        x1, x2, y1, y2 = 2e9, -2e9, 2e9, -2e9 # 最小矩形的最小x, 最大x, 最小y, 最大y
        dx, dy = [0, 1, 0, -1], [1, 0, -1, 0] # 偏移量
        while q:
            t = q.popleft()
            x1, x2, y1, y2 = min(x1, t[0]), max(x2, t[0]), min(y1,t[1]), max(y2, t[1])

            for i in range(4):
                tx, ty = t[0] + dx[i], t[1] + dy[i]
                if tx < 0 or tx >= self.m or ty < 0 or ty >= self.n:
                    continue
                if self.st[tx][ty] or self.g[tx][ty] == "0":
                    continue
                q.append((tx, ty))
                self.st[tx][ty] = True

        return (x2 - x1 + 1) * (y2 - y1 + 1) # 长X宽

    def minArea(self, image: List[List[str]], x: int, y: int) -> int:
        self.m, self.n = len(image), len(image[0])
        self.st = [[False for j in range(self.n)] for i in range(self.m)] # 初始化判重数组
        self.g = image

        return self.bfs(x, y)
```



### 9、拓扑排序

#### （1）5300. 有向无环图中一个节点的所有祖先

**问题**：

给你一个正整数 `n` ，它表示一个 **有向无环图** 中节点的数目，节点编号为 `0` 到 `n - 1` （包括两者）。

给你一个二维整数数组 `edges` ，其中 `edges[i] = [fromi, toi]` 表示图中一条从 `fromi` 到 `toi` 的单向边。

请你返回一个数组 `answer`，其中 `answer[i]`是第 `i` 个节点的所有 **祖先** ，这些祖先节点 **升序** 排序。

如果 `u` 通过一系列边，能够到达 `v` ，那么我们称节点 `u` 是节点 `v` 的 **祖先** 节点。

<img src="https://assets.leetcode.com/uploads/2019/12/12/e1.png" alt="img" width="270px" />

**思路**：拓扑排序

- 基于 **拓扑排序** 从祖先节点出发依次遍历后代节点
- 当前节点可直接利用父节点的祖先信息，减少重复计算

```java
class Solution {
    
    int[] h, e, ne; // 邻接表
    int idx, n, m; 
    int[] q, d; // q 是队列, d[i]表示结点i的入队
    
    public void add(int a, int b){ // 添加a->b的一条有向边
        e[idx] = b; ne[idx] = h[a]; h[a] = idx++;
    }
    
    public List<List<Integer>> topsort(){
        Map<Integer, Set<Integer>> hash = new HashMap<>(); // 记录结点key的所有祖宗结点为value
        
        int hh = 0, tt = -1; // 队列初始化    
        /* 找到所有度为 0 的结点入队列 */
        for(int i = 0; i < n; i ++ )
            if(d[i] == 0){
                q[++ tt] = i;
                hash.put(i, new TreeSet<Integer>()); // 初始化hash
            }
            
        while(hh <= tt){
            int t = q[hh ++]; // 队头元素出队列      
            Set<Integer> tp = hash.get(t);
            
            for(int i = h[t]; i != -1; i = ne[i]){ // 遍历 t 的所有出边
                int j = e[i];
                d[j] --; // 删除从 t->j 的边
                
                Set<Integer> tmp = hash.getOrDefault(j, new TreeSet<Integer>());
                tmp.add(t);
                for(Integer val : tp) tmp.add(val);
                hash.put(j, tmp); // 更新j的祖宗结点, 包括t及其t的祖宗结点
                
                if(d[j] == 0) q[++ tt] = j; // j 之前的结点已经放好位置了
            }
        }   
        
        List<List<Integer>> res = new ArrayList<>();
        for(int i = 0; i < n; i ++)
            res.add(new ArrayList<>(hash.get(i)));
        return res;
    }
    
    public List<List<Integer>> getAncestors(int n, int[][] edges) {
        this.n = n; m = edges.length;
        h = new int[n + 10]; e = new int[m + 10]; ne = new int[m + 10];
        q = new int[n + 10]; d = new int[n + 10];
        
        Arrays.fill(h, -1); // 初始化头指针数组
        
        for(int i = 0; i < m; i ++) {
            int a = edges[i][0], b = edges[i][1];
            add(a, b);
            d[b] ++ ;
        }
        
        return topsort();
    }
}
```

#### （2）[207. 课程表](https://leetcode.cn/problems/course-schedule/)

**问题**

你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi]` ，表示如果要学习课程 `ai` 则 **必须** 先学习课程 `bi` 。

- 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。

**请你判断是否可能完成所有课程的学习？**如果可以，返回 `true` ；否则，返回 `false` 。

示例

```
输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：false
解释：总共有 2 门课程。学习课程 1 之前，你需要先完成课程 0 ；并且学习课程 0 之前，你还应先完成课程 1 。这是不可能的。
```

**数据范围**

- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= 5000`
- `prerequisites[i].length == 2`
- `0 <= ai, bi < numCourses`
- `prerequisites[i]` 中的所有课程对 **互不相同**

**思路** 判断是否存在一个合理的拓扑序列

```java
class Solution {

    final int N = 5010;
    int[] h = new int[N], e = new int[N], ne = new int[N]; // 邻接表
    int[] d = new int[N], q = new int[N];
    int idx = 0;

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++;
    }

    public boolean topsort(int n) {
        int hh = 0, tt = -1; // 初始化队头指针和队尾指针

        for(int i = 0; i < n; i ++ ) // 寻找入度为0的点
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++]; // 队头元素出队列

            // 遍历t的所有出边
            for(int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j] --; // 删除从t->j的边
                if(d[j] == 0) q[++ tt] = j;
            }
        }
        return tt == n - 1;
    }

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int n = numCourses;
        Arrays.fill(h, -1);

        for(int i = 0; i < prerequisites.length; i ++) {
            int a = prerequisites[i][0], b = prerequisites[i][1];
            add(b, a); // 要修课程a, 必修先修课程b，增加一条从b指向a的边
            d[a] ++; // a节点的入度加1
        }

        return topsort(n);
    }
}
```

#### （3）[210. 课程表 II](https://leetcode.cn/problems/course-schedule-ii/)

**问题**

现在你总共有 `numCourses` 门课需要选，记为 `0` 到 `numCourses - 1`。给你一个数组 `prerequisites` ，其中 `prerequisites[i] = [ai, bi]` ，表示在选修课程 `ai` 前 **必须** 先选修 `bi` 。

-  例如，想要学习课程 `0` ，你需要先完成课程 `1` ，我们用一个匹配来表示：`[0,1]` 。

返回你为了学完所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回 **任意一种** 就可以了。如果不可能完成所有课程，返回 **一个空数组** 。

示例

```
输入：numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
输出：[0,2,1,3]
解释：总共有 4 门课程。要学习课程 3，你应该先完成课程 1 和课程 2。并且课程 1 和课程 2 都应该排在课程 0 之后。
因此，一个正确的课程顺序是 [0,1,2,3] 。另一个正确的排序是 [0,2,1,3] 。
```

**数据范围**

- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
- `prerequisites[i].length == 2`
- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
- `prerequisites[i].length == 2`

**思路**：求出任意一个可行的拓扑序列

```java
class Solution {

    int[] h, e, ne; // 邻接表
    int idx = 0;
    int[] q, d;

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public int[] topsort(int n) {
        int hh = 0, tt = -1;

        for(int i = 0; i < n; i ++) 
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++];
            // 遍历t的所有出边
            for(int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j] -- ; // 删除从t->j的边
                if(d[j] == 0) q[++ tt] = j;
            }
        }
        if(tt != n - 1) return new int[0];
        return q;
    }

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int n = numCourses;

        h = new int[n + 10];
        e = new int[n * n  +10];
        ne = new int[n * n + 10];

        q = new int[n];
        d = new int[n];

        Arrays.fill(h, -1);

        for(int i = 0; i < prerequisites.length; i ++) {
            int a = prerequisites[i][0], b = prerequisites[i][1];
            add(b, a); // 要修课程a, 必修先修课程b，增加一条从b指向a的边
            d[a] ++ ; // a节点的入度加1
        }

        return topsort(n);
    }
}
```

#### （4）课程表 III



#### （5）[1462. 课程表 IV](https://leetcode.cn/problems/course-schedule-iv/)

**问题**

你总共需要上 `numCourses` 门课，课程编号依次为 `0` 到 `numCourses-1` 。你会得到一个数组 `prerequisite` ，其中 `prerequisites[i] = [ai, bi]` 表示如果你想选 `bi` 课程，你 **必须** 先选 `ai` 课程。

- 有的课会有直接的先修课程，比如如果想上课程 `1` ，你必须先上课程 `0` ，那么会以 `[0,1]` 数对的形式给出先修课程数对。

先决条件也可以是 **间接** 的。如果课程 `a` 是课程 `b` 的先决条件，课程 `b` 是课程 `c` 的先决条件，那么课程 `a` 就是课程 `c` 的先决条件。

你也得到一个数组 `queries` ，其中 `queries[j] = [uj, vj]`。对于第 `j` 个查询，您应该回答课程 `uj` 是否是课程 `vj` 的先决条件。

返回一个布尔数组 `answer` ，其中 `answer[j]` 是第 `j` 个查询的答案。

示例

<img src="https://assets.leetcode.com/uploads/2021/05/01/courses4-3-graph.jpg" alt="img" style="zoom:50%;" />

```
输入：numCourses = 3, prerequisites = [[1,2],[1,0],[2,0]], queries = [[1,0],[1,2]]
输出：[true,true]
```

**数据范围**

- `2 <= numCourses <= 100`
- `0 <= prerequisites.length <= (numCourses * (numCourses - 1) / 2)`
- `prerequisites[i].length == 2`
- `0 <= ai, bi <= n - 1`
- `ai != bi`
- 每一对 `[ai, bi]` 都 **不同**
- 先修课程图中没有环。
- `1 <= queries.length <= 104`
- `0 <= ui, vi <= n - 1`
- `ui != vi`

**思路**：拓扑排序

```java
class Solution {
    
    boolean[][] condition;

    int[] h, ne, e;
    int idx = 0;
    int[] d, q;

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public void topsort(int n) {
        int hh = 0, tt = -1;

        for(int i = 0; i < n; i ++) 
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++];
            // 遍历t的所有出边
            for(int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j] -- ; // 删除从t->j的边

                condition[t][j] = true;
                for(int k = 0; k < n; k ++)
                    condition[k][j] = (condition[k][j] | condition[k][t]);
                    
                if(d[j] == 0) q[++ tt] = j;
            }
        }
    }

    public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
        int n = numCourses;
        condition = new boolean[n][n];

        h = new int[n];
        ne = new int[n * n];
        e = new int[n * n];

        d = new int[n];
        q = new int[n + 10];

        Arrays.fill(h, -1);

        for(int i = 0; i < prerequisites.length; i ++ ) {
            int a = prerequisites[i][0], b = prerequisites[i][1];
            add(a, b);
            d[b] ++ ;
        }

        topsort(n);

        List<Boolean> res = new ArrayList<>();
        for(int i = 0; i < queries.length; i ++) {
            int a = queries[i][0], b = queries[i][1];
            res.add(condition[a][b]);
        }
        return res;
    }
}
```



### 10、归并

#### （1）4. 寻找两个正序数组的中位数

**问题**：

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

 **思路**：**二路归并**

```python
class Solution:
    
    def merge(self, a, b): # 将两个正序数组进行归并
        res = [] # 存储a与b归并后的结果
        
        i, j = 0, 0 # i是指向a数组的指针，j指向b数组的指针
        while i < len(a) and j < len(b): # 二路归并
            if a[i] < b[j]:
                res.append(a[i])
                i += 1
            else:
                res.append(b[j])
                j += 1
        while i < len(a):
            res.append(a[i])
            i += 1
        while j < len(b):
            res.append(b[j])
            j += 1
        
        mid = 0 + len(res) - 1 >> 1 # 计算中点
        if len(res) % 2 == 0:
            return (res[mid] + res[mid + 1]) / 2
        else:
            return res[mid]

    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        return self.merge(nums1, nums2)
```

#### （2）21. 合并两个有序链表

**问题**：

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**思路**：二路归并

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        res = ListNode()
        i, j, k = list1, list2, res # i指向链表1, j指向链表2, res指向记录结果的指针

        while i and j: # 二路归并
            if i.val <= j.val:
                k.next, i = i, i.next
            else:
                k.next, j = j, j.next
            k = k.next 
        
        if i:
            k.next = i
        if j:
            k.next = j
        return res.next
```

#### （3）23. 合并K个升序链表

**问题**：

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**思路**：**k 路归并**(转化为分治和二路归并)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def merge(self, q, l, r):
        if l == r: # [l,r]区间只有一个元素时,则递归结束
            return q[r]
        
        mid = l + r >> 1 # mid是区间[l,r]的中点
        i, j = self.merge(q, l, mid), self.merge(q, mid + 1, r) # 分治

        k = res = ListNode()
        while i and j: # 二路归并
            if i.val <= j.val:
                k.next, i = i, i.next
            else:
                k.next, j = j, j.next
            k = k.next
        if i:
            k.next = i
        if j:
            k.next = j
        return res.next    

    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists: # 特判
            return None
        return self.merge(lists, 0, len(lists) - 1) # k路归并
```



### 11、思维题

#### （1）[2481. 分割圆的最少切割次数](https://leetcode.cn/problems/minimum-cuts-to-divide-a-circle/)

**问题**：

圆内一个 **有效切割** ，符合以下二者之一：

- 该切割是两个端点在圆上的线段，且该线段经过圆心。
- 该切割是一端在圆心另一端在圆上的线段。

一些有效和无效的切割如下图所示。

<img src="https://assets.leetcode.com/uploads/2022/10/29/alldrawio.png" alt="img" style="zoom:80%;" />

给你一个整数 `n` ，请你返回将圆切割成相等的 `n` 等分的 **最少** 切割次数。

例子：

<img src="https://assets.leetcode.com/uploads/2022/10/24/11drawio.png" alt="img" style="zoom: 60%;" />



```
输入：n = 4
输出：2
解释：
上图展示了切割圆 2 次，得到四等分。
```

<img src="https://assets.leetcode.com/uploads/2022/10/24/22drawio.png" alt="img" style="zoom:60%;" />

```
输入：n = 3
输出：3
解释：
最少需要切割 3 次，将圆切成三等分。
少于 3 次切割无法将圆切成大小相等面积相同的 3 等分。
同时可以观察到，第一次切割无法将圆切割开。
```

**数据范围**：

- `1 <= n <= 100`

**思路**：找规律

n:  1 2 3 4 5 6 7 8

cut:0 1 3 2 5 3 7 4

```java
class Solution {
    public int numberOfCuts(int n) {
        if(n == 1) return 0;
        if((n & 1) == 1) return n;
        else return n / 2;
    }
}
```



#### （2）[1675. 数组的最小偏移量](https://leetcode.cn/problems/minimize-deviation-in-array/) 

**问题**：

给你一个由 `n` 个正整数组成的数组 `nums` 。

你可以对数组的任意元素执行任意次数的两类操作：

- 如果元素是 **偶数** ，**除以**2
  - 例如，如果数组是 `[1,2,3,4]` ，那么你可以对最后一个元素执行此操作，使其变成 `[1,2,3,2]`
- 如果元素是 **奇数** ，**乘上**2
  - 例如，如果数组是 `[1,2,3,4]` ，那么你可以对第一个元素执行此操作，使其变成 `[**2**,2,3,4]`

数组的 **偏移量** 是数组中任意两个元素之间的 **最大差值** 。

返回数组在执行某些操作之后可以拥有的 **最小偏移量** 。

例子：

```
输入：nums = [1,2,3,4]
输出：1
解释：你可以将数组转换为 [1,2,3,2]，然后转换成 [2,2,3,2]，偏移量是 3 - 2 = 1
```

**数据范围**：

- `n == nums.length`
- `2 <= n <= 5 * 104`
- `1 <= nums[i] <= 109`

**思路**：

一经思考，发现要使偏移最小，就要不停的使最小值变大，最大值变小，要么找到最小奇数×2，要么找到最大偶数➗2，但是这样做不仅代码十分复杂，而且超时的离谱。

思考：既然做不到两个任务，是否可以进行恰当的转换，观察题目既有×2又有÷2，是否可以统一来做？发现偶数除以2可能即时奇数又是偶数，而奇数×2 = 偶数，而偶数是可以÷2得到它原来的数的！所以将所有奇数×2并不会影响最终的结果，而将问题转化为全偶问题，问题由此而解。

于是不停循环将最大值除以2，用最大值减去维护的最小值，中止条件是当最大值为奇数时，此时最大值无法缩小，考虑最小值(奇数的话)是否可以扩大，思考发现，一开始全是偶数，如果最小值是奇数，那么它一定是由上一个偶数除以2变过来的，我们在上一个状态已经计算过这个偶数了，因此没必要扩大他，直接返回结果

```java
class Solution {
    public int minimumDeviation(int[] nums) {
        PriorityQueue<Integer> heap = new PriorityQueue<>((a, b)->-a.compareTo(b));
        int minmium = Integer.MAX_VALUE;
        for(int i = 0; i < nums.length; i ++ ) {
            if(nums[i] % 2 == 1) nums[i] *= 2;
            if(nums[i] < minmium ) minmium = nums[i];
            heap.offer(nums[i]);
        }
        
        int res = Integer.MAX_VALUE;
        while(heap.peek() % 2 == 0) {
            res = Math.min(res, heap.peek() - minmium);
            int head = heap.poll();
            head = head / 2;
            if(head < minmium) minmium = head;
            heap.offer(head);
        }
        res = Math.min(res, heap.peek() - minmium);
        return res;
    }
}
```

#### （3）[1375. 二进制字符串前缀一致的次数](https://leetcode.cn/problems/number-of-times-binary-string-is-prefix-aligned/)

**问题**：

给你一个长度为 `n` 、下标从 **1** 开始的二进制字符串，所有位最开始都是 `0` 。我们会按步翻转该二进制字符串的所有位（即，将 `0` 变为 `1`）。

给你一个下标从 **1** 开始的整数数组 `flips` ，其中 `flips[i]` 表示对应下标 `i` 的位将会在第 `i` 步翻转。

二进制字符串 **前缀一致** 需满足：在第 `i` 步之后，在 **闭** 区间 `[1, i]` 内的所有位都是 1 ，而其他位都是 0 。

返回二进制字符串在翻转过程中 **前缀一致** 的次数。

例子：

```
输入：flips = [3,2,4,1,5]
输出：2
解释：二进制字符串最开始是 "00000" 。
执行第 1 步：字符串变为 "00100" ，不属于前缀一致的情况。
执行第 2 步：字符串变为 "01100" ，不属于前缀一致的情况。
执行第 3 步：字符串变为 "01110" ，不属于前缀一致的情况。
执行第 4 步：字符串变为 "11110" ，属于前缀一致的情况。
执行第 5 步：字符串变为 "11111" ，属于前缀一致的情况。
在翻转过程中，前缀一致的次数为 2 ，所以返回 2 。
```

**数据范围**：

- `n == flips.length`
- `1 <= n <= 5 * 10^4`
- `flips` 是范围 `[1, n]` 中所有整数构成的一个排列

**思路**：

翻转最大位置remote应该等于当前的位置i时，才满足在 闭区间 [1, i] 内的所有位都是 1 ，而其他位都是 0。

否则，翻转最大值位置remote大于当前位置i时，意味着当前位置i之前还有比特位没有被翻转

```java
class Solution {
    public int numTimesAllBlue(int[] flips) {
        int cnt  = 0, remote = 0;
        for(int i = 0; i < flips.length; i ++ ) {
            remote = Math.max(remote, flips[i]);
            if(i + 1 == remote) cnt ++;
        }
        return cnt;
    }
}
```

#### （4）899. 有序队列:writing_hand: 

**问题**

给定一个字符串 $s$ 和一个整数 $k$ 。你可以从 $s$ 的前 $k$ 个字母中选择一个，并把它加到字符串的末尾。

返回 在应用上述步骤的任意数量的移动后，字典上最小的字符串 。

**思路** 

首先考虑当 $k = 1$ 的情况：我们只能将字符串第一个元素放到字符串最后，也就是轮转整个字符串，取字典序最小即可；

当 $k = 2$ 时:

- 我们将字符串第一个位置 $s[0]$ 拎出来;
- 剩下的所有位置看作一个轮盘，通过不断将字符串第二个位置 $s[1]$ 放到字符串末尾，即可实现将轮盘转动起来；
- 那么可以在适当的时候将 $s[0]$ 插入轮盘，也可以在适当的时候将 $s[1]$ 拎出来放到位置 $s[0]$ 处，通过这么考虑我们即可实现对 $s$ 字符串的排序。

*即当 $k = 2$ 时的操作，**类似冒泡排序**，不断取前两个数中较小的那个数放到最后，每走一圈都会将一个剩余最大值移到最后，最后可以将整个字符序列从小到大排序*

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208081725226.jpeg" alt="899.有序队列.jpg" width="600px" />

当 $k > 2$ 时同样可以看作  $k = 2$ 的情况实现对字符串 `s` 的排序

**代码**

```java
class Solution {
    public String orderlyQueue(String s, int k) {
        String res = s;
        if(k == 1) { // 每次将第一个字符移到字符串的末尾，找到字典序最小的字符
            for(int i = 0; i < s.length(); i ++ ) {
                s = s.substring(1, s.length()) +  s.charAt(0);                
                res = s.compareTo(res) < 0 ? s : res;
            }
        } 
        else {
            char[] chs = s.toCharArray(); 
            Arrays.sort(chs); // 所有字符按字典序从小到大排序
            res = new String(chs);
        }
        return res;
    }
}
```

### 12、前后缀分解

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
-  `n` 个元素的平均值是 `n` 个元素之 **和** 除以（整数除法） `n` 。
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
