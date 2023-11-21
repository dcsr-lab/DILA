# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  



# 第三部分之最短路问题



### 1、 最短路问题

#### （1） 6032. 得到要求路径的最小带权子图:writing_hand: 

**问题**：

给你一个整数 `n` ，它表示一个 **带权有向** 图的节点数，节点编号为 `0` 到 `n - 1` 。

同时给你一个二维整数数组 `edges` ，其中 `edges[i] = [fromi, toi, weighti]` ，表示从 `fromi` 到 `toi` 有一条边权为 `weighti` 的 **有向** 边。

最后，给你三个 **互不相同** 的整数 `src1` ，`src2` 和 `dest` ，表示图中三个不同的点。

请你从图中选出一个 **边权和最小** 的子图，使得从 `src1` 和 `src2` 出发，在这个子图中，都 **可以** 到达 `dest` 。如果这样的子图不存在，请返回 `-1` 。

**子图** 中的点和边都应该属于原图的一部分。子图的边权和定义为它所包含的所有边的权值之和。

<img src="https://assets.leetcode.com/uploads/2022/02/17/example1drawio.png" alt="img" width="300px" />

**注意**：

- `3 <= n <= 105`
- `0 <= edges.length <= 105`
- `fromi != toi`

**稀疏图**且**无自环、有重边**

**思路**：**堆优化版 Dijkstra** , 时间复杂度$O(mlogm)$ 

从 $\text{src}_1$ 和 $\text{src}_2$ 都可以到达 $\text{dest}$ 的子图，一定是先从 $\text{src}_1 \rightarrow \text{mid}$，$\text{src}_2 \rightarrow \text{mid}$，然后再一起从 $\text{mid}$ 出发，到达 $\text{dest}$ 的形式，如下图：



<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203141054521.png" alt="image.png" width="280px"/>

 

证明：若路径 $\text{src}_1 \rightarrow \text{dest}$ 与 $\text{src}_2 \rightarrow \text{dest}$ 的第一个交点 是 $\text{mid}$，那么一起从 $mid$ 走到 $dest$ 是最优的，因为如果不一起走，那么从 $mid$ 到 $dest$ 就存在多条路径，此时只需选择一条最短的路径一起走，代价一定小于多条路径的权值和。

- 枚举中间节点 $mid=i$，利用 $dijkstra$ 算法，计算
- $dist_1[i]=$ 从$src_1$ 出发，到达节点 $i$ 的距离；
- $dist_2[i]=$ 从 $src_2$出发，到达节点 $i$ 的距离；
- $dist_3[i]=$ 从 $dest$ 出发，沿着 反向边，到达节点 $i$ 的距离。
- 那么，答案就是 $$\min _{0 \leq i \leq n-1}\left(\operatorname{dist}_{1}[i]+\operatorname{dist}_{2}[i]+\operatorname{dist}_{3}[i]\right)$$

```java
class Pair {
    long d; // 结点到源点的最短距离
    int id; // 结点编号
    Pair(long d, int id) {
        this.d = d;
        this.id = id;
    }
}

class Solution {

    final int N = 100010;
    int[] h = new int[N], e = new int[N], ne = new int[N], w = new int[N]; // 邻接表, 存储稀疏图
    int idx = 0;

    public void add(int a, int b, int c) { // 向邻接表中添加一条a->b的边, 边权是c
        e[idx] = b; ne[idx] = h[a]; w[idx] = c; h[a] = idx ++ ;
    }
    
    public long[] dijkstra(int src, int n) { // 堆优化版dijkstra, 求src到其余各大的最短路
        long[] dist = new long[n]; // dist[i] 表示i到src的最短路
        boolean[] st = new boolean[n]; // 判重数组, st[i]为true表示i到src的最短路已经确定
        // 小根堆, 维护所有没有确定最短路的结点, 堆顶元素表示没有确定最短的结点中距离src最近的点
        PriorityQueue<Pair> heap = new PriorityQueue<>((p1, p2) -> (int)(p1.d - p2.d)); // p1.d和p2.d都大于0,不会溢出

        Arrays.fill(dist, (long)2e15);
        dist[src] = 0L; // 初始化src
        heap.offer(new Pair(0L, src));

        while(!heap.isEmpty()) { // 每次确定一个结点的最短路
            Pair t = heap.poll(); // 取出堆顶元素
            long d = t.d;
            int id = t.id;

            if(st[id]) continue; // id的最短路已经确定
            else st[id] = true; 

            for(int i = h[id]; i != -1; i = ne[i]) { // 用t更新它的所有连接的结点
                int j = e[i];
                if(dist[j] > d + w[i]) {
                    dist[j] = d + w[i];
                    heap.offer(new Pair(dist[j], j));
                }
            }
        }
        return dist;
    }

    public long minimumWeight(int n, int[][] edges, int src1, int src2, int dest) {
        Arrays.fill(h, -1); 
        for(int i = 0; i < edges.length; i ++ ) {
            int a = edges[i][0], b = edges[i][1], c = edges[i][2];
            add(a, b, c); // 有重边, 重边不影响求最短路
        }

        long[] dist1 = dijkstra(src1, n); // 求src1到其余各点的最短路
        long[] dist2 = dijkstra(src2, n); // 求src2到其余各点的最短路

        Arrays.fill(h, -1); idx = 0;
        for(int i = 0; i < edges.length; i ++ ) { // 反向建图
            int a = edges[i][0], b = edges[i][1], c = edges[i][2];
            add(b, a, c);
        }

        long[] dist = dijkstra(dest, n); // 求dest到其余各点的最短路

        long res = (long)2e15;
        for(int i = 0; i < n ; i ++ ) { // 枚举每一个结点作为中间点时, mid->src1,mid->src2,dest->mid的最短路之和
            long d = dist1[i] + dist2[i] + dist[i];
            if(d < (long)2e15)
                res = Math.min(res, d);
        }

        if(res == (long)2e15) return -1;
        return res;
    }
}
```

#### （2）743. 网络延迟时间

**问题**：

有 `n` 个网络节点，标记为 `1` 到 `n`。

给你一个列表 `times`，表示信号经过 **有向** 边的传递时间。 `times[i] = (ui, vi, wi)`，其中 `ui` 是源节点，`vi` 是目标节点， `wi` 是一个信号从源节点传递到目标节点的时间。

现在，从某个节点 `K` 发出一个信号。需要多久才能使所有节点都收到信号？如果不能使所有节点收到信号，返回 `-1` 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203142100595.png" alt="img" width="200px" />

注意：

- `1 <= k <= n <= 100`
- `1 <= times.length <= 6000`
- `ui != vi`
- 所有 `(ui, vi)` 对都 **互不相同**（即，不含重复边）

**稀疏图**、**无重边**、**无自环**

**思路**：求结点 `k` 到其余各点的最短路的最大值

```java
class Pair {
    int d, id;
    Pair(int d, int id) {
        this.d = d;
        this.id = id;
    }
}

class Solution {
    final int N = 6010;
    int[] h = new int[N], e = new int[N], ne = new int[N], w = new int[N]; // 邻接表
    int idx = 0; 
    int[] dist = new int[N]; // dist[i] 表示源点到i的最短路
    boolean[] st = new boolean[N]; // 判重数组, st[i]为true, 源点到i的最短路已经求出

    public void add(int a, int b, int c) { // 向邻接表中增加一条边, 边权为w
        e[idx] = b; ne[idx] = h[a]; w[idx] = c; h[a] = idx ++ ;
    }

    public void dijkstra(int start) {
        Arrays.fill(dist, 0x3f3f3f3f); // 初始化dist数组
        dist[start] = 0;

        PriorityQueue<Pair> heap = new PriorityQueue<>( (p1, p2) -> p1.d - p2.d); // 小根堆
        heap.offer(new Pair(0, start));

        while(!heap.isEmpty()) {
            Pair t = heap.poll();
            int d = t.d, id = t.id;

            if(st[id]) continue;
            else st[id] = true;

            for(int i = h[id]; i != -1; i = ne[i]) { // 用t更新临边
                int j = e[i];
                if(dist[j] > d + w[i]) {
                    dist[j] = d + w[i];
                    heap.offer(new Pair(dist[j], j));
                }
            } 
        }
    }

    public int networkDelayTime(int[][] times, int n, int k) {
        Arrays.fill(h, -1); // 初始化头指针数组
        for(int i = 0; i < times.length; i ++ ) {
            int a = times[i][0], b = times[i][1], c = times[i][2];
            add(a, b, c);
        }

        dijkstra(k);

        int res = 0;
        for(int i = 1; i <= n; i ++ ) { // 求源点到其余点的最短路中的最大值
            if(dist[i] == 0x3f3f3f3f) return -1;
            else res = Math.max(res, dist[i]);
        }
        return res;
    }
}
```

#### （3）[1368. 使网格图至少有一条有效路径的最小代价](https://leetcode.cn/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/)（:o:）

**问题**：

给你一个 m x n 的网格图 `grid` 。 `grid` 中每个格子都有一个数字，对应着从该格子出发下一步走的方向。 `grid[i][j]` 中的数字可能为以下几种情况：

- **1** ，下一步往右走，也就是你会从 `grid[i][j]` 走到 `grid[i][j + 1]`
- **2** ，下一步往左走，也就是你会从 `grid[i][j]` 走到 `grid[i][j - 1]`
- **3** ，下一步往下走，也就是你会从 `grid[i][j]` 走到 `grid[i + 1][j]`
- **4** ，下一步往上走，也就是你会从 `grid[i][j]` 走到 `grid[i - 1][j]`

注意网格图中可能会有 **无效数字** ，因为它们可能指向 `grid` 以外的区域。

一开始，你会从最左上角的格子 `(0,0)` 出发。我们定义一条 **有效路径** 为从格子 `(0,0)` 出发，每一步都顺着数字对应方向走，最终在最右下角的格子 `(m - 1, n - 1)` 结束的路径。有效路径 **不需要是最短路径** 。

你可以花费 `cost = 1` 的代价修改一个格子中的数字，但每个格子中的数字 **只能修改一次** 。

请你返回让网格图至少有一条有效路径的最小代价。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/02/29/grid1.png" alt="img" style="zoom:67%;" />



```
输入：grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]
输出：3
解释：你将从点 (0, 0) 出发。
到达 (3, 3) 的路径为： (0, 0) --> (0, 1) --> (0, 2) --> (0, 3) 花费代价 cost = 1 使方向向下 --> (1, 3) --> (1, 2) --> (1, 1) --> (1, 0) 花费代价 cost = 1 使方向向下 --> (2, 0) --> (2, 1) --> (2, 2) --> (2, 3) 花费代价 cost = 1 使方向向下 --> (3, 3)
总花费为 cost = 3.
```

**数据范围**：

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 100`

**思路**：**堆优化版的dijkstar**

如果没有“每个格子中的数字只能修改一次这个条件”，我们可很轻松得**将本题建模成一个球最短路的问题**：

- 我们希望求出从左上角$(0,0)$到右下角$(m-1,n-1)$的最小代价；
- 当我们在任意位置$(i,j)$上，我们都可以向上、下、左、右移动一个位置，但不能走出边界。如果移动的位置与$(i,j)$处的箭方向一致，那么移动的代价为 $0$，否则为 $1$。
- 这样，我们可以将数组 $grid$建模成一个包含$mn$个节点和不超过$4mn$条边的有向图$G$。图$G$中的每一个节点表示数组$grid$中的一个位置，它会向不超过4个相邻的节点各连一条边，边权的值要么为 $0$（移动方向与箭头方向一致），要么为1（移动方向与箭头方向不一致）；
- 我们在图 $G$上使用任意一种最短路算法（$dijksta, spfa, floyd, bellman \ \ ford$），求出从$(0,0)$到$(m-1, n-1)$的最短路，即可得到答案。

那么在加上了这个条件之后，这样的建模方式还是可行的吗？我们可以发现，一定存在一条最短路径，它只经过每个位置最多一次，这是因为如果一条最短路径能表示成如下的形式：
$$
P_0, P_1, \cdots, P_k, P_{k_{11}}, \cdots, P_{k_{1 x}}, P_k, P_{k_{21}}, \cdots, P_{k_{2 y}}
$$
其中，$P_i$表示某个位置。该路径经过了位置 $P_k$两次，那么我们可以删去这两次$P_k$之间的所有位置以及任意一次 $P_k$得到一条新的最短路径：
$$
P_0, P_1, \cdots, P_k, P_{k_{21}}, \cdots, P_{k_{2 y}}
$$
这条最短路径只经过了位置$P_k$ 一次。用同样的方法，我们可以继续删去最短路径中出现超过一次的位置，最终得到一条只经过每个位置最多一次的最短路径。对于这条路径，无论有没有「每个格子中的数字只能修改一次」这个条件，它都不会受到影响，因此我们仍然可以使用上述的建模方式，求出最短路并作为最终答案。

Dijkstra 算法适合用来求出**无负权边图**中的**单源**最短路径。其中「无负权边」表示图中所有边的权值必须为非负数，「单源最短路径」表示 Dijkstra 算法可以求出从某一个节点到其余所有节点的最短路径。由于我们的建模中，所有边的权值均为 0 或 ，并且我们只需要求出从起点到终点的最短路。

时间复杂度是 $O(mnlog(mn))$，其$n$是节点的个数，$m$是边的数量。

```java
class Solution {

    public int dijkstra(int[][] g, int startX, int startY) {
        int n = g.length, m = g[0].length;
        boolean[][] st = new boolean[n][m]; // 判重数组
        int[][] dist = new int[n][m]; // dist[i][j]表示(i,j)到(start, end)的最小代价

        for(int i = 0; i < n; i ++ ) Arrays.fill(dist[i], 0x3f3f3f3f);
        dist[startX][startY] = 0;

        PriorityQueue<Pair<Integer, int[]>> heap = new PriorityQueue<>(
            (a, b) -> a.getKey() - b.getKey());
        heap.offer(new Pair<Integer, int[]>(0, new int[]{startX, startY}));

        int[] dx = new int[]{0, 0, 1, -1}, dy = new int[]{1, -1, 0, 0};
        while(heap.size() != 0) {
            // 从还没有确定最小代价的节点的集合heap中，取出当前代价最小的节点
            var t = heap.poll(); // 堆顶元素出堆
            Integer d = t.getKey(), x = t.getValue()[0], y = t.getValue()[1];

            if(st[x][y]) continue; // tx,ty已经确定了最小代价
            else st[x][y] = true;

            for(int i = 0; i < dx.length; i ++ ) {
                int tx = x + dx[i], ty = y + dy[i];
                if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue; // 越界
                if(st[tx][ty]) continue;
                if(i + 1 == g[x][y]) dist[tx][ty] = Math.min(dist[tx][ty], d);
                else  dist[tx][ty] = Math.min(dist[tx][ty], d + 1);
                heap.offer(new Pair<Integer, int[]>(dist[tx][ty], new int[]{tx,ty}));
            }
        }
        return dist[n - 1][m - 1];
    }

    public int minCost(int[][] grid) {
        return dijkstra(grid, 0, 0);
    }
}
```

#### （4）[LCP 56. 信物传送](https://leetcode.cn/problems/6UEx57/)

问题：

欢迎各位勇者来到力扣城，本次试炼主题为「信物传送」。

本次试炼场地设有若干传送带，`matrix[i][j]` 表示第 `i` 行 `j` 列的传送带运作方向，`"^","v","<",">"` 这四种符号分别表示 **上、下、左、右** 四个方向。信物会随传送带的方向移动。勇者**每一次**施法操作，可**临时**变更一处传送带的方向，在物品经过后传送带恢复原方向。

<img src="https://pic.leetcode-cn.com/1649835246-vfupSL-lcp%20(2).gif" alt="lcp (2).gif" style="zoom:50%;" />

通关信物初始位于坐标 `start`处，勇者需要将其移动到坐标 `end` 处，请返回勇者施法操作的最少次数。

**注意：**

- `start` 和 `end` 的格式均为 `[i,j]`

**数据范围**：

- `matrix` 中仅包含 `'^'、'v'、'<'、'>'`
- `0 < matrix.length <= 100`
- `0 < matrix[i].length <= 100`
- `0 <= start[0],end[0] < matrix.length`
- `0 <= start[1],end[1] < matrix[i].length`

思路：与[1368. 使网格图至少有一条有效路径的最小代价](https://leetcode.cn/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/) 相同

```java
class Solution {

    public int dijkstar(int[][] g, int[] start, int[] end) {
        int n = g.length, m = g[0].length;
        boolean[][] st = new boolean[n][m];
        int[][] dist = new int[n][m];

        for(int i = 0; i < n; i ++) Arrays.fill(dist[i], 0x3f3f3f3f);
        dist[start[0]][start[1]] = 0;

        PriorityQueue<Pair<Integer, int[]>> heap = new PriorityQueue<>((a ,b) -> a.getKey() - b.getKey());
        heap.offer(new Pair<Integer, int[]>(0, start));

        int[] dx = new int[]{0, 0, 1, -1}, dy = new int[]{1, -1, 0, 0};
        while(heap.size() != 0) {
            var t = heap.poll();
            Integer d = t.getKey(), x = t.getValue()[0], y = t.getValue()[1];

            if(st[x][y]) continue;
            else st[x][y] = true;

            for(int i = 0; i < dx.length; i ++ ) {
                int tx = x + dx[i], ty = y + dy[i];
                if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue;
                if(st[tx][ty]) continue;
                if(i + 1 == g[x][y]) dist[tx][ty] = Math.min(dist[tx][ty], d);
                else dist[tx][ty] = Math.min(dist[tx][ty], d + 1);

                heap.offer(new Pair<Integer, int[]>(dist[tx][ty], new int[]{tx, ty}));
            }            
        }

        return dist[end[0]][end[1]];
    }

    public int conveyorBelt(String[] matrix, int[] start, int[] end) {
        int n = matrix.length, m = matrix[0].length();
        int[][] g = new int[n][m];

        for(int i = 0; i < n; i ++ ) 
            for(int j = 0; j < m; j ++)
                if(matrix[i].charAt(j) == '>') g[i][j] = 1;
                else if(matrix[i].charAt(j) == '<') g[i][j] = 2;
                else if(matrix[i].charAt(j) == 'v') g[i][j] = 3;
                else if(matrix[i].charAt(j) == '^') g[i][j] = 4;

        return dijkstar(g, start, end);
    }
}
```



### 2、次短路问题

#### （1）2045. 到达目的地的第二短时间:writing_hand: 

**问题**：

城市用一个 **双向连通** 图表示，图中有 `n` 个节点，从 `1` 到 `n` 编号（包含 `1` 和 `n`）。图中的边用一个二维整数数组 `edges` 表示，其中每个 `edges[i] = [ui, vi]` 表示一条节点 `ui` 和节点 `vi` 之间的双向连通边。每组节点对由 **最多一条** 边连通，顶点不存在连接到自身的边。穿过任意一条边的时间是 `time` 分钟。

每个节点都有一个交通信号灯，每 `change` 分钟改变一次，从绿色变成红色，再由红色变成绿色，循环往复。所有信号灯都 **同时** 改变。你可以在 **任何时候** 进入某个节点，但是 **只能** 在节点 **信号灯是绿色时** 才能离开。如果信号灯是 **绿色** ，你 **不能** 在节点等待，必须离开。

**第二小的值** 是 **严格大于** 最小值的所有值中最小的值。

- 例如，`[2, 3, 4]` 中第二小的值是 `3` ，而 `[2, 2, 4]` 中第二小的值是 `4` 。
- 给你 `n`、`edges`、`time` 和 `change` ，返回从节点 `1` 到节点 `n` 需要的 **第二短时间** 。

<img src="https://assets.leetcode.com/uploads/2021/09/29/e2.png" alt="img" width="200px" />

```
输入：n = 5, edges = [[1,2],[1,3],[1,4],[3,4],[4,5]], time = 3, change = 5
输出：13
```

**注意：**

- 你可以 **任意次** 穿过任意顶点，**包括** `1` 和 `n` 。
- 你可以假设在 **启程时** ，所有信号灯刚刚变成 **绿色** 。

**解法一**：**堆优化版 Dijkstra**，$O(mlogm)$ 

**思路**：

- 红绿灯的变化对所有的点都是一致的，所以不管走到哪里，时间到了就需要等红绿灯，在这个基础上路径越长肯定等的红绿灯就越多，并且路径长通过的边权也越多，所以按照题意我们需要找到一条次短路径
- 通过时间和红绿灯对于所有节点都是一样的，所以这两个只是干扰项，图仍然可以看作无权图
- 通过**dijkstra 算法**求起点到终点的最短路和**次短路**
- 最后在根据次短路的长度，求从起点到终点的时间

```java
class Pair {
    int d, id, type;  // id是编号, d表示源点到id的距离, type是类型, 0是最短路, 1是次短路
    Pair(int d, int id, int type) {
        this.d = d; this.id = id; this.type = type;
    }
}

class Solution {

    int[] h, e, ne; // 邻接表
    int idx = 0;
    int[] dist, subdist; // dist表示最短路 subdist是次短路
    boolean[][] st; // st[i][0]为true表示源点到i的最短路已经求过, st[i][1]表示次短路已经求过

    public void add(int a, int b) { // 向邻接表添加一条边 a->b 的边
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public int dijkstra(int start, int end) {  // 返回从1到n的次短路
        Arrays.fill(dist, 0x3f3f3f3f); Arrays.fill(subdist, 0x3f3f3f3f);
        dist[start] = 0;
        PriorityQueue<Pair> q = new PriorityQueue<>((p1, p2) -> p1.d - p2.d);
        q.offer(new Pair(0, start, 0));

        while(!q.isEmpty()) {
            Pair t = q.poll();
            int d = t.d, id = t.id, type = t.type;

            if(st[id][type]) continue; // 贪心优化
            else st[id][type] = true;

            for(int i = h[id]; i != -1; i = ne[i]) {
                int j = e[i];
                if(dist[j] > d + 1) {
                    subdist[j] = dist[j]; // 更新次短路
                    dist[j] = d + 1; // 更新最短路
                    q.offer(new Pair(dist[j], j, 0));
                    q.offer(new Pair(subdist[j], j, 1));
                } else if(d + 1 > dist[j] && d + 1 < subdist[j]) {
                    subdist[j] = d + 1; // 更新次短路
                    q.offer(new Pair(subdist[j], j, 1));
                }
            }
        }
        return subdist[end];
    }

    public int secondMinimum(int n, int[][] edges, int time, int change) {
        int m = edges.length;
        h = new int[n + 10]; e = new int[2*m + 10]; ne = new int[2*m + 10];
        dist = new int[n + 10]; subdist = new int[n + 10];
        st = new boolean[n + 10][2];

        Arrays.fill(h, -1); // 初始化头指针数组

        for(int i = 0; i < m; i ++ ) {
            int a = edges[i][0], b = edges[i][1];
            add(a, b); add(b, a); // 无向图
        }

        int d = dijkstra(1, n);

        //计算走了 path[n][1] 步，共需要等待多少红灯和共需要多少时间
        int res = 0;
        for(int i = 0; i < d; i ++ ) {
            //经过 (2 * change) 灯由绿灯变成绿灯，并且维持 change 秒
            //如果 ret 不在该范围到达，就无法到达后立即出发，需要等红灯
            //等待时间为，一个 (2 * change) 周期，减去到达时间
            if(res % (2 * change) >= change) 
                res += (2 * change - res % (2 * change));
            res += time;
        }
        return res;
    }
}
```

**解法二**：**BFS 求最短路**

- 与**Dijkstra 算法**相比, **BFS 求最短路**使用的数据结构是**队列**，所以**不能使用贪心优化**

```java
class Pair {
    int d, id, type;  // id是编号, d表示源点到id的距离, type是类型, 0是最短路, 1是次短路
    Pair(int d, int id, int type) {
        this.d = d; this.id = id; this.type = type;
    }
}

class Solution {

    int[] h, e, ne; // 邻接表
    int idx = 0;
    int[] dist, subdist; // dist表示最短路 subdist是次短路
    boolean[][] st; // st[i][0]为true表示源点到i的最短路已经求过, st[i][1]表示次短路已经求过

    public void add(int a, int b) { // 向邻接表添加一条边 a->b 的边
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public int bfs(int start, int end) {  // 返回从1到n的次短路
        Arrays.fill(dist, 0x3f3f3f3f); Arrays.fill(subdist, 0x3f3f3f3f);
        dist[start] = 0;
        Queue<Pair> q = new ArrayDeque<>();
        q.offer(new Pair(0, start, 0));

        while(!q.isEmpty()) {
            Pair t = q.poll();
            int d = t.d, id = t.id, type = t.type;

            for(int i = h[id]; i != -1; i = ne[i]) {
                int j = e[i];
                if(dist[j] > d + 1) {
                    subdist[j] = dist[j]; // 更新次短路
                    dist[j] = d + 1; // 更新最短路
                    q.offer(new Pair(dist[j], j, 0));
                    q.offer(new Pair(subdist[j], j, 1));
                } else if(d + 1 > dist[j] && d + 1 < subdist[j]) {
                    subdist[j] = d + 1; // 更新次短路
                    q.offer(new Pair(subdist[j], j, 1));
                }
            }
        }
        return subdist[end];
    }

    public int secondMinimum(int n, int[][] edges, int time, int change) {
        int m = edges.length;
        h = new int[n + 10]; e = new int[2*m + 10]; ne = new int[2*m + 10];
        dist = new int[n + 10]; subdist = new int[n + 10];
        st = new boolean[n + 10][2];

        Arrays.fill(h, -1); // 初始化头指针数组

        for(int i = 0; i < m; i ++ ) {
            int a = edges[i][0], b = edges[i][1];
            add(a, b); add(b, a); // 无向图
        }

        int d = bfs(1, n);

        //计算走了 path[n][1] 步，共需要等待多少红灯和共需要多少时间
        int res = 0;
        for(int i = 0; i < d; i ++ ) {
            //经过 (2 * change) 灯由绿灯变成绿灯，并且维持 change 秒
            //如果 ret 不在该范围到达，就无法到达后立即出发，需要等红灯
            //等待时间为，一个 (2 * change) 周期，减去到达时间
            if(res % (2 * change) >= change) 
                res += (2 * change - res % (2 * change));
            res += time;
        }
        return res;
    }
}
```

#### （2）AcWing 1133. 第二短路

**问题**：

贝茜把家搬到了一个小农场，但她常常回到 FJ 的农场去拜访她的朋友。

贝茜很喜欢路边的风景，不想那么快地结束她的旅途，于是她每次回农场，都会选择第二短的路径，而不像我们所习惯的那样，选择最短路。

贝茜所在的乡村有 $R$ 条双向道路，每条路都连接了所有的 $N$ 个农场中的某两个。

贝茜居住在农场 $1$，她的朋友们居住在农场 $N$（即贝茜每次旅行的目的地）。

贝茜选择的第二短的路径中，可以包含任何一条在最短路中出现的道路，并且一条路可以重复走多次。

当然第二短路的长度必须严格大于最短路（可能有多条）的长度，但它的长度必须不大于所有除最短路外的路径的长度。

**解法一**：**堆优化版 dijkstra**， 求最短路和次短路

**思路**：

```java
import java.util.*;

class Pair {
    int d, id, type; // id是编号, d表示源点到id的距离, type是类型, 0是最短路, 1是次短路
    Pair(int d, int id, int type) {
        this.d = d; this.id = id; this.type = type;
    }
}

class Main {
    
    static int[] h, e, ne, w; // 邻接表
    static int idx = 0;
    static int[] dist, subdist; // dist表示最短路 subdist是次短路
    static boolean[][] st; // st[i][0]为true表示源点到i的最短路已经求过, st[i][1]表示次短路已经求过
    static int n, m;
    
    public static void add(int a, int b, int c) { // 向邻接表添加一条边 a->b 的边, 边权为c
        e[idx] = b; ne[idx] = h[a]; w[idx] = c; h[a] = idx ++ ;
    }
    
    public static int dijkstra() { // 返回从1到n的次短路
        Arrays.fill(dist, 0x3f3f3f3f);  Arrays.fill(subdist, 0x3f3f3f3f); 
        dist[1] = 0;
        PriorityQueue<Pair> heap = new PriorityQueue<>((p1, p2) -> p1.d - p2.d);
        heap.offer(new Pair(0, 1, 0));
        
        while(!heap.isEmpty()) {
            Pair t = heap.poll();
            int d = t.d, id = t.id, type = t.type;
            
            if(st[id][type]) continue; // 贪心优化
            else st[id][type] = true;
            
            for(int i = h[id]; i != -1; i = ne[i]) { 
                int j = e[i];
                if(dist[j] > d + w[i]) {
                    subdist[j] = dist[j]; // 更新次短路
                    dist[j] = d + w[i]; // 更新最短路
                    heap.offer(new Pair(dist[j], j, 0));
                    heap.offer(new Pair(subdist[j], j, 1));
                } else if(d + w[i] > dist[j] && d + w[i] < subdist[j]) {
                    subdist[j] = d + w[i]; // 只更新次短路
                    heap.offer(new Pair(subdist[j], j, 1));
                }
            }
        }
        return subdist[n];
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt(); m = sc.nextInt();
    
        h = new int[n + 10]; e = new int[2*m + 10]; ne = new int[2*m + 10]; w = new int[2*m + 10];
        st = new boolean[n + 10][2];
        dist = new int[n + 10]; subdist = new int[n + 10];
        
        Arrays.fill(h, -1); // 初始化头指针数组
        
        for(int i = 0; i < m; i ++ ) {
            int a = sc.nextInt(), b = sc.nextInt(), c = sc.nextInt();
            add(a, b, c); add(b, a, c); // 无向图
        }
        
        System.out.println(dijkstra());
    }
}
```

**解法二**：**BFS** 求最短路和次短路

```java
import java.util.*;

class Pair {
    int d, id, type; // id是编号, d表示源点到id的距离, type是类型, 0是最短路, 1是次短路
    Pair(int d, int id, int type) {
        this.d = d; this.id = id; this.type = type;
    }
}

class Main {
    
    static int[] h, e, ne, w; // 邻接表
    static int idx = 0;
    static int[] dist, subdist; // dist表示最短路 subdist是次短路
    static boolean[][] st; // st[i][0]为true表示源点到i的最短路已经求过, st[i][1]表示次短路已经求过
    static int n, m;
    
    public static void add(int a, int b, int c) { // 向邻接表添加一条边 a->b 的边, 边权为c
        e[idx] = b; ne[idx] = h[a]; w[idx] = c; h[a] = idx ++ ;
    }
    
    public static int bfs() { // 返回从1到n的次短路的长度
        Arrays.fill(dist, 0x3f3f3f3f);  Arrays.fill(subdist, 0x3f3f3f3f); 
        dist[1] = 0;
        Queue<Pair> heap = new ArrayDeque<>();
        heap.offer(new Pair(0, 1, 0));
        
        while(!heap.isEmpty()) {
            Pair t = heap.poll();
            int d = t.d, id = t.id, type = t.type;
            
            for(int i = h[id]; i != -1; i = ne[i]) { 
                int j = e[i];
                if(dist[j] > d + w[i]) {
                    subdist[j] = dist[j]; // 更新次短路
                    dist[j] = d + w[i]; // 更新最短路
                    heap.offer(new Pair(dist[j], j, 0));
                    heap.offer(new Pair(subdist[j], j, 1));
                } else if(d + w[i] > dist[j] && d + w[i] < subdist[j]) {
                    subdist[j] = d + w[i]; // 只更新次短路
                    heap.offer(new Pair(subdist[j], j, 1));
                }
            }
        }
        return subdist[n];
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt(); m = sc.nextInt();
    
        h = new int[n + 10]; e = new int[2*m + 10]; ne = new int[2*m + 10]; w = new int[2*m + 10];
        st = new boolean[n + 10][2];
        dist = new int[n + 10]; subdist = new int[n + 10];
        
        Arrays.fill(h, -1); // 初始化头指针数组
        
        for(int i = 0; i < m; i ++ ) {
            int a = sc.nextInt(), b = sc.nextInt(), c = sc.nextInt();
            add(a, b, c); add(b, a, c); // 无向图
        }
        
        System.out.println(bfs());
    }
}
```

### 3、有边数限制的最短路问题

#### （1）787. K 站中转内最便宜的航班

**问题**：

有 `n` 个城市通过一些航班连接。给你一个数组 `flights` ，其中 `flights[i] = [fromi, toi, pricei]` ，表示该航班都从城市 `fromi` 开始，以价格 `pricei` 抵达 `toi`。

现在给定所有的城市和航班，以及出发城市 `src` 和目的地 `dst`，你的任务是找到出一条最多经过 `k` 站中转的路线，使得从 `src` 到 `dst` 的 **价格最便宜** ，并返回该价格。 如果不存在这样的路线，则输出 `-1`。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203142229437.png" alt="img" width="260px" />

```
输入: 
n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 1
输出: 200
```

**注意**：

- `fromi != toi`
- 航班没有重复，且不存在自环

**思路**：**Bellman Ford 算法**求有边数限制的最短路

```java
class Solution {

    int[][] edges;
    int n;
    int[] dist, last; // last为dist的备份数组

    public int bellmanFord(int k, int start, int end) { 
        Arrays.fill(dist, 0x3f3f3f3f);
        dist[start] = 0; // 初始化

        while(k -- != 0) { // 迭代k次, 每i次dist数组存储经过i条边从源点到各个点的最短路
            last = Arrays.copyOfRange(dist, 0, n); // 备份dist
            for(int i = 0; i < edges.length; i ++ ) { // 遍历所有的边
                int a = edges[i][0], b = edges[i][1], w = edges[i][2];
                dist[b] = Math.min(dist[b], last[a] + w); // 松弛操作, 用备份进行更新
            }
        }
        if(dist[end] > 0x3f3f3f3f / 2) return -1; // 不存在解
        return dist[end];
    }

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        edges = flights;
        this.n = n; 
        dist = new int[n + 10]; last = new int[n + 10];

        return bellmanFord(k + 1, src, dst); // 这里的k是中转站数, 边数为k + 1
    }
}
```

### 4、带干扰条件的最短路问题

#### （1）1786. 从第一个节点出发到最后一个节点的受限路径数:writing_hand: 

**问题**：

现有一个加权无向连通图。给你一个正整数 `n` ，表示图中有 `n` 个节点，并按从 `1` 到 `n` 给节点编号；另给你一个数组 `edges` ，其中每个 `edges[i] = [ui, vi, weighti]` 表示存在一条位于节点 `ui` 和 `vi` 之间的边，这条边的权重为 `weighti` 。

从节点 `start` 出发到节点 `end` 的路径是一个形如 `[z0, z1, z2, ..., zk]` 的节点序列，满足 `z0 = start` 、`zk = end` 且在所有符合 `0 <= i <= k-1` 的节点 `zi` 和 `zi+1` 之间存在一条边。

路径的距离定义为这条路径上所有边的权重总和。用 `distanceToLastNode(x)` 表示节点 `n` 和 `x` 之间路径的最短距离。**受限路径** 为满足`distanceToLastNode(zi) > distanceToLastNode(zi+1)` 的一条路径，其中 `0 <= i <= k-1` 。

返回从节点 `1` 出发到节点 `n` 的 **受限路径数** 。由于数字可能很大，请返回对 `109 + 7` **取余** 的结果。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203142237684.png" alt="img" width="230px" />

```
输入：n = 5, edges = [[1,2,3],[1,3,3],[2,3,1],[1,4,2],[5,2,2],[3,5,1],[5,4,10]]
输出：3
```

**注意**：

- $1 <= n <= 2 * 10^4$
- $n - 1 <= edges.length <= 4 * 10^4$
- $u_i != v_i$
- $1 <= weight_i <= 10^5$
- 任意两个节点之间至多存在一条边
- 任意两个节点之间至少存在一条路径

**思路**：**堆优化版 dijkstra 算法**、带**记忆化搜索**的**DFS**

- 使用`Dijkstra`求结点`n`到其余各个结点的最短路
- 从结点`n`开始进行`dfs`搜索，搜索所有的满足从结点`1`到结点`n`的受限路径，统计其受限路径的数量
- 普通的`dfs`搜索容易超时，在搜索的过程中使用`memo`备忘录，进行**记忆化搜索**，对`dfs`剪枝

```java
class Pair {
    int d, id;
    Pair(int d, int id) {
        this.d = d; this.id = id;
    }
}

class Solution {

    final int MOD = (int)1e9 + 7;
    int[] h, e, ne ,w; // 邻接表
    int idx = 0;
    int[] dist; // dist[i] 表示起点到i的最短路 
    boolean[] st;
    int[] memo; // 备忘录, 用于dfs记忆化搜索, memo[i]表示从i到终点的受限路径的条数

    public void add(int a, int b, int c) { // 向邻接表中添加一条边a->b, 边权为c
        e[idx] = b; ne[idx] = h[a]; w[idx] = c; h[a] = idx ++ ;
    }

    public void dijkstra(int start, int end) {
        Arrays.fill(dist, Integer.MAX_VALUE); // 注意Integer.MAX_VALUE
        dist[start] = 0;
        PriorityQueue<Pair> heap = new PriorityQueue<>((p1, p2) -> p1.d - p2.d); // 小根堆
        heap.offer(new Pair(0, start));

        while(!heap.isEmpty()) {
            Pair t = heap.poll();
            int d = t.d, id = t.id;

            if(st[id]) continue; // 贪心优化
            else st[id] = true;

            for(int i = h[id]; i != -1; i = ne[i]) { // 用t去更新与t相邻的结点
                int j = e[i];
                if(dist[j] > d + w[i]) {
                    dist[j] = d + w[i];
                    heap.offer(new Pair(dist[j], j));
                }
            }
        }
    }

    public int dfs(int u) { // dfs + 记忆化搜索, 搜索从u到1的受限路径的条数
        if(memo[u] != -1) 
            return memo[u]; // 之前已经求过结点u到结点1的受限路径的条数, memo[u]=0也相当于已经求解过
        
        if(u == 1) return memo[u] = 1;
        memo[u] = 0;

        for(int i = h[u]; i != -1; i = ne[i]) {
            int j = e[i];
            if(dist[j] > dist[u]) memo[u] = (memo[u] + dfs(j)) % MOD;
        }
        
        return memo[u];
    }

    public int countRestrictedPaths(int n, int[][] edges) {
        int m  = edges.length;
        h = new int[n + 10]; e = new int[2*m + 10]; ne = new int[2*m + 10]; w = new int[2*m + 10];
        dist = new int[n + 10]; memo = new int[n + 10];
        st = new boolean[n + 10];

        Arrays.fill(h, -1); // 初始化头结点
        Arrays.fill(memo, -1); // 必须初始化为-1

        for(int i = 0; i < m; i ++ ) {
            int a = edges[i][0], b = edges[i][1], c = edges[i][2];
            add(a, b, c); add(b, a, c); // 无向图
        }

        dijkstra(n, 1);
        return dfs(n);
    }
}
```



### 5、动态修改权的最短路问题 

#### （1）[2699. 修改图中的边权](https://leetcode.cn/problems/modify-graph-edge-weights/) 

**问题**：

给你一个 `n` 个节点的 **无向带权连通** 图，节点编号为 `0` 到 `n - 1` ，再给你一个整数数组 `edges` ，其中$edges[i] = [a_i, b_i, w_i]$ 表示节点 $a_i$ 和 $b_i$ 之间有一条边权为 $w_i$ 的边。

部分边的边权为 `-1`（$w_i = -1$），其他边的边权都为 **正** 数（$w_i > 0$）。

你需要将**所有边权**为 `-1` 的边都修改为范围 `[1, 2 * 10^9]` 中的 **正整数** ，使得从节点 `source` 到节点 `destination` 的 **最短距离** 为整数 `target` 。如果有 **多种** 修改方案可以使 `source` 和 `destination` 之间的最短距离等于 `target` ，你可以返回任意一种方案。

如果存在使 `source` 到 `destination` 最短距离为 `target` 的方案，请你按任意顺序返回包含所有边的数组（包括未修改边权的边）。**如果不存在这样的方案**，请你返回一个 **空数组** 。

**注意：**你不能修改一开始边权为正数的边。

例子：

<img src="https://assets.leetcode.com/uploads/2023/04/18/graph.png" alt="img" style="zoom:67%;" />

```
输入：n = 5, edges = [[4,1,-1],[2,0,-1],[0,3,-1],[4,3,-1]], source = 0, destination = 1, target = 5
输出：[[4,1,1],[2,0,1],[0,3,3],[4,3,1]]
解释：上图展示了一个满足题意的修改方案，从 0 到 1 的最短距离为 5 。
```

数据范围：

- `1 <= n <= 100`
- `1 <= edges.length <= n * (n - 1) / 2`
- `edges[i].length == 3`
- `0 <= ai, bi < n`
- `wi = -1` 或者 `1 <= wi <= 10^7`
- `ai != bi`
- `0 <= source, destination < n`
- `source != destination`
- `1 <= target <= 10^9`
- 输入的图是连通图，且没有自环和重边。

**思路**：

见灵神的视频讲解 [【周赛 346】](https://leetcode.cn/link/?target=https://www.bilibili.com/video/BV1Qm4y1t7cx/)第四题 和[题解](https://leetcode.cn/problems/modify-graph-edge-weights/solution/xiang-xi-fen-xi-liang-ci-dijkstrachou-mi-gv1m/) 

```java
class Solution {

    // k == 0指第一次dijkstra, k == 1指第二次dijkstra
    public void dijkstra(int[][][] g, int[][] dist, int[][] edges, int n, int s, int d, int target, int k) {
        boolean[] st = new boolean[n];
        dist[k][s] = 0; // 起点到其自己的距离为0

        for(int i = 0; i < n; i ++ ) { // 迭代n次，每次确定一个点到起点的最短距离
            int t = -1;
            for(int j = 0; j < n; j ++ ) // 第一次选择的点必定是起点,因为起点到起点的距离为0，其余为无穷大
                if(!st[j] && (t == -1 || dist[k][t] > dist[k][j])) 
                // 从还没有确定最短路的集合中选择一个距离起点最近的点
                    t = j;

            st[t] = true; // 将t加入已经确定最短路的集合S中
            if(t == d) return ;

            for(int j = 0; j < n; j ++) { 
                // 用t去更新其他还没有加入集合S中的节点，加入S中的节点的最短路已经确定，一定不会被更新
                int wt = g[t][j][0], idx = g[t][j][1];
                if(wt == 0x3f3f3f3f) continue;
                if(wt == -1) wt = 1; // 将边权从-1更新成1
                
                // dist[1][t] + w + dist[0][d] - dist[0][j] = target
                if(k == 1 && g[t][j][0] == -1) {
                    int w = target - dist[0][d] - dist[1][t] + dist[0][j];
                    if(w > wt) g[t][j][0] = g[j][t][0] = edges[idx][2] = wt = w;
                }
                dist[k][j] = Math.min(dist[k][j], dist[k][t] + wt);
            }
        }
    }

    public int[][] modifiedGraphEdges(int n, int[][] edges, int source, int destination, int target) {
        int s = source, d = destination;
        int[][][] g = new int[n][n][2]; // 邻接矩阵存储稠密图
        int[][] dist = new int[2][n]; // 起点source到i的最短距离为dist[0][i] or dist[1][i]

        // 初始化g和dist数组
        Arrays.fill(dist[0], 0x3f3f3f3f);
        Arrays.fill(dist[1], 0x3f3f3f3f);
        for(int i = 0; i < n; i ++ ) 
            for(int j = 0; j < n; j ++) g[i][j][0] = 0x3f3f3f3f;

        for(int i = 0; i < edges.length; i ++) { // 初始化邻接矩阵
            int a = edges[i][0], b = edges[i][1], w = edges[i][2];
            g[a][b][0] = g[b][a][0] = w; // 存储边权
            g[a][b][1] = g[b][a][1] = i; // 存储边在edges数组中的编号
        }
 
        dijkstra(g, dist, edges, n, s, d, target, 0);
        if(dist[0][d] > target) return new int[][]{}; // 无解

        dijkstra(g, dist, edges, n, s, d, target, 1);
        if(dist[1][d] < target) return new int[][]{}; // 无界
        
        for(int i = 0; i < edges.length; i ++) 
            if(edges[i][2] == -1) edges[i][2] = 1;
        return edges;
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
