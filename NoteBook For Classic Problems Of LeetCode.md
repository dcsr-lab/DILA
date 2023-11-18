# NoteBook For Classic Problems Of LeetCode 

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>




> 引言
>
> 此文章是为了
>
> :man:：`Laplace`	:timer_clock: ：`2021-11-07`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  



### 1、回文串问题

##### （1）最长回文子串（双指针）

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

**双指针** $O(n^2)$ 

```java
class Solution {
    public String longestPalindrome(String s) {
        // 枚举所有回文串的中心, 双指针
        String res = "";
        for(int i = 0; i < s.length(); i ++ )
        {
            for(int l = i, r = i; l >= 0 && r < s.length(); l --, r ++ ) // 枚举长度为奇数的回文串
                if(s.charAt(l) != s.charAt(r)) break;
                else res = res.length() >= (r - l + 1) ? res : s.substring(l, r + 1);
   
            for(int l = i, r = i + 1; l >= 0 && r < s.length(); l --, r ++ ) // 枚举长度为偶数的回文串
                if(s.charAt(l) != s.charAt(r)) break;
                else res = res.length() >= (r - l + 1) ? res : s.substring(l, r + 1);
        }

        return res;
    }
}
```

##### （2）回文子字符串的个数 （线性 DP）

**问题**

给定一个字符串 `s` ，请计算这个字符串中有多少个回文子字符串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

**注意** 

- `1 <= s.length <= 1000`
- `s` 由小写英文字母组成

**思路** 

枚举每一个字符串判断其是否是回文串

依据之前求过的字符串来判断当前的字串是否是回文串

**线性 DP** $O(n^2)$

```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        boolean[][] f = new boolean[n + 10][n + 10]; // 状态f[i][j]表示s(i,j)是回文字符
        char[] str = s.toCharArray();
        
        for(int i = 0; i < n; i ++) f[i][i] = true; // 初始化状态数组, 单个字符也是回文串

        int cnt = n; // 单个字符也是回文串
        for(int i = n - 1; i >= 0; i -- )
            for(int j = i + 1; j < n; j ++ ) {
                if(i + 1 > j - 1) f[i][j] = str[i] == str[j]; // 情况一: aa
                else f[i][j] = f[i + 1][j - 1] && str[i] == str[j]; // 情况二 aba

                if(f[i][j]) cnt ++ ; // s(i,j)是回文串
            }
        return cnt;
    }
}
```

**代码优化版** 

```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        boolean[][] f = new boolean[n + 10][n + 10]; // 状态f[i][j]表示s(i,j)是回文字符
        
        for(int i = 0; i < n; i ++) Arrays.fill(f[i], true); // 初始化状态数组, 单个字符也是回文串

        int cnt = n; // 单个字符也是回文串
        for(int i = n - 1; i >= 0; i -- )
            for(int j = i + 1; j < n; j ++ ) {
                f[i][j] = f[i + 1][j - 1] && s.charAt(i) == s.charAt(j); // 情况二 aba
                if(f[i][j]) cnt ++ ; // s(i,j)是回文串
            }
        return cnt;
    }
}
```

**用途** 

- 求回文串的个数
- 预处理出所有的回文串

##### （3）131. 分割回文串 I（回溯+记忆化搜索）

**问题**

给你一个字符串 `s`，请你将 `s` 分割成一些子串，使**每个子串**都是 **回文串** 。返回 `s` 所有可能的分割方案。

**回文串** 是正着读和反着读都一样的字符串。

注意

- `1 <= s.length <= 16`
- `s` 仅由小写英文字母组成

**思路** 

采用**回溯**的方式，本题的**递归树模**型是一棵**多叉树** 

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208221821153.png" alt="image.png" width="700px" />

在递归之前**预处理**出**回文串数组**

**代码**

```java
class Solution {

    final int N = 20;
    boolean[][] f = new boolean[N][N]; // 回文串数组, f[i][j]为true表示s(i~j)是回文串, 为false表示s(i~j)不是回文串
    List<List<String>> solutions = new ArrayList<>();

    public void dfs(String u, int st, List<String> ans) { // u是待分割的字符串, st是u相对于原字符串的起始位置
        if(u == null || "".equals(u)) { // 枚举到搜索树的叶子结点,找到一种分割方案, u是空字符串
            solutions.add(new ArrayList<>(ans));
            return ;
        }

        for(int i = 0; i < u.length(); i ++ ) { // 枚举字符串u的所有分割位置
            String a = null, b = null;
            // 分别截取a和b
            a = u.substring(0, i + 1);
            if(i + 1 < u.length()) b = u.substring(i + 1, u.length());
            
            if(f[st][st + i]) ans.add(a); // a是回文串
            else continue; // a不是回文串, 则进行剪枝操作

            dfs(b, st + i + 1, ans);
            ans.remove(ans.size() - 1); // 恢复现场
        }       
    }

    public List<List<String>> partition(String s) {
        int n = s.length();
        for(int i  = 0; i < n; i ++ ) Arrays.fill(f[i], true);

        for (int i = n - 1; i >= 0; i --) // 预处理回文串数组f
            for (int j = i + 1; j < n; j ++ ) 
                f[i][j] = (s.charAt(i) == s.charAt(j)) && f[i + 1][j - 1];

        dfs(s, 0, new ArrayList<>());
        return solutions;
    }
}
```

##### （4）132. 分割回文串 II（线性 DP）

**问题** 

给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是回文。

返回符合要求的 **最少分割次数** 。

注意

- `1 <= s.length <= 2000`
- `s` 仅由小写英文字母组成

**思路**

首先想到和 [分割回文串Ⅰ](https://leetcode.cn/problems/palindrome-partitioning/) 一样的暴力方法，遍历出所有分割的可能，返回最小分割即可。

但是本题不用求出具体的方案数，只需要**最小分割次数**，可以使用**动态规划**来求解子问题。

$f[i]$ 表示在区间 $[0, i)$ 内的字符串的最小分割次数，那么如果 $[j, i-1]$ 是一个回文串，则 $f[i] = min(f[i], f[j] + 1)$

初始化的时候，因为最差情况下需要将字符串全部分割成单个字符，需要分割 $n-1$ 次， 而 $f[0]$ 即空串 不需要分割，初始化$f[0] = 0$

寻找回文串的过程，可以用**二维动规**去找，也可以使用**中心扩展**法去找，只需要在找的过程中不断更新 $f[i]$ 即可。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208221844337.png" alt="image-20220822184415268" width="800px" />

**代码** $O(n^2)$ 

```java
class Solution {
    public int minCut(String s) {
        int n = s.length();
        int[] f = new int[n + 1];
        boolean [][] symmetry = new boolean[n + 1][n + 1];//为true表示s(i~j)是回文串,为false表示s(i~j)不是回文串

        for(int i = 1; i <= n; i ++ ) f[i] = i - 1; // 初始化, 前i个字符组成的字符串的最多分割次数
        for(int i  = 0; i < n; i ++ ) Arrays.fill(symmetry[i], true);

        for (int i = n - 1; i >= 0; i --) // 预处理symmetry数组
            for (int j = i + 1; j < n; j ++ ) 
                symmetry[i][j] = (s.charAt(i) == s.charAt(j)) && symmetry[i + 1][j - 1];

        for(int i = 1; i <= n; i ++ ) // 线性 DP
            for(int j = 0; j < i; j ++ ) // 枚举最后一次的分割位置
                if(symmetry[j][i - 1]) // s(j, i - 1) 是回文串
                    if(j > 0) f[i] = Math.min(f[i], f[j] + 1);
                    else f[i] = Math.min(f[i], 0); 
                    
        return f[n];    
    }
}
```



### 2、股票买卖问题

| 模型                   | 特点                                                   | 状态表示                                                     |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 买卖股票的最佳时机 I   | 买入和卖出**一次**股票                                 | $f[i]$ 表示前$i$天中所有买卖股票的方案的集合                 |
| 买卖股票的最佳时机 II  | **多次**买入卖出一只股票，在再次购买前出售掉之前的股票 | $f[i][j]$ 考虑前 $i$ 天的股市，第 $i$ 天持股状态为 $j$的所有方案的集合 |
| 买卖股票的最佳时机 III | 最多可以完成**两笔**交易                               |                                                              |



##### （1）买卖股票的最佳时机 I（线性DP）

**问题**

给定一个长度为 $N(1≤N≤10^5)$ 的数组，数组中的第 $i$ 个数字表示一个给定股票在第 $i$ 天的价格。

如果你最多**只允许完成一笔交易**（即买入和卖出一支股票），设计一个算法来计算你所能获取的**最大利润**。

注意：你不能在买入股票前卖出股票。

**思路**

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181534077.png" alt="image-20220718153409026" width="650px" />

**线性DP** $O(N)$

```java
Scanner sc = new Scanner(System.in);

final int N = 100010;
int[] f = new int[N]; // f[i] 表示前i天所有买卖股票的方案的集合, 值表示所有方案的最大利润
int[] prices = new int[N];

int minimum = 0x3f3f3f3f, n = sc.nextInt();

for(int i = 1; i <= n; i ++ )
{
    prices[i] = sc.nextInt();
    f[i] = Math.max(f[i - 1], prices[i] - minimum);
    minimum = Math.min(minimum, prices[i]);
}

System.out.println(f[n]);
```

##### （2）买卖股票的最佳时机 II（状态机DP/贪心）

**问题**

给定一个长度为 $N(1≤N≤10^5)$ 的数组，数组中的第 $i$ 个数字表示一个给定股票在第 $i$ 天的价格。

设计一个算法来计算你所能获取的**最大利润**。你可以**尽可能地完成更多的交易**（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**思路**

**状态机DP**

定义状态

用状态 $0$ 表示未持股，转态 $1$ 表示持股，状态之间的关系如下：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181538931.jpeg" alt="IMG_4E31C78D0C18-1.jpeg" width="450px" />

动态规划

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181539054.jpeg" alt="IMG_7844DAF97668-1.jpeg" width="670px" />



**状态机模型 DP** $O(N)$

```java
Scanner sc = new Scanner(System.in);

final int N = 100010, INF = 0x3f3f3f3f;
int[][] f = new int[N][2]; // f[i][j]表示考虑前i天的股票, 且第i持有股票的状态是j(j为0表示不持股, j为1表示持股)
int[] prices = new int[N];
int n = sc.nextInt();

f[0][1] = -INF; // 初始化不合法情况
for(int i = 1; i <= n; i ++ )
{
    prices[i] = sc.nextInt();
    f[i][0] = Math.max(f[i - 1][0], f[i - 1][1] + prices[i]); // max(不持股, 卖出)
    f[i][1] = Math.max(f[i - 1][1], f[i - 1][0] - prices[i]); // max(持股, 买入)
}

System.out.println(f[n][0]);
```

**贪心** $O(N)$

股票买卖策略：

- 单独交易日：设今天的股票价格是 $p_1$，明天的价格是 $p_2$，今天买入，明天卖出可以赚取的金额是 $p_2-p_1$。（负值表示亏损）
- 连续上涨交易日：设此连续上涨交易日股票价格分别为 $p_1,p_2,...,p_n$。则第一天买入，最后一天卖出的收益最大，即$p_n-p_1$。等价于每天都买入和卖出股票，即 $(p_1-p_2) + (p_2-p_3)+...+(p_{n-2}-p_{n-1})+(p_{n-1}-p_n)$。
- 连续下降交易日：不买不卖收益最大，即不会亏损。

贪心思想：遍历整个股票交易日价格，所有连续上涨交易日都进行买卖股票（尽可能的赚取利润），所有连续下降交易日都不进行买卖（能不亏钱就不要亏钱）。

- 设$temp$为第$i-1$天买入，第$i$天卖出的利润，即 $temp=prices[i-1]-prices[i]$。
- 如果 $temp>0$，即**当前交易的利润为正，则将 $temp$加到累积总的利润 $res$中**；否则，就跳过此次交易（因为这次交易会有亏损）。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-07-12%2000.21.21.png" alt="截屏2023-07-12 00.21.21" style="zoom:50%;" />

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 1; i < prices.length; i ++)
            res += Math.max(0, prices[i] - prices[i - 1]);
        return res;
    }
}
```



##### （3）买卖股票的最佳时机 III（状态机DP）

**问题** 

给定一个长度为 $N(1≤N≤10^5)$ 的数组，数组中的第 $i$ 个数字表示一个给定股票在第 $i$ 天的价格。

设计一个算法来计算你所能获取的**最大利润**。你**最多可以完成两笔交易**。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**思路** 

定义状态

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181538931.jpeg" alt="IMG_4E31C78D0C18-1.jpeg" width="450px" />

此问题是`买卖股票的最佳时机II`的一般化问题

定义**买入股票的时候算作一次交易**

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181634361.png" alt="image-20220718163431311" width="700px" />

**状态机模型 DP** $O(NM)$

```java
Scanner sc = new Scanner(System.in);
final int N = 100010, M = 3, INF = 0x3f3f3f3f;

int[][][] f = new int[N][M][2]; // f[i][j][k] 表示考虑前i天的股市, 且前i天进行了j次交易, 第i天的持股状态为k
int[] prices = new int[N];
int n = sc.nextInt();

for(int i = 1; i <= n; i ++ ) prices[i] = sc.nextInt();

for(int j = 0; j <= 2; j ++ ) f[0][j][1] = -INF; // 初始化不合法情况

for(int i = 1; i <= n; i ++ )
    for(int j = 1; j <= 2; j ++ )
    {
        f[i][j][0] = Math.max(f[i - 1][j][0], f[i - 1][j][1] + prices[i]); // max(不持股, 卖出)
        f[i][j][1] = Math.max(f[i - 1][j][1], f[i - 1][j - 1][0] - prices[i]); // max(持股, 买入)
    }
    
System.out.println(f[n][2][0]);
```

##### （4）买卖股票的最佳时机 IV（滚动数组优化+状态机DP）

**问题**

给定一个长度为 $N$ ($1≤N≤10^5$) 的数组，数组中的第 $i$ 个数字表示一个给定股票在第 $i$ 天的价格。

设计一个算法来计算你所能获取的**最大利润**，你**最多可以完成 $k$($1≤k≤100$) 笔交易**。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。一次买入卖出合为一笔交易。

**思路**

定义状态

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181538931.jpeg" alt="IMG_4E31C78D0C18-1.jpeg" width="450px" />

此问题是`买卖股票的最佳时机III`的推广

定义买入股票的时候算作一次交易

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207181634361.png" alt="image-20220718163431311" width="700px" />

**状态机模型 DP** $O(NM)$ 

```java
Scanner sc = new Scanner(System.in);
final int N = 100010, M = 110, INF = 0x3f3f3f3f;       
int[][][] f = new int[N][M][2]; // f[i][j][k] 表示考虑前i天的股市, 且前i天进行了j次交易, 第i天的持股状态为k
int[] prices = new int[N];
int n = sc.nextInt(), m = sc.nextInt();

for(int i = 1; i <= n; i ++ ) prices[i] = sc.nextInt();
for(int j = 0; j <= m; j ++ ) f[0][j][1] = -INF; // 初始化不合法情况

for(int i = 1; i <= n; i ++ )
    for(int j = 1; j <= m; j ++ )
    {
        f[i][j][0] = Math.max(f[i - 1][j][0], f[i - 1][j][1] + prices[i]); // max(不持股, 卖出)
        f[i][j][1] = Math.max(f[i - 1][j][1], f[i - 1][j - 1][0] - prices[i]); // max(持股, 买入)
    }
    
System.out.println(f[n][m][0]);
```

**滚动数组优化** $O(M)$

```java
Scanner sc = new Scanner(System.in);
final int N = 100010, M = 110, INF = 0x3f3f3f3f;
int[][] f = new int[M][2]; // f[i][j][k] 表示考虑前i天的股市, 且前i天进行了j次交易, 第i天的持股状态为k
int[] prices = new int[N];
int n = sc.nextInt(), m = sc.nextInt();

for(int i = 1; i <= n; i ++ ) prices[i] = sc.nextInt();
for(int j = 0; j <= m; j ++ ) f[j][1] = -INF; // 初始化不合法情况

for(int i = 1; i <= n; i ++ )
    for(int j = m; j >= 1; j -- )
    {
        f[j][0] = Math.max(f[j][0], f[j][1] + prices[i]); // max(不持股, 卖出)
        f[j][1] = Math.max(f[j][1], f[j - 1][0] - prices[i]); // max(持股, 买入)
    }
    
System.out.println(f[m][0]);
```



##### （5）买卖股票的最佳时机（含冷冻期） V（状态机DP）

**问题**

给定一个长度为 $N(1≤N≤10^5)$ 的数组，数组中的第 $i$ 个数字表示一个给定股票在第 $i$ 天的价格。

设计一个算法计算出**最大利润**。在满足以下约束条件下，你可以**尽可能地完成更多的交易**（多次买卖一支股票）:

- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即**冷冻期**为 1 天)。

**思路**

定义状态：状态0表示不持股, 状态1表示持股，状态2表示冷冻期

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207182022433.png" alt="image-20220718202220354" width="450px" />

**动态规划**

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207182019475.png" alt="image-20220718201959407" width="700px" />

**状态机模型 DP** $O(N)$

```java
Scanner sc = new Scanner(System.in);
final int N = 100010, INF = 0x3f3f3f3f;
int[][] f = new int[N][3]; // f[i][j]表示考虑前i天的股市, 且第i天的持股状态是j
int[] prices = new int[N];

int n = sc.nextInt();

for(int i = 1; i <= n; i ++ ) prices[i] = sc.nextInt();
f[0][1] = -INF;

for(int i = 1; i <= n; i ++ )
{
    f[i][0] = Math.max(f[i - 1][0], f[i - 1][2]);
    f[i][1] = Math.max(f[i - 1][1], f[i - 1][0] - prices[i]);
    f[i][2] = f[i - 1][1] + prices[i];
}

System.out.println(Math.max(f[n][0], f[n][2]));
```

##### （6）买卖股票的最佳时机（含手续费） VI（状态机DP）

**问题**

给定一个长度为 $N(1≤N≤10^5)$ 的数组，数组中的第 $i$ 个数字表示一个给定股票在第 $i$ 天的价格，再给定一个非负整数 $f$，表示交易股票的手续费用。

设计一个算法来计算你所能获取的**最大利润**。你可以**无限次地完成交易**，但是你每次交易都需要支付**手续费**。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**状态机模型 DP** $O(N)$ 

```java
Scanner sc = new Scanner(System.in);
final int N = 100010, INF = 0x3f3f3f3f;
int[][] f = new int[N][2]; // f[i][j]表示考虑前i天的股市, 且第i天的持股状态是j
int[] prices = new int[N];

int n = sc.nextInt(), fee = sc.nextInt();

for(int i = 1; i <= n; i ++ ) prices[i] = sc.nextInt();

f[0][1] = -INF; // 初始化不合法的情况

for(int i = 1; i <= n; i ++ )
{
    f[i][0] = Math.max(f[i - 1][0], f[i - 1][1] + prices[i]);
    f[i][1] = Math.max(f[i - 1][1], f[i - 1][0] - prices[i] - fee);
}

System.out.println(f[n][0]);
```

### 3、打家劫舍

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207201701285.png" alt="打家劫舍总结.png" width="700px" />

##### （1）打家劫舍 I（线性DP）

**问题**

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是**相邻的房屋**装有相互连通的**防盗系统**，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你**不触动警报装置**的情况下 ，一夜之内能够偷窃到的**最高金额**。

**思路** 

所有房屋排列方式为**线性**，制约关系为**不能抢相邻的房屋**

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207201708138.png" alt="image-20220720170849069" width="600px" />

**线性 DP** $O(n)$

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        int[] cash = new int[n + 1];
        for(int i = 1; i <= n; i ++ ) cash[i] = nums[i - 1];

        final int N = 110;
        int[] f = new int[N]; // f[i]表示考虑前i间房屋, 所有满足要求的抢劫方案的集合
        
        f[0] = 0; f[1] = cash[1]; // 初始化f数组

        for(int i = 2; i <= n; i ++ ) // 根据第i间房屋是否抢劫对集合进行划分
            f[i] = Math.max(f[i - 1], f[i - 2] + cash[i]);

        return f[n];
    }
}
```

##### （2）打家劫舍 II（线性DP）

**问题**

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方**所有的房屋都围成一圈** ，这意味着**第一个房屋和最后一个房屋是紧挨**着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，今晚能够偷窃到的**最高金额**。

**思路**

所有房屋排列方式为**环形**，制约关系为**不能抢相邻的房屋**，且**首尾两房屋不能同时抢**

环形排列的问题--->两个线性排列的问题，即分别排除第 $1$ 间房屋($2～n$)和第 $n$ 间房屋($1～n-1$)，求`打家劫舍I`

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207201722338.png" alt="image-20220720172202267" width="500px" />

**线性 DP** $O(n)$

```java
class Solution {
    public int rob(int[] nums) {

        int n = nums.length;
        int[] cash = new int[n + 1];
        for(int i = 1; i <= n; i ++ ) cash[i] = nums[i - 1];

        final int N = 110;
        int[] f = new int[N];

        if(n > 1) { f[1] = 0; f[2] = cash[2];} 
        for(int i = 3; i <= n; i ++ ) // 求第2~n间房屋的打家劫舍I
            f[i] = Math.max(f[i - 1], f[i - 2] + cash[i]);

        f[0] = 0; f[1] = cash[1];
        for(int i = 2; i <= n - 1; i ++ ) // 求第1~n-1间房屋的打家劫舍I
            f[i] = Math.max(f[i - 1], f[i - 2] + cash[i]);

        return Math.max(f[n - 1], f[n]); // 两个方式求最大值
    }
}
```

##### （3）打家劫舍 III（树形DP）

**问题**

小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 `root` 。

除了 `root` 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的**所有房屋的排列类似于一棵二叉树**”。 如果两个直接相连的房子在同一天晚上被打劫 ，房屋将自动报警。

给定二叉树的 `root` 。返回 在不触动警报的情况下 ，小偷能够盗取的最高金额 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202207201730790.jpeg" alt="img" width="200px" />

**思路**

所有房屋排列方式为**二叉树形**，制约关系体现在**父节点与左右孩子节点的制约**，具体为父节点抢了，左右孩子一定不能抢

此题与[没有上司的舞会](https://www.acwing.com/problem/content/287/)一致，解题思路相同

**树形DP** $O(logn)$ 

```java
class Solution {

    final int N = 10010;
    // f[i][0]表示以i为根子树中，不迭代i的所以情况的集合
    // f[i][1]表示以i为根子树中，迭代i的所以情况的集合
    int[][] f = new int[N][2]; 
    int idx = 1;
    HashMap<TreeNode, Integer> mp = new HashMap<>(); // 存储TreeNode及其编号的键值对

    public void code(TreeNode u) { // 对树中的每一个结点进行编号, 根结点的编号为1
        mp.put(u, idx ++ );
        if(u.left != null) code(u.left);
        if(u.right != null) code(u.right);
    }

    public void dfs(TreeNode u) { // 求解树形DP
        int root = mp.get(u);
        f[root][1] = u.val;

        if(u.left != null) 
        {
            dfs(u.left);
            int l = mp.get(u.left);
            f[root][1] += f[l][0]; // 父节点抢了，左右孩子一定不能抢
            f[root][0] += Math.max(f[l][0], f[l][1]);
        }

        if(u.right != null)
        {
            dfs(u.right);
            int r = mp.get(u.right);
            f[root][1] += f[r][0]; // 父节点抢了，左右孩子一定不能抢
            f[root][0] += Math.max(f[r][0], f[r][1]);
        }
    }

    public int rob(TreeNode root) {
        code(root);
        dfs(root);
        return Math.max(f[1][0], f[1][1]);
    }
}
```

### 4、丑数

##### （1）丑数（分解质因数）

**问题**

**丑数** 就是只包含质因数 `2`、`3` 或 `5` 的正整数。

给你一个整数 `n` ，请你判断 `n` 是否为 **丑数** 。如果是，返回 `true` ；否则，返回 `false` 。

**思路一** **除因子**

```java
class Solution {
    public boolean isUgly(int n) {
        if(n <= 0) return false; // 特判

        while(n % 2 == 0) n /= 2; // 如果包含2，将2除尽
        while(n % 3 == 0) n /= 3; // 如果包含3，将3除尽
        while(n % 5 == 0) n /= 5; // 如果包含5，将5除尽

        return n == 1; // 如果还包含其他质因数, 则不是丑数
    }
}
```

**思路二** **分解质因数** $O(n)$ 

```java
class Solution {
	// prime存放分解质因数后的结果
    Map<Integer, Integer> prime = new HashMap<>(); // key是底数, value是指数

    public void find(int x) { // 分解质因数, x必须是自然数
        for(int i = 2; i <= x / i; i ++ )
            if(x % i == 0) { // 如果x包含i这个质因子, 就将i除尽
                int t = prime.getOrDefault(i, 0);
                while(x % i == 0) {
                    x /= i; 
                    t ++ ;
                }
                prime.put(i, t);
            }

        if(x > 1) prime.put(x, prime.getOrDefault(x, 0) + 1); // 将最后一个大于 sqrt(x)的因子加进去
    }

    public boolean isUgly(int n) {
        if(n == 1) return true; // 特判
        if(n <= 0) return false; // 特判
        
        find(n);
        Set<Integer> factors = prime.keySet();
        for(Integer x : factors)
            if(x != 2 && x != 3 && x != 5) return false; // 如果n还包含其他因子，则不是丑数
        return true; // n只包含2或3或5
    }
}
```

##### （2）丑数 II（动态规划）

**问题**

给你一个整数 `n` ，请你找出并返回第 `n` 个 **丑数** 。

**丑数** 就是只包含质因数 `2`、`3` 和/或 `5` 的正整数。

**思路** 

定义数组 $f$，其中 $f[i]$ 表示第 $i$ 个丑数，第 $n$ 个丑数即为 $f[n]$。

由于最小的丑数是 $1$，因此 $f[1]=1$。

如何得到其余的丑数呢？定义三个指针 $a$, $b$, $c$ ，表示下一个丑数是当前指针指向的丑数乘以对应的质因数。初始时，三个指针的值都是 $1$。

当 $2≤i≤n$ 时，令 $\textit{f}[i]=\min(\textit{f}[a] \times 2, \textit{f}[b] \times 3, \textit{f}[c] \times 5)$)，然后分别比较 $f[i]$ 和 $\textit{f}[a] \times 2,\textit{f}[b] \times 3,\textit{f}[c] \times 5$ 是否相等，如果相等则将对应的指针加 1。

**动态规划** $O(n)$

```java
class Solution {
    public int nthUglyNumber(int n) {
        int[] f = new int[n + 1]; // 状态数组，f[i]表示第i个丑数, i从1开始
        f[1] = 1; // 初始化

        int a = 1, b = 1, c = 1; // 定义三个指针, 初始化时指向第一个丑数
        for(int i = 2; i <= n; i ++ ) { // 依次计算出第2, ..., n个丑数
            int x = f[a] * 2, y = f[b] * 3, z = f[c] * 5; // 下一个丑数
            f[i] = Math.min(Math.min(x, y), z); // 值最小的即可作为下一个丑数
            
            // 如果一个丑数f[a]通过乘以2可以得到下一个丑数，那么这个丑数f[a]就永远失去了同2相乘的资格（没有必要再乘了），我们把a++让a指向下一个丑数即可
            if(f[i] == x) a ++ ;
            if(f[i] == y) b ++ ;
            if(f[i] == z) c ++ ;
        }

        return f[n];
    }
}
```

##### （3）超级丑数（动态规划）

**问题** 

**超级丑数** 是一个正整数，并满足其所有质因数都出现在质数数组 `primes` 中。

给你一个整数 `n` 和一个整数数组 `primes` ，返回第 `n` 个 **超级丑数** 。

题目数据保证第 `n` 个 超级丑数 **在 32-bit** 带符号整数范围内。

**思路**

此问题是 [丑数II](https://leetcode.cn/problems/ugly-number-ii/) 的拓展题，思路与其类似

定义数组 $f$，其中 $f[i]$ 表示第 $i$ 个丑数，第 $n$ 个丑数即为 $f[n]$。

由于最小的丑数是 $1$，因此 $f[1]=1$。

如何得到其余的丑数呢？定义指针数组 $p$，表示下一个丑数是当前指针指向的丑数乘以对应的质因数。初始时三个指针的值都是 $1$。

当 $2≤i≤n$ 时，令 $\textit{f}[i]=\min(\textit{f}[p[1]] \times prime[1], \textit{f}[p[2]] \times prime[2], ...,\textit{f}[p[k]] \times prime[k])$)，然后分别比较 $f[i]$ 和 $\textit{f}[p[1]] \times prime[1],\textit{f}[p[2]] \times prime[2],...,\textit{f}[p[k]] \times prime[k]$ 是否相等，如果相等则将对应的指针加 1。

**动态规划** $O(nm)$， $m$表示`prime`数组的大小

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] f = new int[n + 1]; // 状态数组，f[i]表示第i个丑数, i从1开始
        f[1] = 1; // 初始化第一个丑数

        int[] p = new int[primes.length]; // p是指针数组
        Arrays.fill(p, 1); // 指针数组p中的每个指针初始化时, 都指向第一个丑数

        for(int i = 2; i <= n; i ++ ) {
            // 计算下一个丑数
            long[] a = new long[primes.length];
            long t = Long.MAX_VALUE;
            for(int j = 0; j < p.length; j ++ ) { // 指针数组所指丑数与对应的prime相乘, 最小值即为下一个丑数
                a[j] = (long)f[p[j]] * primes[j]; // 可能会int溢出
                t = Math.min(t, a[j]);
            }
            f[i] = (int)t;
            // 更新指针
            // 如果一个丑数f[p[i]]通过乘以prime[i]可以得到下一个丑数，那么这个丑数f[p[i]]就永远失去了同prime[i]相乘的资格（没有必要再乘了），我们把prime[i]++让prime[i]指向下一个丑数即可
            for(int j = 0; j < a.length; j ++ ) 
                if(f[i] == a[j]) p[j] ++ ;
        }

        return f[n];
    }
}
```

##### （4）第 N 个神奇数字（二分+容斥原理）

**问题**

一个正整数如果能被 `a` 或 `b` 整除，那么它是神奇的。

给定三个整数 `n` , `a` , `b` ，返回第 `n` 个神奇的数字。因为答案可能很大，所以返回答案 **对** `109 + 7` **取模** 后的值。

**思路**

设 $L=lcm(a, b)$ 为 $a,b$ 的**最小公倍数**，$L=\frac{a * b}{gcd(a, b)}$

把$1～x$ 之间 的 $a$ 的倍数、$b$ 的倍数组成的集合分别叫做 $A$、$B$，$1～x$之间 的神奇数字相当于集合 $A \cup B $，集合元素的个数为 $|A \cup B |$，由**容斥原理**可得：
$$
\left|A_{1} \cup A_{2} \cup \ldots \cup A_{n}\right|=\sum_{1 \leq i \leq n}\left|A_{i}\right|-\sum_{1 \leq i<j \leq n}\left|A_{i} \cap A_{j}\right|+\ldots+(-1)^{n-1}\left|A_{1} \cap A_{2} \cap \ldots \cap A_{n}\right|
$$
带入可得
$$
|A \cup B |=|A|+|B|-|A \cap B|
$$
也就是说$1～x $ 的神奇数字个数为
$$
f(x)=\left\lfloor\frac{x}{a}\right\rfloor+\left\lfloor\frac{x}{b}\right\rfloor-\left\lfloor\frac{x}{\operatorname{lcm}(a, b)}\right\rfloor
$$
$f(x)$ 为 $1～x $之间的神奇数字的个数，是一个**单调递增函数**。$f(x)=\left\lfloor\frac{x}{a}\right\rfloor+\left\lfloor\frac{x}{b}\right\rfloor-\left\lfloor\frac{x}{lcm(a,b)}\right\rfloor$，有 $\left\lfloor\frac{x}{a}\right\rfloor$个数字能被 $a$ 整除的，有 $\left\lfloor\frac{x}{b}\right\rfloor$个数字能被 $b$ 整除的，有 $\left\lfloor\frac{x}{lcm(a,b)}\right\rfloor$个数字能被 $a,b$ 整除的。

$f(x)$ 是一个单增函数，可以用**二分**搜索来做这道题，在可行解范围内**二分左边界**

**二分** $o(logn)$

```java
class Solution {
    final int mod = (int)1e9 + 7;

    public int nthMagicalNumber(int n, int a, int b) {
        long l = 0, r = (long)1e14; // 可行解所在的范围是 [l,r], 即[0, 1e14]
        while(l < r) {
            long mid = l + r >> 1;
            // 满足 (mid / a + mid / b - mid / lcm(a,b) == n) 条件的mid有很多个, 最左边的mid一定是第n个神奇数字
            if(mid / a + mid / b - mid / lcm(a,b) < n) l = mid + 1; // 二分左边界
            else r = mid; 
        }

        return (int)(l % mod);
    }

    public int gcd(int a, int b) { // 求 a 和 b 的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }

    public int lcm(int a, int b) { // 求 a 和 b 的最小公倍数
        return a * b / gcd(a, b);
    }   
}
```

##### （5）丑数 III（二分+容斥原理）

**问题**

给你四个整数：`n` 、`a` 、`b` 、`c` ，请你设计一个算法来找出第 `n` 个丑数。

丑数是可以被 `a` **或** `b` **或** `c` 整除的 **正整数** 。

本题结果在 `[1, 2 * 10^9]` 的范围内

**思路** 

此问题是 [第 N 个神奇数字](https://leetcode.cn/problems/nth-magical-number/) 的拓展题，思路与其类似

把$1～x$ 之间 的 $a$ 的倍数、$b$ 的倍数、$c$ 的倍数组成的集合分别叫做 $A$、$B$、$C$，$1～x$之间 的丑数相当于集合 $A \cup B \cup C$，集合元素的个数为 $|A \cup B \cup C|$，由**容斥原理**可得：
$$
\left|A_{1} \cup A_{2} \cup \ldots \cup A_{n}\right|=\sum_{1 \leq i \leq n}\left|A_{i}\right|-\sum_{1 \leq i<j \leq n}\left|A_{i} \cap A_{j}\right|+\ldots+(-1)^{n-1}\left|A_{1} \cap A_{2} \cap \ldots \cap A_{n}\right|
$$
带入可得
$$
|A \cup B \cup C|=|A|+|B|+|C|-|A \cap B|-|A \cap C|-|B \cap C|+|A \cap B \cap C|
$$
也就是说$1～x $ 的丑数个数为
$$
f(x)=\left\lfloor\frac{x}{a}\right\rfloor+\left\lfloor\frac{x}{b}\right\rfloor+\left\lfloor\frac{x}{c}\right\rfloor-\left\lfloor\frac{x}{\operatorname{lcm}(a, b)}\right\rfloor-\left\lfloor\frac{x}{\operatorname{lcm}(a, c)}\right\rfloor-\left\lfloor\frac{x}{\operatorname{lcm}(b, c)}\right\rfloor+\left\lfloor\frac{x}{\operatorname{lcm}(a, b, c)}\right\rfloor
$$
$f(x)$ 是一个单增函数，可以用**二分**搜索来做这道题，在可行解范围内**二分左边界**

**二分** $O(logn)$ 

```java
class Solution {
    public int nthUglyNumber(int n, int a, int b, int c) {
        long l = Math.min(Math.min(a, b), c), r = (long)2e9; // 可行解所在的范围是 [l,r], 即[0, 2e9]
        while(l < r) {
            long mid = l + r >> 1;
            if(check(mid, a, b, c, n)) l = mid + 1; // 满足check()条件的mid有很多个, 最左边的mid一定是第n个丑数
            else r = mid;
        }
        return (int)l;
    }

    public boolean check(long x, int a, int b, int c, int n) {
        return x / a + x / b + x / c 
        - x / lcm(a, b) - x / lcm(a, c) - x / lcm(b, c) 
        + x / lcm(lcm(a, b), c) < n;        
    }

    public long gcd(long a, long b) { // 求 a 和 b 的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }

    public long lcm(long a, long b) { // 求 a 和 b 的最小公倍数
        return a * b / gcd(a, b);
    }  
}
```

##### （6）AcWing 890. 能被整除的数（容斥原理）

**问题**

给定一个整数 $n$ 和 $m$ 个不同的**质数** $p_1,p_2,…,p_m$。

请你求出 $1∼n$ 中能被 $p_1,p_2,…,p_m$ 中的至少一个数整除的整数有多少个

- $1≤m≤16,1≤n$,
- $p_i≤10^9$

**思路** 

容斥原理
$$
S_1⋃S_2⋃...S_m=S_1+S_2+…+S_i−(S_1⋂S_2+S_1⋂S_3+…+S_{m−1}⋂S_m)+...\\
|S_1⋃S_2⋃...S_m|=|S_1|+|S_2|+…+|S_i|−(|S_1⋂S_2|+|S_1⋂S_3|+…+|S_{m−1}⋂S_m|)+...
$$
求出 $1∼n$ 中能被 $p_1,p_2,…,p_m$ 中的至少一个数整除的整数有多少个?

- 将 $1∼n$ 中是 $p_1$的倍数视为集合 $S_1$，是 $p_2$ 的倍数视为集合 $S_2$，...
- "$1∼n$ 中能被 $p_1,p_2,…,p_m$ 中的至少一个数整除的整数的个数”可以用集合表示为 $|S_1⋃S_2⋃...S_m|$

- 调用以上容斥原理进行计算，具体计算方式：
  -  $|S_1|=n/p_1$；
  - $|S_1⋂S_2|=n/(p_1×p_2)$；
  - ....

注：容斥原理表达式右侧所有集合的个数为 $2^m-1$

可以用 $m$ 位的二进制数进行枚举，每一个二进数(除0外)都可以与表达式右侧的集合一一对应

$O(m×2^m)$

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
typedef long long LL;
const int N = 20;
int n, m;
int p[N]; // 存储给定的质数

int main()
{
    cin >> n >> m;
    for(int i = 0; i < m; i ++) cin >> p[i];
 
    int res = 0; // 表示1-n中能被质数p1、p2、...、或pm整除的数的个数
    for(int i = 1; i < 1 << m; i ++) // 用二进制数枚举所有集合
    {
        int t = 1, cnt = 0; // t表示质数的乘积，cnt参与乘积的质数个数
        for(int j = 0; j < m; j ++) // 枚举所有二进制位
            if(i >> j & 1) // 二进制数i的第j位是1
            {
                if((LL)t * p[j] > n)
                {
                    t = -1;
                    break;
                }
                t *= p[j];
                cnt ++ ;
            }
        
        if(t != -1)
        {
            if(cnt % 2) res += n / t; // 奇数是+
            else res -= n / t; // 偶数是-
        }
    }
    
    cout << res;
    return 0;
}
```

### 5、剪绳子

##### （1）剪绳子 I（线性 DP）

**问题**

给你一根长度为 $n(2 <= n <= 58)$ 的绳子，请把绳子剪成整数长度的 $m$ 段（$m、n$都是整数，$n>1$ 并且 $m>1$），每段绳子的长度记为 $k[0],k[1]...k[m-1]$ 。请问 $k[0]×k[1]×...×k[m-1]$ 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为 2、3、3 的三段，此时得到的最大乘积是 18。

**思路** 

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208131744608.png" alt="image-20220813174400472" width="600px" />

**线性 DP** $O(n^2)$

```java
class Solution {
    public int cuttingRope(int n) {
        final int N = 70;
        int[] f = new int[N]; // f[i]表示一根长度为i的绳子, 剪成m段的所有方案的集合, m段的最大乘积是f[i]
    
        f[1] = 1; // 初始化

        for(int i = 2; i <= n; i ++ ) 
            for(int j = 1; j <= i / 2; j ++ ) { // 状态计算
                int t = Math.max(f[j], j) * Math.max(f[i - j] ,i - j);
                f[i] = Math.max(f[i], t);
            }
                
        return f[n];
    }
}
```



##### （2）剪绳子 II（线性 DP+高精度）

**问题**

给你一根长度为 $n(2 <= n <= 1000)$ 的绳子，请把绳子剪成整数长度的 $m$ 段（$m、n$都是整数，$n>1$ 并且 $m>1$），每段绳子的长度记为 $k[0],k[1]...k[m - 1]$ 。请问  可能的最大$k[0]×k[1]×...×k[m-1]$乘积是多少？例如，当绳子的长度是 8 时，我们把它剪成长度分别为 2、3、3的三段，此时得到的最大乘积是 18

**思路** 

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208131744608.png" alt="image-20220813174400472" width="600px" />

`Java` 中 提供了用于处理高精度的类 `BigInteger`

**线性DP + 高精度** $O(n^2)$ 

```java
import java.math.BigInteger;

class Solution {
    public int cuttingRope(int n) {
        final int N = 1010;
        final BigInteger mod = BigInteger.valueOf(1000000007);

        // f[i]表示一根长度为i的绳子, 剪成m段的所有方案的集合, m段的最大乘积是f[i]
        BigInteger[] f = new BigInteger[N]; 
        Arrays.fill(f, BigInteger.valueOf(0));
        f[1] = BigInteger.valueOf(1); // 初始化

        for(int i = 2; i <= n; i ++ ) 
            for(int j = 1; j <= i / 2; j ++ ) { // 状态计算
                BigInteger a = f[j].max(BigInteger.valueOf(j));
                BigInteger b = f[i - j].max(BigInteger.valueOf(i - j));
                BigInteger t = a.multiply(b);
                f[i] = f[i].max(t);
            }
                
        return f[n].divideAndRemainder(mod)[1].intValue();
    }
}
```

### 6、最多能完成的排序的块

##### （1）最多能完成的排序的块 I（贪心）

**问题 **

给定一个长度为 `n` 的整数数组 `arr` ，它表示在 `[0, n - 1]` 范围内的**整数的排列**。

我们将 `arr` 分割成若干 **块** (即分区)，并对每个块单独排序。将它们连接起来后，使得连接的结果和按升序排序后的原数组相同。

返回数组能分成的最多块数量。

注意，**arr** 中每个元素都 **不同**

**思路** 

当遍历到第`i`个位置时，如果可以切分为块，那前i个位置的最大值一定等于`i`。

否则，一定有比`i`大的数划分到前面的块，一定有比`i`小的数划分到后面的块，那块排序后，一定不满足升序。

且前一个块的最大值要小于后一个块的任意一个数

**贪心** $O(long)$ 

```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        int max = -0x3f3f3f3f, cnt = 0; 
        for(int i = 0; i < arr.length; i ++ ) {
            max = Math.max(max, arr[i]);
            if(max == i) cnt ++ ;
        }
        return cnt;
    }
}
```



##### （2）最多能完成的排序的块 II



### 7、组合排列问题

##### （1）子集 I（回溯）

**问题**

给你一个整数数组 `nums`（$1 <= nums.length <= 10$） ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

**思路**

**递归实现指数型枚举**

```java
class Solution {

    final int N = 16;
    List<List<Integer>> res = new ArrayList<>();
    int[] st = new int[N]; // 状态数组 1表示选 2表示不选
    int[] a = new int[N]; // 整数数组 a
    int n = 0;

    public void dfs(int u) {
        if(u >= n) { // 找到一个子集
            List<Integer> arr = new ArrayList<>();
            for(int i = 0; i < n; i ++ )
                if(st[i] == 1) arr.add(a[i]);
            res.add(arr);
            return ;
        }

        st[u] = 2; dfs(u + 1); st[u] = 0; // 不选第u个数
        st[u] = 1; dfs(u + 1); st[u] = 0; // 选第u个数
    }

    public List<List<Integer>> subsets(int[] nums) {
        a = nums;
        n = nums.length;

        dfs(0);
        return res;
    }
}
```

##### （2）子集 II（去重+回溯）

**问题** 

给你一个整数数组 `nums` （$1 <= nums.length <= 10$），其中可能包含**重复元素**，请你返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。返回的解集中，子集可以按 **任意顺序** 排列。

**思路**

先对原数组进行排序，再**递归实现指数型枚举**，用 `Set` 集合对元素去重

```java
class Solution {

    final int N = 16;
    Set<List<Integer>> res = new HashSet<>(); // set去重
    int[] st = new int[N]; // 状态数组 1表示选 2表示不选
    int[] a = new int[N]; // 整数数组 a
    int n = 0;

    public void dfs(int u) {
        if(u >= n) { // 找到一个子集
            List<Integer> arr = new ArrayList<>();
            for(int i = 0; i < n; i ++ )
                if(st[i] == 1) arr.add(a[i]);
            res.add(arr);
            return ;
        }

        st[u] = 2; dfs(u + 1); st[u] = 0; // 不选第u个数
        st[u] = 1; dfs(u + 1); st[u] = 0; // 选第u个数
    }

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        a = nums;
        n = nums.length;

        dfs(0);
        return new ArrayList(res);
    }
}
```

##### （3）组合



### :mag_right: 参考文献 

[1] https://leetcode.cn/ 

[2] https://www.acwing.com/ 

### :closed_lock_with_key: License

本文内容不是将网上的资料随意拼凑而来，除了少部分引用书上和技术文档的原文（这部分内容都在末尾的参考文献中加了出处），其余都是作者的原创。在您引用本文内容或者对内容进行修改演绎时，请署名并以相同方式共享，谢谢。

转载文章请在开头明显处标明该页面地址，公众号等其它转载请联系 [1246550576@qq.com](mailto:1246550576@qq.com)

### 📝排版申明

笔记内容按照一定标准进行排版，以保证内容的可读性。

不使用 `![]()` 这种方式来引用图片，而是用 `<img>` 标签。为了能够控制图片以合适的大小显示，使用 `<div align="center"> <img src=""/> </div>` 达到居中的效果。

排版美化工具：[Typora-Purple](https://theme.typora.io/theme/Purple/)

