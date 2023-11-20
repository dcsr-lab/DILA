# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之枚举

### 1、枚举

#### （1）[活字印刷](https://leetcode.cn/problems/letter-tile-possibilities/)

**问题**：

你有一套活字字模 `tiles`，其中每个字模上都刻有一个字母 `tiles[i]`。返回你可以印出的非空字母序列的数目。

**注意：**本题中，每个活字字模只能使用一次。

**数据范围**：

- `1 <= tiles.length <= 7`
- `tiles` 由大写英文字母组成

例：

```
输入："AAB"
输出：8
解释：可能的序列为 "A", "B", "AA", "AB", "BA", "AAB", "ABA", "BAA"。
```

**思路：**

DFS搜索， N个字符组成的字符序列，每个位置有N+1种可能(选N+不选1), (N+1)^(N+1)<=O(16777216)

```go
class Solution {
    // N个字符组成的字符序列，每个位置有N+1种可能(选N+不选1), (N+1)^(N+1)<=O(16777216)
    static String tiles;
    static int[] st; // 1-select, 0-not select
    static int[] way;
    int N; // length

    static HashSet<String> set;

    public void dfs(int u) {
        if(u >= N) {
            StringBuilder res = new StringBuilder();
            for(int i = 0; i < N; i ++) {
                if(st[i] == 1 && way[i] != -1) res.append(tiles.charAt(way[i]));
            }
            if(res.toString().equals("")) return;
            set.add(res.toString());
            return;
        }

        // 第u个位置有N + 1种可能
        for(int i = 0; i < N; i ++) { // 第u个位置选
            if (st[i] == 1) continue;
            st[i] = 1;
            way[u] = i;
            dfs(u + 1);
            way[u] = -1;
            st[i] = 0; // 恢复现场 
        }
        dfs(u + 1); // 第u个位置不选
    }
    
    public int numTilePossibilities(String tiles) {
        this.N = tiles.length();
        this.tiles = tiles;
        this.st = new int[N];
        this.way = new int[N];
        this.set = new HashSet<String>();

        for(int i = 0; i < N; i ++) {
            this.way[i] = -1;
        }

        dfs(0);
        return this.set.size();
    }
}
// "" A A B AA AB AB AAB
```

#### （2）[2475. 数组中不等三元组的数目](https://leetcode.cn/problems/number-of-unequal-triplets-in-array/)

**问题**：

给你一个下标从 **0** 开始的正整数数组 `nums` 。请你找出并统计满足下述条件的三元组 `(i, j, k)` 的数目：

- `0 <= i < j < k < nums.length`
- `nums[i]`、`nums[j]` 和 `nums[k]` **两两不同** 。
  - 换句话说：`nums[i] != nums[j]`、`nums[i] != nums[k]` 且 `nums[j] != nums[k]` 。

返回满足上述条件三元组的数目*。*

例子：

```
输入：nums = [4,4,2,4,3]
输出：3
解释：下面列出的三元组均满足题目条件：
- (0, 2, 4) 因为 4 != 2 != 3
- (1, 2, 4) 因为 4 != 2 != 3
- (2, 3, 4) 因为 2 != 4 != 3
共计 3 个三元组，返回 3 。
注意 (2, 0, 4) 不是有效的三元组，因为 2 > 0 。
```

**数据范围**：

- `3 <= nums.length <= 100`
- `1 <= nums[i] <= 1000`

**思路**：

递归实现组合枚举。但是，**一个组合中的元素不能有重复元**素。

```java
class Solution {

    int n = 0, m = 3; // 从n中选取m个数
    int[] map = new int[1010]; // 存储每个数出现的次数
    int[] a = new int[110]; // 存储nums去重后的数组, 下标从0开始

    // 假设选择的三个不同的数为a[i],a[j],a[k]
    // count = map[a[i]] * map[a[j]] * map[a[k]]
    public int dfs(int u, int start, int count) { // u当前枚举的位置, start表示当前可以选的数为start~n
        if(u - 1 + n - start + 1 < m) return 0; // 剪枝；剩余的数不足m个
        if(u == m + 1) return count; // 找到一个合适的解

        int res = 0;
        for(int i = start; i <= n; i ++)
            res += dfs(u + 1, i + 1, count * map[a[i - 1]]);
        return res;
    }

    public int unequalTriplets(int[] nums) {
        int cnt = 0;
        for(int i = 0; i < nums.length; i ++ ) {
            if(map[nums[i]] == 0) a[cnt ++] = nums[i];
            map[nums[i]] ++ ;
        }
        this.n = cnt;      
        return dfs(1, 1, 1); // 下标从1开始
    }
}
```

#### （3）247. 中心对称数 II:writing_hand: 

**问题**：

给定一个整数 `n` ，返回所有长度为 `n` 的 **中心对称数** 。你可以以 **任何顺序** 返回答案。

**中心对称数** 是一个数字在旋转了 `180` 度之后看起来依旧相同的数字（或者上下颠倒地看）。

**思路**：

- 用指定的数字集，**递归实现排列型枚举**
- 判断所枚举的数是否是一个中心对称数

```java
class Solution {

    int n;
    int[] st; // 用来表示0~(n-1)分别放了那个数
    int[] digit = new int[]{0, 1, 6, 8, 9};
    int[] hash = new int[]{0, 1, -1, 3, -1, -1, 9, -1, 8, 6};
    List<String> arr = new ArrayList<>(); 

    public boolean check(String num) { // 判断num是否中心对称数
        String res = "";
        for(char ch : num.toCharArray())
            res = hash[ch - '0'] + res;
        return res.equals(num); // num旋转180, 即每个数都旋转180, 然后倒序拼接起来
    }

    public void dfs(int u){
        if(u > (0 + n - 1 >> 1)) { //采取同时枚举两边的方式, 枚举到中间位置, 则停
            String res = "";
            if(st[0] == 0 && n > 1) return ; // 特判
            for(int i = 0; i < n; i ++) res = res + st[i];
            if(check(res)) arr.add(res); // 找到一个解
            return ;
        }

        for(int i = 0; i < digit.length; i ++){
            st[u] = digit[i]; // 同时枚举两边
            st[n - 1 - u] = hash[digit[i]];
            dfs(u + 1);
            st[u] = -1; // 恢复现场
            st[n - 1 - u] =  - 1;
        }
    }

    public List<String> findStrobogrammatic(int n) {
        this.n = n;
        st = new int[n + 10];

        dfs(0);
        return arr;
    }
}
```

#### （4）248. 中心对称数 III

**问题**：

给定两个字符串 `low` 和 `high` 表示两个整数 `low` 和 `high` ，其中 `low <= high` ，返回 范围 `[low, high]` 内的 **「中心对称数」**总数 。

**中心对称数** 是一个数字在旋转了 `180` 度之后看起来依旧相同的数字（或者上下颠倒地看）。

**思路**：

- 用指定的数字集，**递归实现排列型枚举**（枚举的数位长度`n`取`[low.length(), high.length()]`）
- 判断所枚举的数是否是一个中心对称数

```java
class Solution {

    int[] st; // 用来表示0~(n-1)分别放了那个数
    int[] digit = new int[]{0, 1, 6, 8, 9};
    int[] hash = new int[]{0, 1, -1, 3, -1, -1, 9, -1, 8, 6};
    int cnt = 0;

    public boolean check(String num) { // 判断num是否中心对称数
        String res = "";
        for(char ch : num.toCharArray())
            res = hash[ch - '0'] + res;
        return res.equals(num); // num旋转180, 即每个数都旋转180, 然后倒序拼接起来
    }

    public void dfs(int u, int n, String l, String r){
        if(u > (0 + n - 1 >> 1)) { //采取同时枚举两边的方式, 枚举到中间位置, 则停
            String res = "";
            if(st[0] == 0 && n > 1) return ; // 特判
            for(int i = 0; i < n; i ++) res = res + st[i];
            if(check(res) && Long.parseLong(l) <= Long.parseLong(res) && 
                Long.parseLong(res) <= Long.parseLong(r))
                    cnt ++; // 找到一个解
            return ;
        }

        for(int i = 0; i < digit.length; i ++){
            st[u] = digit[i]; // 同时枚举两边
            st[n - 1 - u] = hash[digit[i]];
            dfs(u + 1, n, l, r);
            st[u] = -1; // 恢复现场
            st[n - 1 - u] =  - 1;
        }
    }

    public int strobogrammaticInRange(String low, String high) {
        st = new int[high.length() + 10];

        for(int i = low.length(); i <= high.length(); i ++)
            dfs(0, i, low, high); // 枚举所有长度为i的中心对称数, 并且要在[low, high]范围内
        
        return cnt;
    }
}
```

#### （5）[646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/)

**问题**

给你一个由 `n` 个数对组成的数对数组 `pairs` ，其中 `pairs[i] = [lefti, righti]` 且 `lefti < righti` 。

现在，我们定义一种 **跟随** 关系，当且仅当 `b < c` 时，数对 `p2 = [c, d]` 才可以跟在 `p1 = [a, b]` 后面。我们用这种形式来构造 **数对链** 。

找出并返回能够形成的 **最长数对链的长度** 。

你不需要用到所有的数对，你可以以任何顺序选择其中的一些数对来构造。

例子

```
输入：pairs = [[1,2],[7,8],[4,5]]
输出：3
解释：最长的数对链是 [1,2] -> [4,5] -> [7,8] 。
```

**数据范围**

- `n == pairs.length`
- `1 <= n <= 1000`
- `-1000 <= lefti < righti <= 1000`

思路

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        int n = pairs.length;
        Arrays.sort(pairs, (a, b) -> a[0] - b[0]);

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

### 



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
