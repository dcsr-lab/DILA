# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第四部分之树状数组



### 1、一维树状数组

#### （1）307. 区域和检索 - 数组可修改

**问题**：

给你一个数组 `nums` ，请你完成两类查询。

- 其中一类查询要求 **更新** 数组 `nums` 下标对应的值
- 另一类查询要求返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** ，其中 `left <= right` 

实现 `NumArray` 类：

- `NumArray(int[] nums)` 用整数数组 `nums` 初始化对象
- `void update(int index, int val)` 将 `nums[index]` 的值 **更新** 为 `val` 
- `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** （即，`nums[left] + nums[left + 1], ..., nums[right]`）

**思路**：树状数组模板题

```java
class NumArray {

    int n;
    int[] a, tr; // a是原数组, tr是树状数组

    public int lowbit(int x) { // 返回x末尾0的个数
        return x & -x;
    }

    public void add(int x, int v) { // 在原数组x的位置加上v， 同时更新树状数组
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) { // 返回原数组[0,x]之间所有元素的和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public NumArray(int[] nums) {
        n = nums.length;
        tr = new int[n + 10];
        a = nums;
        for(int i = 1; i <= n; i ++ ) add(i, nums[i - 1]); // 用nums数组初始化树状数组
    }
    
    public void update(int index, int val) { // 单点更新
        int x = index + 1;
        add(x, val - a[index]);
        a[index] =  val;
    }
    
    public int sumRange(int left, int right) { // 区间求和
        int l = left + 1, r = right + 1;
        return query(r) - query(l - 1);
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(index,val);
 * int param_2 = obj.sumRange(left,right);
 */
```

#### （2）683. K 个关闭的灯泡

**问题**：

`n` 个灯泡排成一行，编号从 `1` 到 `n` 。最初，所有灯泡都关闭。每天 **只打开一个** 灯泡，直到 `n` 天后所有灯泡都打开。

给你一个长度为 `n` 的灯泡数组 `blubs` ，其中 `bulls[i] = x` 意味着在第 `(i+1)` 天，我们会把在位置 `x` 的灯泡打开，其中 `i` **从 0 开始**，`x` **从 1 开始**。

给你一个整数 `k` ，请返回恰好有两个打开的灯泡，且它们中间 **正好** 有 `k` 个 **全部关闭的** 灯泡的 **最小的天数** 。如果不存在这种情况，返回 `-1` 。

**思路**：

对于`bulbs[i] = x`：

- 检查 `x` 前后间隔 `k` 个灯泡的灯泡亮没亮
- `x - 1`和 `x - k - 1`这两个位置的前缀和是否相等，即中间间隔的 `k` 个灯是否都是灭的。同时`x - k - 1` 与 `x - k - 1 - 1` 位置的前缀和应该相差 `1`，即两端的灯应该是亮着
- `x` 和 `x + k` 这两个位置的前缀和是否相等，即中间间隔的 `k` 个灯是否都灭了。同时`x + k + 1` 与 `x` 位置的前缀和应该相差 `1`，即两端的灯应该是亮着

```java
class Solution {

    int n;
    int[] tr; // 树状数组

    public int lowbit(int x) { // lowbit(x)返回x末尾1的个数
        return x & -x;
    }

    public void add(int x, int v) { // 将元素组x的位置的元素加上v
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) { // 查询[1, x]的区间和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public int kEmptySlots(int[] bulbs, int k) {
        n = bulbs.length;
        tr = new int[n + 10];
        for(int i = 0; i < bulbs.length; i ++ ) {
            int x = bulbs[i];
            add(x, 1);

            if(x + k + 1 <= n) { // 恰好有两个打开的灯泡, 其中间[x-k, x-1] 之间的灯泡全部熄灭
                int a = query(x), b = query(x + k), c = query(x + k + 1);
                if(a == b && c - a == 1) return i + 1;
            } 
            if(x - k - 1 >= 1) {  // 恰好有两个打开的灯泡, 其中间[x-k, x-1] 之间的灯泡全部熄灭
                int a = query(x - k - 1 - 1), b = query(x - k - 1), c = query(x - 1);
                if(b - a == 1 && b == c) return i + 1;
            }
        }
        return -1;
    }
}
```

#### （3）[2426. 满足不等式的数对数目](https://leetcode.cn/problems/number-of-pairs-satisfying-inequality/)



### 2、离散化——树状数组

#### （1）剑指 Offer 51. 数组中的逆序对:writing_hand: 

**思路**：

- 将`nums`数组的元素进行去除排序，映射到`1~n`
- 原数组`a[k]`存储`nums[i]`出现的次数，树状数组`tr[k]`支持单点修改并维护前缀和
- 从后向前枚举`nums`数组。`nums[i]`对应的原数组为`a[k]`，更新`nums[i]`出现的次数，并求出前缀和，即比`nums[i]`元素之后比`nums[i]`小的元素个数，也即逆序对的个数

```java
class Solution {
    int[] tr; // 树状数组
    int n;
    Map<Integer,Integer> mp = new HashMap<>();

    public int lowbit(int x){ // lowbit(x) = 2^k k是x二进制表示末尾0的个数
        return x & -x;
    }

    public void add(int x, int v){ // 单点修改
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    } 

    public int query(int x){ // 区间查询
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public int reversePairs(int[] nums) {
        n = nums.length;
        tr = new int[n + 1]; // 树状数组下标从1开始
        // 对nums数组元素去重并排序
        Set<Integer> set = new TreeSet<>();
        for(int i = 0; i < n; i ++) set.add(nums[i]); 
        // 对元素进行离散化
        int idx = 1; // 映射到 1~k
        for(Integer i : set) mp.put(i, idx ++);

        int res = 0;
        for(int i = n - 1; i >= 0; i --){ // 从后向前枚举nums数组
            int k = mp.get(nums[i]); // 得到nums[i]的映射值
            add(k, 1); // 原数组a[k]存储了nums[i]元素的个数, 更新原素组, 同时更新树状数组
            res += query(k - 1); // 查询nums[i]后面比它小的元素个数, 即与其构成逆序对的个数
        }
        return res;
    }

}
```

#### （2）315. 计算右侧小于当前元素的个数

**问题**：

给你一个整数数组 `nums` ，按要求返回一个新数组 `counts` 。数组 `counts` 有该性质： `counts[i]` 的值是 `nums[i]` 右侧小于 `nums[i]` 的元素的数量。

**思路**：此题与上一题求数组中逆序对的个数相似

```java
class Solution {
    
    int n; 
    int[] tr; // 树状数组
    
    public int lowbit(int x) {
        return x & -x;
    }

    public void add(int x, int v) {
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) {
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public List<Integer> countSmaller(int[] nums) {
        // 将所有数从小到大排序, 去重, 离散化
        TreeSet<Integer> unique  = new TreeSet<>();
        for(int val : nums) unique.add(val);
        HashMap<Integer, Integer> hash = new HashMap<>();
        int idx = 1;
        for(Integer val : unique) hash.put(val, idx ++ );

        n = unique.size();
        tr = new int[n + 10];
        
        // 从右往左遍历nums[i], 依次统计nums[i] 右侧小于 nums[i] 的元素的数量
        LinkedList<Integer> res = new LinkedList<>();
        for(int i = nums.length - 1; i >= 0; i -- ) {
            int t = hash.get(nums[i]); // t 是 nums[i] 映射后的值
            res.addFirst(query(t - 1)); // 求 nums[i] 右侧小于 nums[i] 的元素的数量
            add(t, 1);
        }
        return res;
    }
}
```

#### （3）[493. 翻转对](https://leetcode.cn/problems/reverse-pairs/)





### 3、前缀和离散化——树状数组

#### （1）327. 区间和的个数:writing_hand: 

**问题**：

给你一个整数数组 `nums` 以及两个整数 `lower` 和 `upper` 。求数组中，值位于范围 `[lower, upper]` （包含 `lower` 和 `upper`）之内的 **区间和的个数** 。

**区间和** `S(i, j)` 表示在 `nums` 中，位置从 `i` 到 `j` 的元素之和，包含 `i` 和 `j` (`i` ≤ `j`)。

**注意**：`-231 <= nums[i] <= 231 - 1` 

**思路**：

设前缀和数组为 $s$ ，则问题等价于求所有的下标对$(i,j)$，满足下列等式的个数：
$$
s[j]−s[i]∈[lower,upper]
$$
对于每个下标 $j$，以 $j$ 为右端点的满足条件的下标对的数量，就等于数组 $[0..j−1]$ 中的所有整数，出现在区间 $[s[j]−upper,s[j]−lower]$ 的次数。

所以可以考虑使用树状数组

具体地：

- 对原数组求前缀和，得到前缀和数组
- 对所有的前缀和、前缀和与`upper`的差、前缀和与`lower`的差进行离散化
  - 从小到大排序 
  - 去重
  - 从下标1开始映射
- 从左往右遍历前缀和数组的每一个元素$s[j]$，先进行在树状数组中区间查询 $[s[j]−upper,s[j]−lower]$ ，然后在单点更新 $s[j]$

```java
class Solution {

    int n; // 树状数组实际元素个数
    long[] s;
    int[] tr; // s 是前缀和数组, tr是树状数组, s和tr下标都是从1开始

    public int lowbit(int x) { // lowbit(x)返回x末尾1的个数
        return x & -x;
    }

    public void add(int x, int v) { // 将元素组x的位置的元素加上v
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) { // 查询[1, x]的区间和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public int countRangeSum(int[] nums, int lower, int upper) {
        s = new long[nums.length + 10]; 

        // 求前缀和
        for(int i = 1; i <= nums.length; i ++ ) s[i] = s[i - 1] + nums[i - 1];

        // 将前缀和离散化到 1 ~ k
        TreeSet<Long> ts = new TreeSet<>();
        for(int i = 0; i <= nums.length; i ++ ) { // 从小到大排序并去重, s[0] 也需要加入
            ts.add(s[i]); 
            ts.add(s[i] - lower);
            ts.add(s[i] - upper); // s[i] - upper 和 s[i] - lower 也需要加入进行离散化
        }
        int idx = 1;
        HashMap<Long, Integer> hash = new HashMap<>();
        for(Long val : ts) hash.put(val, idx ++); // 从1开始映射
        
        n = ts.size(); // n为树状数组的实际大小
        tr = new int[n + 10];
        
        int res = 0;
        for(int i = 0; i <= nums.length; i ++) { // 从前往后枚举前缀和数组, s[0]也需要加入
            //  query(r)是和为前缀和为s[i] - lower出现的次数
            int l = hash.get(s[i] - upper), r = hash.get(s[i] - lower);
            res += query(r) - query(l - 1);
            add(hash.get(s[i]), 1);
        }

        return res;
    }
}
```

#### （2）2031. 1 比 0 多的子数组个数

**问题**：

给你一个只包含 `0` 和 `1` 的数组 `nums`，请返回 `1` 的数量 **大于** `0` 的数量的子数组的个数。由于答案可能很大，请返回答案对 `109 + 7` **取余** 的结果。

一个 **子数组** 指的是原数组中连续的一个子序列。

思路：

- 将原数组中的所有的 `0` 都换成 `-1`
- 设其前缀和数组为 $s$， 对于每个下标 $j$，以 $j$ 为右端点的满足条件的子数组的个数(`1` 的数量 **大于** `0` 的数量的子数组的个数)，等价于求 在 $j$ 左边比 $s[j]$ 小的前缀和的数量
- 可以考虑使用树状数组

具体的：

- 对原数组求前缀和，得到前缀和数组
- 对所有的前缀和进行离散化
  - 从小到大排序 
  - 去重
  - 从下标1开始映射
- 从左往右遍历前缀和数组的每一个元素$s[j]$，先进行在树状数组中区间查询 ，然后在单点更新 $s[j]$

```java
class Solution {

    final int MOD = (int)1e9 + 7;
    int n;
    int[] tr; // 树状数组

    public int lowbit(int x) {
        return x & -x;
    }

    public void add(int x, int v) {
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) {
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res = (res + tr[i]) % MOD;
        return res;
    }

    public int subarraysWithMoreZerosThanOnes(int[] nums) {
        int[] s = new int[nums.length + 10]; // 前缀和数组

        for(int i = 0; i < nums.length; i ++ ) // 将数组中所有的0替换成-1
            if(nums[i] == 0) nums[i] = -1;
        for(int i = 1; i <= nums.length; i ++ ) s[i] = s[i - 1] + nums[i - 1]; // 求前缀和

        // 所有前缀和和从小到大排序, 去重, 离散化
        TreeSet<Integer> unique = new TreeSet<>();
        for(int i = 0; i <= nums.length; i ++ ) unique.add(s[i]);
        int idx = 1;
        HashMap<Integer, Integer> hash = new HashMap<>();
        for(Integer val : unique) hash.put(val, idx ++ );

        n = unique.size();
        tr = new int[n + 10];
        
        int res = 0;
        for(int i = 0; i <= nums.length; i ++ ) {
            int t = hash.get(s[i]);
            res = (res + query(t - 1)) % MOD;
            add(t, 1);
        }
        return res;
    }
}
```



### 4、二维树状数组

#### （1）308. 二维区域和检索 - 可变:writing_hand: 

**问题**：

给你一个二维矩阵 `matrix` ，你需要处理下面两种类型的若干次查询：

- **更新：**更新 `matrix` 中某个单元的值。
- **求和：**计算矩阵 `matrix` 中某一矩形区域元素的 **和** ，该区域由 **左上角** `(row1, col1)` 和 **右下角** `(row2, col2)` 界定。

实现 `NumMatrix` 类：

- `NumMatrix(int[][] matrix)` 用整数矩阵 `matrix` 初始化对象。
- `void update(int row, int col, int val)` 更新 `matrix[row][col]` 的值到 `val` 。
- `int sumRegion(int row1, int col1, int row2, int col2)` 返回矩阵 `matrix` 中指定矩形区域元素的 **和** ，该区域由 **左上角** `(row1, col1)` 和 **右下角** `(row2, col2)` 界定。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203162232909.jpeg" alt="img" width="500px" />

**思路**：与一维前缀和类似，二维前缀和模板题

```java
class NumMatrix {

    int m, n; // 子矩阵 m 行, n 列 
    int[][] a, tr; // a表示原数组, tr表示树状数组

    public int lowbit(int x) { // 返回x的末尾0的个数
        return x & -x;
    }

    public void add(int x, int y, int v) { // 将原数组(x,y)更新为val, 同时更新tr
        for(int i = x; i <= m; i += lowbit(i)) 
            for(int j = y; j <= n; j += lowbit(j))
                tr[i][j] += v;
    }   

    public int query(int x, int y) { // 查询 (1,1) 到 (x, y) 构成的子矩阵的和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i))
            for(int j = y; j != 0; j -= lowbit(j))
                res += tr[i][j];
        return res;
    }

    public NumMatrix(int[][] matrix) {
        m = matrix.length; n = matrix[0].length;
        tr = new int[n + 10][n + 10];
        a = new int[n + 10][n + 10];
        for(int i = 0; i < m; i ++ )
            for(int j = 0; j < n; j ++){
                a[i + 1][j + 1] = matrix[i][j];
                add(i + 1, j + 1, matrix[i][j]); // 初始化树状数组
            }
    }
    
    public void update(int row, int col, int val) { // 单点修改
        int x = row + 1, y = col + 1;
        add(x, y, val - a[x][y]);
        a[x][y] = val;
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) { // 区间查询
        int x1 = row1 + 1, y1 = col1 + 1, x2 = row2 + 1, y2 = col2 + 1;
        return query(x2, y2) - query(x2, y1 - 1) - query(x1 - 1, y2) + query(x1 - 1, y1 - 1);
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
