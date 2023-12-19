# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>



> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之快速幂

#### 1、矩阵快速幂优化DP

##### （1）[1220. 统计元音字母序列的数目](https://leetcode.cn/problems/count-vowels-permutation/)

**问题**

给你一个整数 `n`，请你帮忙统计一下我们可以按下述规则形成多少个长度为 `n` 的字符串：

- 字符串中的每个字符都应当是小写元音字母（`'a'`, `'e'`, `'i'`, `'o'`, `'u'`）
- 每个元音 `'a'` 后面都只能跟着 `'e'`
- 每个元音 `'e'` 后面只能跟着 `'a'` 或者是 `'i'`
- 每个元音 `'i'` 后面 **不能** 再跟着另一个 `'i'`
- 每个元音 `'o'` 后面只能跟着 `'i'` 或者是 `'u'`
- 每个元音 `'u'` 后面只能跟着 `'a'`

由于答案可能会很大，所以请你返回 模 `10^9 + 7` 之后的结果。

**数据范围**：

- `1 <= n <= 2 * 10^4`

**思路**

方案一：线性DP $O(5n)$

```java
class Solution {
    public int countVowelPermutation(int n) {
        int mod = (int)1e9 + 7; 
        long[][] f = new long[n + 1][6];
        for(int i = 1; i <= 5; i ++)
            f[1][i] = 1;

        for(int i = 2; i <= n; i ++) {
            f[i][1] = (f[i - 1][2] + f[i - 1][3] + f[i - 1][5]) % mod; // a -> e, u
            f[i][2] = (f[i - 1][1] + f[i - 1][3]) % mod; // e -> a, i
            f[i][3] = (f[i - 1][2] + f[i - 1][4]) % mod; // i -> e, o
            f[i][4] = f[i - 1][3] % mod; // o -> i
            f[i][5] = (f[i - 1][3] + f[i - 1][4]) % mod; // u -> i, o
        }

        long res = 0;
        for(int i = 1; i <= 5; i ++)
            res = (res + f[n][i]) % mod;
        return (int)res;
    }
}
```

方案二：矩阵快速幂优化DP $O(5^3logn)$

以上的方法适用于 $n$ 比较小的情况，在 $n$ 变大之后，$O(n)$ 的时间复杂度会让这个算法看起来有些捉襟见肘。我们可以用**「矩阵快速幂」**的方法来优化这个过程。

首先我们可以构建这样一个递推关系：
$$
\left[\begin{array}{c}
f(i,1) \\
f(i,2)\\
f(i,3)\\
f(i,4)\\
f(i,5)
\end{array}\right]
=
\left[\begin{array}{lll}
0 & 1 & 1 & 0 & 1 \\
1 & 0 & 1 & 0 & 0 \\
0 & 1 & 0 & 1 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 1 & 1 & 0
\end{array}\right]\left[\begin{array}{c}
f(i-1,1) \\
f(i-1,2) \\
f(i-1,3)\\
f(i-1,4)\\
f(i-1,5)
\end{array}\right]
$$
因此
$$
\left[\begin{array}{c}
f(n,1) \\
f(n,2)\\
f(n,3)\\
f(n,4)\\
f(n,5)
\end{array}\right]
=
\left[\begin{array}{lll}
0 & 1 & 1 & 0 & 1 \\
1 & 0 & 1 & 0 & 0 \\
0 & 1 & 0 & 1 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 1 & 1 & 0
\end{array}\right]^{n-1}\left[\begin{array}{c}
f(1,1) \\
f(1,2) \\
f(1,3)\\
f(1,4)\\
f(1,5)
\end{array}\right]
$$
令：
$$
M=
\left[\begin{array}{lll}
0 & 1 & 1 & 0 & 1 \\
1 & 0 & 1 & 0 & 0 \\
0 & 1 & 0 & 1 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 1 & 1 & 0
\end{array}\right]
$$
因此我们只要能快速计算矩阵 $M$ 的 $n$ 次幂，就可以得到 $f(n)$ 的值。如果直接求取 $M^n$ ，时间复杂度是 $O(n)$ 的，我们可以**定义矩阵乘法**，然后用**快速幂算法**来加速这里 $M^n$ 的求取。

```java
class Solution {

    final int mod = (int)1e9 + 7; 

    public void mul(long[][] C, long[][] A, long[][] B) { // C = A * B
        int N = C.length;
        long[][] t = new long[N][N];

        for(int i = 0; i < N; i ++ )
        	for(int j = 0; j < N; j ++ )
                for(int k = 0; k < N; k ++ )
                    t[i][j] = (t[i][j] + (A[i][k] * B[k][j]) % mod) % mod;

        for(int i = 0; i < N; i ++)
            for(int j = 0; j < N; j ++)
                C[i][j] = t[i][j];
    }

    public void qmi(long[][] f, long[][] a, int k) {
        while(k != 0) {
            if((k & 1) != 0) mul(f, a, f);
            k >>= 1;
            mul(a, a, a);
        }
    }

    public int countVowelPermutation(int n) {
        
        long[][] f = new long[][]{
            {1, 1, 1, 1, 1},
            {1, 1, 1, 1, 1},
            {1, 1, 1, 1, 1},
            {1, 1, 1, 1, 1},
            {1, 1, 1, 1, 1},
        };

        long[][] a = new long[][]{
            {0, 1, 1, 0, 1},
            {1, 0, 1, 0, 0},
            {0, 1, 0, 1, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 1, 1, 0},
        };

        qmi(f, a, n - 1);

        long res = 0;
        for(int i = 0; i < 5; i ++)
            res = (res + f[i][0]) % mod;
        return (int)res;
    }
}
```



##### （2）[790. 多米诺和托米诺平铺](https://leetcode.cn/problems/domino-and-tromino-tiling/)

**问题**

有两种形状的瓷砖：一种是 `2 x 1` 的多米诺形，另一种是形如 "L" 的托米诺形。两种形状都可以旋转。

<img src="https://assets.leetcode.com/uploads/2021/07/15/lc-domino.jpg" alt="img" style="zoom:75%;" />

给定整数 n ，返回可以平铺 `2 x n` 的面板的方法的数量。**返回对** `109 + 7` **取模** 的值。

平铺指的是每个正方形都必须有瓷砖覆盖。两个平铺不同，当且仅当面板上有四个方向上的相邻单元中的两个，使得恰好有一个平铺有一个瓷砖占据两个正方形。

例子

<img src="https://assets.leetcode.com/uploads/2021/07/15/lc-domino1.jpg" alt="img" style="zoom:67%;" />

```
输入: n = 3
输出: 5
解释: 五种不同的方法如上所示。
```

数据范围

- `1 <= n <= 1000`

思路

方案一：线性DP

考虑这么一种平铺的方式：在第 $i$ 列前面的正方形都被瓷砖覆盖，在第 $i$ 列后面的正方形都没有被瓷砖覆盖（$i$ 从 $1$ 开始计数）。那么第 $i$ 列的正方形有四种被覆盖的情况：

- 一个正方形都没有被覆盖，记为状态 0；
- 只有上方的正方形被覆盖，记为状态 1；
- 只有下方的正方形被覆盖，记为状态 2；
- 上下两个正方形都被覆盖，记为状态 3。

使用 $dp[i][j]$ 表示平铺到第 $i$ 列时，各个状态 $s$ 对应的平铺方法数量。考虑第 $i−1$ 列和第 $i$ 列正方形，它们之间的状态转移如下图（红色条表示新铺的瓷砖）：

![fig1](https://assets.leetcode-cn.com/solution-static/790/1.png)



初始时 $dp[1][0]=dp[1][3]=1,dp[1][1]=dp[1][2]=0$ 为：

$$
\begin{aligned}
& d p[i][0]=d p[i-1][3] \\
& d p[i][1]=d p[i-1][0]+d p[i-1][2] \\
& d p[i][2]=d p[i-1][0]+d p[i-1][1] \\
& d p[i][3]=d p[i-1][0]+d p[i-1][1]+d p[i-1][2]+d p[i-1][3]
\end{aligned}
$$



最后平铺到第 $n$ 列时，上下两个正方形都被覆盖的状态 $dp[n][3]$ 对应的平铺方法数量就是总平铺方法数量。

```java
class Solution {

    final int mod = (int)1e9 + 7;

    public int numTilings(int n) {
        long[][] f = new long[n + 1][4];

        f[1][0] = f[1][3] = 1;

        for(int i = 2; i <= n; i ++) {
            f[i][0] = f[i - 1][3];
            f[i][1] = (f[i - 1][0] + f[i - 1][2]) % mod;
            f[i][2] = (f[i - 1][0] + f[i - 1][1]) % mod;
            f[i][3] = (f[i - 1][0] + f[i - 1][1] + f[i - 1][2] + f[i - 1][3]) % mod;
        }

        return (int)f[n][3];
    }
}
```

方案二：矩阵快速幂优化DP

当前的多种状态，只跟前一个状态的多种状态成线性关系的，可以用矩阵来表示它的系数然后动态规划，是否一定需要利用快速幂取决于每个时刻状态数的多少以及求得时刻是多少

以上的方法适用于 $n$ 比较小的情况，在 $n$ 变大之后，$O(n)$ 的时间复杂度会让这个算法看起来有些捉襟见肘。我们可以用**「矩阵快速幂」**的方法来优化这个过程。

首先我们可以构建这样一个递推关系：
$$
\left[\begin{array}{c}
f(i,0) \\
f(i,1)\\
f(i,2)\\
f(i,3)\\
\end{array}\right]
=
\left[\begin{array}{lll}
1 & 1 & 1 & 1  \\
1 & 0 & 1 & 0  \\
1 & 1 & 0 & 0  \\
1 & 1 & 1 & 1  \\
\end{array}\right]\left[\begin{array}{c}
f(i-1,0) \\
f(i-1,1) \\
f(i-1,2)\\
f(i-1,3)\\
\end{array}\right]
$$
因此
$$
\left[\begin{array}{c}
f(n,0) \\
f(n,1)\\
f(n,2)\\
f(n,3)\\
\end{array}\right]
=
\left[\begin{array}{lll}
1 & 1 & 1 & 1  \\
1 & 0 & 1 & 0  \\
1 & 1 & 0 & 0  \\
1 & 1 & 1 & 1  \\
\end{array}\right]^{n-1}\left[\begin{array}{c}
f(1,0) \\
f(1,1) \\
f(1,2)\\
f(1,3)\\
\end{array}\right]
$$
令：
$$
M=
\left[\begin{array}{lll}
1 & 1 & 1 & 1  \\
1 & 0 & 1 & 0  \\
1 & 1 & 0 & 0  \\
1 & 1 & 1 & 1  \\
\end{array}\right]
$$
因此我们只要能快速计算矩阵 $M$ 的 $n$ 次幂，就可以得到 $f(n)$ 的值。如果直接求取 $M^n$ ，时间复杂度是 $O(n)$ 的，我们可以**定义矩阵乘法**，然后用**快速幂算法**来加速这里 $M^n$ 的求取。

```java
class Solution {

    final int mod = (int)1e9 + 7;

    public void mul(long[][] C, long[][] A, long[][] B) { // C = A * B
        int N = C.length;
        long[][] t = new long[N][N];

        for(int i = 0; i < N; i ++ )
        	for(int j = 0; j < N; j ++ )
                for(int k = 0; k < N; k ++ )
                    t[i][j] = (t[i][j] + (A[i][k] * B[k][j]) % mod) % mod;

        for(int i = 0; i < N; i ++)
            for(int j = 0; j < N; j ++)
                C[i][j] = t[i][j];
    }

    public void qmi(long[][] f, long[][] a, int k) {
        while(k != 0) {
            if((k & 1) != 0) mul(f, a, f);
            k >>= 1;
            mul(a, a, a);
        }
    }

    public int numTilings(int n) {
        long[][] a = new long[][]{
            {0, 0, 0, 1},
            {1, 0, 1, 0},
            {1, 1, 0, 0},
            {1, 1, 1, 1},
        };

        long[][] f = new long[][]{
            {1, 1, 1, 1},
            {0, 0, 0, 0},
            {0, 0, 0, 0},
            {1, 1, 1, 1}
        };

        qmi(f, a, n - 1);

        return (int)f[3][0];
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