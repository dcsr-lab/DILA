# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第四部分之线段树

### 1、线段树

#### （1）LCP 09. 最小跳跃次数:writing_hand: 

**问题**：

为了给刷题的同学一些奖励，力扣团队引入了一个弹簧游戏机。游戏机由 `N` 个特殊弹簧排成一排，编号为 `0` 到 `N-1`。

初始有一个小球在编号 `0` 的弹簧处。若小球在编号为 `i` 的弹簧处，通过按动弹簧，可以选择把小球向右弹射 `jump[i]` 的距离，或者向左弹射到**任意左侧弹簧的位置**。

也就是说，在编号为 `i` 弹簧处按动弹簧，小球可以弹向 `0` 到 `i-1` 中任意弹簧或者 `i+jump[i]` 的弹簧（若 `i+jump[i]>=N` ，则表示小球弹出了机器）。小球位于编号 0 处的弹簧时不能再向左弹。

为了获得奖励，你需要将小球弹出机器。请求出**最少**需要按动多少次弹簧，可以将小球从编号 `0` 弹簧弹出整个机器，即向右越过编号 `N-1` 的弹簧。

**简化后的问题**：给定一个数组 `jump`，长度为 `N`，在第 `i` 个位置可以选择跳到 `0..i-1` 和 `i + jump[i]`，问从 `0` 跳过 `n-1` 的最小跳跃次数是多少？

**数据范围**：

- `1 <= jump.length <= 10^6`
- `1 <= jump[i] <= 10000`

**思路**：

- 考虑第 `i` 个位置，可以**直接跳**到 `i+jump[i]`, 也可以**跳两步**（即先跳到$i+jump[i]$，再往回跳）跳到所有的 `i+1 .. i+jump[i]-1`。
- 每一个元素`f[i]`表示从`1`位置跳到`i`位置的最少跳数。
- 向右跳：`f[i+jump[i]] = min(f[i + jump[i]], f[i] + 1)` 。
- 向左跳：`f[j] = min(f[j], f[i] + 2), i < j < i+jump[i]` 。

用**线段树**来实现区间更新，单点查询。线段树某个节点的区间维护的是数组$f$的区间最小值。

```java
class Node{ // 线段树的结点, 维护一段区间的信息, jump 和 minj初始化都为0x3f3f3f3f
    int l,r; // 区间的左右边界
    int jump; // 表示从1位置跳到[l,r]中的任意一个位置的最少的跳数
    int minj; // 懒标记
    Node(int l, int r){ this.l = l; this.r = r; this.minj = 0x3f3f3f3f;}
    Node(int l, int r, int jump){ this.l = l; this.r = r; this.jump = jump;this.minj = 0x3f3f3f3f;}
}

class Solution {
    final int INF = 0x3f3f3f3f;
    Node[] tr; // 线段树
    int[] f; // 权值序列，权值序列的每一个元素f[i]表示从1位置跳到该位置的最少跳数
    int n; // 权值序列的实际元素个数

    public void pushUp(int u){
        tr[u].jump = Math.min(tr[u << 1].jump, tr[u << 1|1].jump);
    }

    public void pushDown(int u){ // 下传标记
        if(tr[u].l == tr[u].r) {tr[u].minj = INF; return ;} // 叶子结点，清流标记
        tr[u << 1].jump = Math.min(tr[u << 1].jump, tr[u].minj);
        tr[u << 1|1].jump = Math.min(tr[u << 1|1].jump, tr[u].minj);
        tr[u << 1].minj = Math.min(tr[u << 1].minj, tr[u].minj);
        tr[u << 1|1].minj = Math.min(tr[u << 1|1].minj, tr[u].minj);
        tr[u].minj = INF; // 清空标记
    }

    public void build(int u, int l, int r){
        if(l == r) tr[u] = new Node(l,r,f[l]);
        else{
            tr[u] = new Node(l,r);
            int mid = l + r >> 1;
            build(u << 1, l, mid);
            build(u << 1|1, mid + 1, r);
            pushUp(u);
        }
    }

    public int query(int u, int x){ // 单点查询
        if(tr[u].l == tr[u].r) return tr[u].jump;
        else{
            pushDown(u); // 递归查询前, 先下传标记
            int mid = tr[u].l + tr[u].r >> 1;
            if(x <= mid) return query(u << 1, x);
            else return query(u << 1|1, x);
        }
    }

    public void modify(int u, int l, int r, int v){ // 区间更新
        if(tr[u].l >= l && tr[u].r <= r) {
            tr[u].jump = Math.min(tr[u].jump, v);
            tr[u].minj = Math.min(tr[u].minj, v);
        }else{
            pushDown(u);
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, v);
            if(r > mid) modify(u << 1|1, l, r, v);
            // pushUp(u); // 单点查询时此处无需更新父结点
        }
    }

    public int minJump(int[] jump) {
        n = jump.length;
        tr = new Node[4*(n+1)];
        f = new int[n + 1];
        Arrays.fill(f, INF); // 初始化为无穷大表示无法到达
        build(1, 1, n);
        modify(1, 1, 1, 0); // 从1位置到达1位置最小跳数为0

        int res = INF;
        for(int i = 0; i < n; i ++){
            int cnt = query(1, i + 1); // 从0到达i位置最少跳多少次
            if(i + jump[i] >= n) res = Math.min(res, cnt + 1); // 再跳一次就跳出机器
            else{ 
                modify(1, i+jump[i]+1,i+jump[i]+1, cnt + 1);
                if(i+1+1 <= i+jump[i]-1+1) modify(1, i+1+1, i+jump[i]-1+1, cnt + 2);
            }
        }
        return res;
    }
}
```

#### （2）[LCP 52. 二叉搜索树染色](https://leetcode.cn/problems/QO5KpG/)

**问题**：

欢迎各位勇者来到力扣城，本次试炼主题为「二叉搜索树染色」。

每位勇士面前设有一个**二叉搜索树**的模型，模型的根节点为 `root`，树上的各个节点值均不重复。初始时，所有节点均为蓝色。现在按顺序对这棵二叉树进行若干次操作， `ops[i] = [type, x, y]` 表示第 `i` 次操作为：

- `type` 等于 0 时，将节点值范围在 `[x, y]` 的节点均染蓝
- `type` 等于 1 时，将节点值范围在 `[x, y]` 的节点均染红

请返回完成所有染色后，该二叉树中红色节点的数量。

例子：

```
输入：root = [4,2,7,1,null,5,null,null,null,null,6]
ops = [[0,2,2],[1,1,5],[0,4,5],[1,5,7]]

输出：5

解释：
第 0 次操作，将值为 2 的节点染蓝；
第 1 次操作，将值为 1、2、4、5 的节点染红；
第 2 次操作，将值为 4、5 的节点染蓝；
第 3 次操作，将值为 5、6、7 的节点染红；
因此，最终值为 1、2、5、6、7 的节点为红色节点，返回数量 5
```

<img src="https://pic.leetcode-cn.com/1649833763-BljEbP-image.png" alt="image.png" style="zoom:50%;" />



**注意：**

- 题目保证对于每个操作的 `x`、`y` 值定出现在二叉搜索树节点中

数据范围：

- `1 <= 二叉树节点数量 <= 10^5`
- `1 <= ops.length <= 10^5`
- `ops[i].length == 3`
- `ops[i][0]` 仅为 `0` or `1`
- `0 <= ops[i][1] <= ops[i][2] <= 10^9`
- `0 <= 节点值 <= 10^9`

**思路**：区间更新和区间查询

二叉搜索树的节点值不同，其数据范围是`0 <= 节点值 <= 10^9`，节点个数是`1 <= 二叉树节点数量 <= 10^5`，首先需要对节点值进行离散化处理。将所有节点值从小到大排序后，离散化到1~n之间。

线段树某个节点的区间维护的是该区间内的红色节点的数量。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */

class Node { // 定义线段树
    int l, r; // 维护区间的左右边界 
    int sum; // 维护区间中红色节点的数量
    int tadd = -1; // 懒标签, 0-表示将区间中所有节点染成蓝色，1表示染成红色, -1表示懒标记清空
    Node(int ll, int rr) {l = ll; r = rr;}
    Node(int ll, int rr, int s) {l = ll; r = rr; sum = s;}
}

class Solution {

    List<Integer> arr = new ArrayList<>();
    Map<Integer, Integer> map = new HashMap<>();
    
    int n;
    int[] w; // 权值序列, w[i]=0表示节点i是蓝色
    Node[] tr; // 线段树是一颗完全二叉树，用一个一维数组来存储

    public void dfs(TreeNode u) {
        if(u.left != null) dfs(u.left);
        if(u.right != null) dfs(u.right);
        arr.add(u.val);
    }

    public void pushup(int u) { // 用u的左右孩子更新u的信息
        tr[u].sum = tr[u << 1].sum + tr[u << 1 | 1].sum;
    }

    public void pushdown(int u) { // 懒标记下传，只下传一层。依据懒标记的信息，更新u的左右子节点
        if(tr[u].tadd == -1) return; // u的懒标签是清空状态
        if(tr[u].l == tr[u].r) {tr[u].tadd = -1; return;} // 如果u是叶子节点，它没有子节点，直接清空懒标记
        tr[u << 1].sum = (tr[u << 1].r - tr[u << 1].l + 1) * tr[u].tadd; // 左子树
        tr[u << 1 | 1].sum = (tr[u << 1 | 1].r - tr[u << 1 | 1].l + 1) * tr[u].tadd; // 右子树
        tr[u << 1].tadd = tr[u].tadd; // 更新左子树tadd
        tr[u << 1 | 1].tadd = tr[u].tadd; // 更新右子树的tadd
        tr[u].tadd = -1; // 清空自己懒标记
    }

    public void build(int u, int l, int r) { // 用权值序列w的区间[l,r]构建以u为根节点的子树
        if(l == r) tr[u] = new Node(l, r, w[l]); // 如果[l,r]是单个元素，对应的节点是叶子节点，则直接构建
        else {
            tr[u] = new Node(l, r);
            int mid = l + r >> 1;
            build(u << 1, l, mid); // 构建u的左子树
            build(u << 1 | 1, mid + 1, r); // 构建u的右子树
            pushup(u);
        }
    }

    // 在更新之前，先将之前的懒标记下传pushdown(u) ->递归更新modify(u) -> pushup(u)
    public void modify(int u, int l, int r, int v) { // 将区间[l,r]之间的节点都染成v, 0是蓝色, 1是红色
        if(tr[u].l >= l && tr[u].r <= r) { // 如果u表示的区间包含在修改区间[l,r]中
            tr[u].sum = (tr[u].r - tr[u].l + 1) * v;
            tr[u].tadd = v; // 更新懒标记
        } else {
            pushdown(u); // 懒标记下传
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, v);
            if(r > mid) modify(u << 1 | 1, l, r, v);
            pushup(u);
        }
    }

    public int query(int u, int l, int r) {
        if(tr[u].l >= l && tr[u].r <= r) return tr[u].sum; // u节点表示的区间包含在查询区间[l,r]
        pushdown(u);
        int res = 0;
        int mid = tr[u].l + tr[u].r >> 1;
        if(l <= mid) res += query(u << 1, l, r);
        if(r > mid) res += query(u << 1 | 1, l, r);
        return res;
    }

    public int getNumber(TreeNode root, int[][] ops) {
        // 离散化
        dfs(root);
        Collections.sort(arr);
        for(int i = 0; i < arr.size(); i ++ ) map.put(arr.get(i), i + 1); // 将所有节点值离散化到1~n

        n = arr.size();
        w = new int[n + 1]; // 所有借点初始状态都是蓝色0, 下标从1开始
        tr = new Node[4 * n];

        // 构建线段树
        build(1, 1, n); // 完全二叉树的根节点编号必须从1开始

        // 区间修改
        for(int i = 0; i < ops.length; i ++ ) {
            int v = ops[i][0], l = ops[i][1], r = ops[i][2];
            modify(1, map.get(l), map.get(r), v);
        }

        return query(1, 1, n);
    }
}
```

#### （3）[6468. 统计没有收到请求的服务器数目](https://leetcode.cn/problems/count-zero-request-servers/)

**问题**：

给你一个整数 `n` ，表示服务器的总数目，再给你一个下标从 **0** 开始的 **二维** 整数数组 `logs`，其中 `logs[i] = [server_id, time]` 表示id 为 `server_id` 的服务器在 `time` 时收到了一个请求。

同时给你一个整数 `x` 和一个下标从 **0** 开始的整数数组 `queries` 。

请你返回一个长度等于 `queries.length` 的数组 `arr` ，其中 `arr[i]` 表示在时间区间 `[queries[i] - x, queries[i]]` 内没有收到请求的服务器数目。

注意时间区间是个闭区间。

例子：

```
输入：n = 3, logs = [[1,3],[2,6],[1,5]], x = 5, queries = [10,11]
输出：[1,2]
解释：
对于 queries[0]：id 为 1 和 2 的服务器在区间 [5, 10] 内收到了请求，所以只有服务器 3 没有收到请求。
对于 queries[1]：id 为 2 的服务器在区间 [6,11] 内收到了请求，所以 id 为 1 和 3 的服务器在这个时间段内没有收到请求。
```

**数据范围**：

- `1 <= n <= 60`
- `1 <= logs.length <= 10^5`
- `1 <= queries.length <= 10^5`
- `logs[i].length == 2`
- **`1 <= logs[i][0] <= n`**
- `1 <= logs[i][1] <= 10^6`
- `1 <= x <= 10^5`
- `x < queries[i] <= 10^6`

**思路**：

题目给定了一组日志记录 $logs$，$logs[i]$表示编号为$logs[i][0]$的服务器在时刻$logs[i][1]$收到了一个请求。服务器的集群数量为 $n, 1<=n<=60$。给定一组查询$queries$，要求查询时间区间 $[queries[i]-x, queries[i]]$内没有收到请求的服务器。

所有问题可以看做一个**单点修改、区间查询**的问题。考虑使用线段树或者树状数组来解决

其中，线段树的节点维护了一段时间区间，以及在该时间区间内收到请求的服务器编号的集合，**该集合包含不重复的元素**。

在线段树的单点修改和区间查询涉及到**集合合并操作**。设单点修改的数量为 $P$，查询的个数为 $K$，涉及到总时间区间长度为 $D$，在**不考虑集合合并操作的时间复杂度下**，总的时间复杂度为 $O(PlogD+KlogD)$。

为了使得集合合并操作复杂度尽可能的小，在服务器规模 $n$ 比较小的情况下，我们可以考虑**采用二进制表示集合**，**位运算实现集合合并和新增元素操作**，这些集合操作复杂度为 $O(1)$。`long`的数据范围为$2^{64}$，因此二进制可以表示的集合大小为 $64$。

但是，在服务器规模 $n$ 比较大的情况下，即使使用大整数实现二进制表示集合，但是其集合操作时间复杂度不是常数复杂度，使得算法总的复杂度较高，此时可以考虑使用其他算法来解决此问题。

```java
class Node {
    int l, r;
    long sum; // 二进制表示集合
    Node(int ll, int rr) {l = ll; r = rr;}
    Node(int ll, int rr, int s) {l = ll; r = rr; sum = s;}
}

class Solution {
    
    int N = 0;
    int[] w;
    Node[] tr;
    
    public void pushup(int u) {
        tr[u].sum = tr[u << 1].sum | tr[u << 1 | 1].sum; // 集合合并
    }
    
    public void build(int u, int l, int r) {
        if(l == r) tr[u] = new Node(l, r, w[l]);
        else {
            tr[u] = new Node(l, r);
            int mid = l + r >> 1;
            build(u << 1, l, mid);
            build(u << 1 | 1, mid + 1, r);
            pushup(u);
        }
    }
    
    public void modify(int u, int x, int v) {
        if(tr[u].l == tr[u].r &&(((tr[u].sum >> v) & 1) == 0)) tr[u].sum += 1 << v; // 如何集合中不包此元素，则向集合中添加元素
        else {
            int mid = tr[u].l + tr[u].r >> 1;
            if(x <= mid) modify(u << 1, x, v);
            else modify(u << 1 | 1, x, v);
            pushup(u);
        }
    }
    
    public long query(int u, int l, int r) {
        if(tr[u].l >= l && tr[u].r <= r) return tr[u].sum;
        int mid = tr[u].l + tr[u].r >> 1;
        long res = 0; // 集合合并
        if(l <= mid) res |= query(u << 1, l, r);
        if(r > mid) res |= query(u << 1 | 1, l, r);
        return res;
    }
    
    public int count(long x) {
        int res = 0;
        while(x != 0) {
            if((x & 1) == 1) res ++;
            x >>= 1;
        }
        return res;
    }
           
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        for(int i = 0; i < logs.length; i ++ ) N = Math.max(N, logs[i][1]);
        for(int i = 0; i < queries.length; i ++) N = Math.max(N, queries[i]);
        
        w = new int[N + 10];
        tr = new Node[4 * (N + 10)];
        
        build(1, 1, w.length - 1);
        
        for(int i = 0;  i < logs.length; i ++ ) {
            int v = logs[i][0], y = logs[i][1];
            modify(1, y, v);
        }

        int[] res = new int[queries.length];
        for(int i = 0; i < queries.length; i ++ ) 
            res[i] = n - count(query(1, queries[i] - x, queries[i]));
        return res;
    }
}
```

#### （4）[2569. 更新数组后处理求和查询](https://leetcode.cn/problems/handling-sum-queries-after-update/) 

### 2、扫描线

#### （1）[1094. 拼车](https://leetcode.cn/problems/car-pooling/)



#### （2）[1851. 包含每个查询的最小区间](https://leetcode.cn/problems/minimum-interval-to-include-each-query/)



#### （3）[850. 矩形面积 II](https://leetcode.cn/problems/rectangle-area-ii/)（结合线段树）

**问题**

给你一个轴对齐的二维数组 `rectangles` 。 对于 `rectangle[i] = [x1, y1, x2, y2]`，其中（x1，y1）是矩形 `i` 左下角的坐标， `(xi1, yi1)` 是该矩形 **左下角** 的坐标， `(xi2, yi2)` 是该矩形 **右上角** 的坐标。

计算平面中所有 `rectangles` 所覆盖的 **总面积** 。任何被两个或多个矩形覆盖的区域应只计算 **一次** 。

返回 ***总面积*** 。因为答案可能太大，返回 `10^9 + 7` 的 **模** 。

例子

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/06/rectangle_area_ii_pic.png" alt="img" style="zoom: 33%;" />

```
输入：rectangles = [[0,0,2,2],[1,0,2,3],[1,0,3,1]]
输出：6
解释：如图所示，三个矩形覆盖了总面积为 6 的区域。
从(1,1)到(2,2)，绿色矩形和红色矩形重叠。
从(1,0)到(2,3)，三个矩形都重叠。
```

**数据范围**：

- `1 <= rectangles.length <= 200`
- `rectanges[i].length = 4`
- `0 <= xi1, yi1, xi2, yi2 <= 10^9`

**思路**：离散化+二分+线段树+扫描线，模板题

```java
class Segment {
    int x, y1, y2;
    int k;
    public Segment(int x, int y1, int y2, int k) {
        this.x = x;
        this.y1 = y1;
        this.y2 = y2;
        this.k = k;
    }
}

class Node {
    int l, r;
    int cnt, len;
    public Node(int left, int right) {
        l = left;
        r = right;
    }
}

class Solution {

    final int N = 210, MOD = (int)1e9 + 7;
    Segment[] segs;
    Node[] tr;
    int[] alls;

    public int find(int[] a, int x) {
        int l = 0, r = a.length;
        while(l < r) {
            int mid = l + r >> 1;
            if(a[mid] >= x) r = mid;
            else l = mid + 1;
        }
        return r; // 离散化到0,...
    }

    public int unique(int[] a, int st, int ed) {
        int j = 0; // 不相同元素的个数
        for(int i = st; i <= ed; i ++) 
            if(i == 0 || a[i] != a[i - 1])
                a[j ++] = a[i];
        return st + j;
    }

    public void pushup(int u) { // 用u的孩子信息更新u
        if(tr[u].cnt > 0) tr[u].len = alls[tr[u].r + 1] - alls[tr[u].l]; // 用u自己的信息更新
        else if(tr[u].l == tr[u].r) tr[u].len = 0;
        else tr[u].len = tr[u << 1].len + tr[u << 1 | 1].len; // 用孩子的信息更新u的信息
    }

    public void build(int u, int l, int r) {
        tr[u] = new Node(l, r);
        if(l == r) return ; // 叶子节点返回
        int mid = l + r >> 1;
        build(u << 1, l, mid); // 递归构建左右孩子
        build(u << 1 | 1, mid + 1, r);
    }

    public void modify(int u, int l, int r, int k) {
        if(tr[u].l >= l && tr[u].r <= r) { // 区间[tr[u].l, tr[u].r] 被包含在 [l, r] 中
            tr[u].cnt += k;
            pushup(u);
        } else {
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, k);
            if(r > mid) modify(u << 1 | 1, l, r, k);
            pushup(u);
        }
    }

    public int rectangleArea(int[][] rectangles) {
        int n = rectangles.length;
        segs = new Segment[n * 2 + 10];
        tr = new Node[n * 8 + 10];
        alls = new int[n * 2];

        int m = 0, idx = 0;
        for(int i = 0; i < n; i ++ ) {
            int x1 = rectangles[i][0], y1 = rectangles[i][1];
            int x2 = rectangles[i][2], y2 = rectangles[i][3];
            segs[m ++] = new Segment(x1, y1, y2, 1);
            segs[m ++] = new Segment(x2, y1, y2, -1);
            alls[idx ++] = y1; alls[idx ++] = y2;
        }

        Arrays.sort(segs, 0, m, (a, b) -> a.x - b.x);

        Arrays.sort(alls); //sort
        int ed = unique(alls, 0, alls.length - 1); // unique
        alls = Arrays.copyOfRange(alls, 0, ed); // erase

        build(1, 0, alls.length);

        int res = 0;
        for(int i = 0; i < m; i ++ ) {
            if(i != 0) res = (int)((res + (long)tr[1].len * (segs[i].x - segs[i - 1].x)) % MOD);
            modify(1, find(alls, segs[i].y1), find(alls, segs[i].y2) - 1, segs[i].k);
        }
        return res;
    }
}
```

#### （4）[218. 天际线问题](https://leetcode.cn/problems/the-skyline-problem/) （结合线段树+离散化）

**问题**

城市的 **天际线** 是从远处观看该城市中所有建筑物形成的轮廓的外部轮廓。给你所有建筑物的位置和高度，请返回 *由这些建筑物形成的 **天际线*** 。

每个建筑物的几何信息由数组 `buildings` 表示，其中三元组 `buildings[i] = [lefti, righti, heighti]` 表示：

- `lefti` 是第 `i` 座建筑物左边缘的 `x` 坐标。
- `righti` 是第 `i` 座建筑物右边缘的 `x` 坐标。
- `heighti` 是第 `i` 座建筑物的高度。

你可以假设所有的建筑都是完美的长方形，在高度为 `0` 的绝对平坦的表面上。

**天际线** 应该表示为由 “关键点” 组成的列表，格式 `[[x1,y1],[x2,y2],...]` ，并按 **x 坐标** 进行 **排序** 。**关键点是水平线段的左端点**。列表中最后一个点是最右侧建筑物的终点，`y` 坐标始终为 `0` ，仅用于标记天际线的终点。此外，任何两个相邻建筑物之间的地面都应被视为天际线轮廓的一部分。

**注意：**输出天际线中不得有连续的相同高度的水平线。例如 `[...[2 3], [4 5], [7 5], [11 5], [12 7]...]` 是不正确的答案；三条高度为 5 的线应该在最终输出中合并为一个：`[...[2 3], [4 5], [12 7], ...]`.

例子

<img src="https://assets.leetcode.com/uploads/2020/12/01/merged.jpg" alt="img" style="zoom: 33%;" />

```
输入：buildings = [[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
输出：[[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
解释：
图 A 显示输入的所有建筑物的位置和高度，
图 B 显示由这些建筑物形成的天际线。图 B 中的红点表示输出列表中的关键点。
```

**数据范围：**

- `1 <= buildings.length <= 10^4`
- `0 <= lefti < righti <= 2^31 - 1`
- `1 <= heighti <= 2^31 - 1`
- `buildings` 按 `lefti` 非递减排序

**思路**：**离散化+二分+线段树+扫描线**，模板题

但是，需要注意一下两种类型用例

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-07-18%2021.30.01%E7%9A%84%E5%89%AF%E6%9C%AC.png" alt="截屏2023-07-18 21.30.01的副本" style="zoom: 20%;" />

左边的用例的特点，两个高度相同的矩形相邻且不重叠；右边的用例的特点，两个高度不相同的矩形相邻且不重叠。

```java
class Segment {
    int x, y1, y2;
    int k;
    public Segment(int x, int y1, int y2, int k) {
        this.x = x;
        this.y1 = y1;
        this.y2 = y2;
        this.k = k;
    }
}

class Node {
    int l, r;
    int cnt, len;
    public Node(int l, int r) {
        this.l = l; this.r = r;
    }
}

class Solution {
    final int N = 10010;
    Segment[] segs;
    Node[] tr;
    int[] alls;

    public int unique(int[] a, int st, int ed) {
        int j = 0;
        for(int i = st; i <= ed; i ++)
            if(i == 0 || a[i] != a[i - 1])
                a[j ++] = a[i];
        return st + j;
    }

    public int find(int[] a, int x) {
        int l = 0, r = a.length - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(a[mid] >= x) r = mid;
            else l = mid + 1;
        }
        return r; // 映射到0,....
    }

    public void pushup(int u) {
        if(tr[u].cnt > 0) tr[u].len = alls[tr[u].r + 1] - alls[tr[u].l];
        else if(tr[u].l == tr[u].r) tr[u].len = 0;
        else tr[u].len = tr[u << 1].len + tr[u << 1 | 1].len; 
    }

    public void build(int u, int l, int r) {
        tr[u] = new Node(l, r);
        if(l == r) return ;
        int mid = l + r >> 1;
        build(u << 1, l, mid);
        build(u << 1 | 1, mid + 1, r);
    }

    public void modify(int u, int l, int r, int k) {
        if(tr[u].l >= l && tr[u].r <= r) {
            tr[u].cnt += k;
            pushup(u);
        } else {
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, k);
            if(mid < r) modify(u << 1 | 1, l, r, k);
            pushup(u);
        }
    }

    public List<List<Integer>> getSkyline(int[][] buildings) {
        int n = buildings.length;
        segs = new Segment[n * 2 + 10];
        tr = new Node[n * 8 + 10];
        alls = new int[n + 1];

        int m = 0, idx = 1;
        for(int i = 0; i < n; i ++) {
            int x1 = buildings[i][0], y1 = 0;
            int x2 = buildings[i][1], y2 = buildings[i][2];
            segs[m ++] = new Segment(x1, y1, y2, 1);
            segs[m ++] = new Segment(x2, y1, y2, -1);
            alls[idx ++] = y2;
        }

        Arrays.sort(segs, 0, m, (a, b) -> (a.x - b.x));

        Arrays.sort(alls); // sort
        int ed = unique(alls, 0, alls.length - 1); // unique
        alls = Arrays.copyOfRange(alls, 0, ed); // erase

        build(1, 0, alls.length);

        List<List<Integer>> res = new ArrayList<>();

        int last = 0;
        for(int i = 0; i < m; i ++) {
            if(i != 0) {
                if(segs[i].x != segs[i - 1].x && last != tr[1].len) {
                    List<Integer> temp = new ArrayList<>();
                    temp.add(segs[i - 1].x);
                    temp.add(tr[1].len);
                    res.add(temp);
                    last = tr[1].len;
                }
            }
            modify(1, find(alls, segs[i].y1), find(alls, segs[i].y2) - 1, segs[i].k);
        }

        List<Integer> temp = new ArrayList<>();
        temp.add(segs[m - 1].x);
        temp.add(0);
        res.add(temp);
        return res;
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
