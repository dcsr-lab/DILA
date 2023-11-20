# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分之Tarjan算法



#### 1、[1192. 查找集群内的关键连接](https://leetcode.cn/problems/critical-connections-in-a-network/) （求割边）

**问题**：

力扣数据中心有 `n` 台服务器，分别按从 `0` 到 `n-1` 的方式进行了编号。它们之间以 **服务器到服务器** 的形式相互连接组成了一个内部集群，连接是无向的。用  `connections` 表示集群网络，`connections[i] = [a, b]` 表示服务器 `a` 和 `b` 之间形成连接。任何服务器都可以直接或者间接地通过网络到达任何其他服务器。

**关键连接** 是在该集群中的重要连接，假如我们将它移除，便会导致某些服务器无法访问其他服务器。

请你以任意顺序返回该集群内的所有 **关键连接** 。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/critical-connections-in-a-network.png" alt="img" style="zoom:50%;" />

```
输入：n = 4, connections = [[0,1],[1,2],[2,0],[1,3]]
输出：[[1,3]]
解释：[[3,1]] 也是正确的。
```

**数据范围**：

- `2 <= n <= 10^5`
- `n - 1 <= connections.length <= 10^5`
- `0 <= ai, bi <= n - 1`
- `ai != bi`
- 不存在重复的连接

**思路**：使用**Tarjan算法**求解图中割边的模板题

```java
class Solution {

    int[] dfn = new int[100010]; // dfn[i]是访问i的时间戳, 同时用于判重
    int[] low = new int[100010]; // low[i]表示下标对应的节点不通过其父节点就能访问到的最早被访问到的节点的时间戳

    int[] h = new int[100010], e = new int[200010], ne = new int[200010];
    int idx = 0, t = 1;
    
    List<List<Integer>> edges = new ArrayList<>(); // 存储所有的割边

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public void tarjan(int u, int p) { // p是u的父节点
        dfn[u] = low[u] = t ++ ; // 一开始状态节点不通过父节点能访问的最早的节点只能是它自己

        for(int i = h[u]; i != -1; i = ne[i]) { // 遍历u的邻接节点
            int v = e[i];
            if(v == p) continue; // v不能是u的父节点，因为u->v已经遍历过了

            if(dfn[v] == 0) { // v还没有被访问过
                tarjan(v, u); // 访问v
                low[u] = Math.min(low[u], low[v]); // 回溯
                if(low[v] > dfn[u]) edges.add(Arrays.asList(new Integer[]{u, v}));
            //访问到下一个节点不是父节点，但又是已经被访问过的，所以该节点的时间戳肯定小于当前节点，该节点也必是当前节点的父节点的祖先
            } else if(dfn[v] < low[u]) low[u] = dfn[v]; // fdn[v]已经计算过了，用fdn[v]更新low[u]
        }
    }

    public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
        // 初始化
        Arrays.fill(h, -1); // 初始化头指针数组
        for(int i = 0; i < connections.size(); i ++ ) {
            int a = connections.get(i).get(0), b = connections.get(i).get(1);
            add(a, b); add(b, a);
        }
        // 遍历图的所有节点, 选定一个点作为根节点来进行dfs遍历, 但由于图可能不是连通图, 故外面要套一个for,
        for(int i = 0; i < n; i ++) 
            if(dfn[i] == 0) tarjan(i, -1);
        return edges;
    }
}
```

#### 2、[LCP 54. 夺回据点](https://leetcode.cn/problems/s5kipK/) （点双连通分量缩点+求割点+求点双）

**问题**：

欢迎各位勇者来到力扣城，本次试炼主题为「夺回据点」。

魔物了占领若干据点，这些据点被若干条道路相连接，`roads[i] = [x, y]` 表示编号 `x`、`y` 的两个据点通过一条道路连接。

现在勇者要将按照以下原则将这些据点逐一夺回：

- 在开始的时候，勇者可以花费资源先夺回一些据点，初始夺回第 `j` 个据点所需消耗的资源数量为 `cost[j]`
- 接下来，勇者在**不消耗资源**情况下，每次可以夺回**一个**和「已夺回据点」相连接的魔物据点，并对其进行夺回

注：为了防止魔物暴动，勇者在每一次夺回据点后（包括花费资源夺回据点后），需要**保证剩余的所有魔物据点之间是相连通的（不经过「已夺回据点」）。**

请返回勇者夺回所有据点需要消耗的**最少资源数量**。

例子

> 输入：
> cost = [1,2,3,4,5,6]
> roads = [[0,1],[0,2],[1,3],[2,3],[1,2],[2,4],[2,5]]
>
> 输出：6
>
> 解释：
> 勇者消耗资源 6 夺回据点 0 和 4，魔物据点 1、2、3、5 相连通；
> 第一次夺回据点 1，魔物据点 2、3、5 相连通；
> 第二次夺回据点 3，魔物据点 2、5 相连通；
> 第三次夺回据点 2，剩余魔物据点 5；
> 第四次夺回据点 5，无剩余魔物据点；
> 因此最少需要消耗资源为 6，可占领所有据点。
>
> <img src="https://pic.leetcode-cn.com/1648706944-KJstUN-image.png" alt="image.png" style="zoom:50%;" />

**数据范围**：

- `1 <= roads.length, cost.length <= 10^5`
- `0 <= roads[i][0], roads[i][1] < cost.length`
- `1 <= cost[i] <= 10^9`
- 输入保证初始所有据点都是连通的，且不存在重边和自环

**思路**：点双连通分量缩点

**问题简化**

首先考虑简化问题：题目**给出的是一棵树**，并按要求夺回据点。

容易发现，一开始**只能选择叶子节点**（选择非叶子节点树直接不连通），而且这颗树有 $k$ 个叶子节点，我们至少选择其中 $k - 1$ 个，因此我们选择权值最小的 $k-1$个叶子即可。

**原问题**：**将所有点双连通分量缩点到一棵树，题转换到树上情况**，每个点的权值是该双连通分量中非割点权值的最小值。

点双联通缩点之后，只能选叶子。 假设缩点后共有 $k$ 个叶子，每个叶子的权重是集合中权值最小的点，然后只取前 $k - 1$ 小即可。 另外需要注意**缩点后仅有一个点**的特殊情况（当原图是点双连通图时，缩点后就只有一个点）。

此做法的正确性建立在：

- 考虑到一个单独的点双连通分量，一开始只需要选一个点，就能合法地占领整个点双连通分量。
- 点双连通分量缩点后得到一定是一颗树（圆方树）
- 缩点后得到的叶子一定不是原图中的割点。

点双联通分量内部满足任意两个点都在某个环上（用割点处理），而边双连通分量保证每条边都在某个环上（用割边处理）。这里用**点双缩点**的原因是，**如果初始选择的点在割点上，就会使得魔物分成两个连通块，所以首先应该想到的就是筛割点，然后缩点后简化成一个树去处理**。

```java
class Solution {
    int[] dfn = new int[100010]; // fnd[i]表示访问i时的时间戳, 判重数组
    int[] low = new int[100010]; // low[i]表示i能访问的最早时间戳

    int[] stk = new int[100010]; // 模拟栈
    int t = 1, tt = 0; // tt是栈顶指针

    int[] h = new int[100010], e = new int[200010], ne = new int[200010];
    int idx = 0;

    boolean[] cut = new boolean[100010]; // 割点
    List<List<Integer>> vdccs = new ArrayList<>(); // 点双连通分量

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public void tarjan(int u, int p, int root) { // p是u的父节点
        stk[++ tt] = u; // u第一次被访问, u入栈
        dfn[u] = low[u] = t ++ ;

        int kids = 0; // 以u为根结点的子树的个数
        for(int i = h[u]; i != -1; i = ne[i]) { // 遍历u的所有邻接节点
            int v = e[i];
            if(v == p) continue;

            if(dfn[v] == 0) { // v还没被访问过
                kids ++; tarjan(v, u, root);
                low[u] = Math.min(low[u], low[v]); // 通过low[v] 更新 low[u]
                // 对于一个根节点, 如果它有两个或者两个以上的子树, 那么去掉根节点这几颗子树就不连通了, 故这时可以判定根节点是割点
                if((u == root && kids >= 2) || (u != root && low[v] >= dfn[u])) cut[u] = true; // u是割点
                if (low[v] >= dfn[u]) {  // v无法到达u的祖宗节点，也可以到达u, 必须经过u才能到达u的祖宗节点
                    List<Integer> vdcc = new ArrayList<>(); // 点连通分量
                    do vdcc.add(stk[tt --]); while(tt != 0 && stk[tt + 1] != v); // 从栈顶不断弹出节点直到v弹出
                    vdcc.add(u); // 这些点和u一起构成一个点双连通分量
                    vdccs.add(vdcc);
                } 
            } else if(dfn[v] < low[u]) low[u] = dfn[v]; // fdn[v]已经计算过了，用fdn[v]更新low[u]
        }
    }

    public long minimumCost(int[] cost, int[][] roads) {
        int n = cost.length;
        Arrays.fill(h, -1);

        for(int i = 0; i < roads.length; i ++ ) {
            int a = roads[i][0], b = roads[i][1];
            add(a, b); add(b, a);
        }

        for(int i = 0; i < n; i ++) // 原图有可能不是连通图
            if(dfn[i] == 0) tarjan(i, -1, i);

        long sum = 0, mx = Integer.MIN_VALUE;
        for(int i = 0; i < vdccs.size(); i ++ ) {
            int fee = Integer.MAX_VALUE, cnt = 0;
            List<Integer> arr = vdccs.get(i);

            for(int j = 0; j < arr.size(); j ++ ) {
                int a = arr.get(j);
                if(!cut[a]) fee = Math.min(fee, cost[a]);
                else cnt ++;
            }
            // 如果包含 > 1 个割点，那么该集合不是割点后的叶子结点，不予考虑
            if(cnt <= 1 && fee != Integer.MAX_VALUE) {
                mx = Math.max(mx, fee);  
                sum += fee;
            }
        }

        if(vdccs.size() == 1) return mx; // 该图就是点双连通图，没有割点
        return sum - mx; // 所有费用总和-费用最高的
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
