# Dive Into LeetCode’s Algorithm

<div align="center">
    <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202110171205398.jpg" alt="芝士" width = "150px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分

### 1、最短路

#### （I） 最短路问题

###### （1） 6032. 得到要求路径的最小带权子图:writing_hand: 

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

###### （2）743. 网络延迟时间

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

###### （3）[1368. 使网格图至少有一条有效路径的最小代价](https://leetcode.cn/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/)（:o:）

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

###### （4）[LCP 56. 信物传送](https://leetcode.cn/problems/6UEx57/)

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



#### （II）次短路问题

###### （1）2045. 到达目的地的第二短时间:writing_hand: 

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

###### （2）AcWing 1133. 第二短路

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

#### （III）有边数限制的最短路问题

###### （1）787. K 站中转内最便宜的航班

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

#### （IV）带干扰条件的最短路问题

###### （1）1786. 从第一个节点出发到最后一个节点的受限路径数:writing_hand: 

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



#### （V）动态修改权的最短路问题 

###### （1）[2699. 修改图中的边权](https://leetcode.cn/problems/modify-graph-edge-weights/) 

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



### 2、滑动窗口算法

#### （1）[1156. 单字符重复子串的最大长度](https://leetcode.cn/problems/swap-for-longest-repeated-character-substring/)

**问题**：

如果字符串中的所有字符都相同，那么这个字符串是单字符重复的字符串。

给你一个字符串 `text`，你只能**交换其中两个字符一次**或者什么都不做，然后得到一些单字符重复的子串。返回其中最长的子串的长度。

例子：

```
输入：text = "aaabbaaa"
输出：4
```

**数据范围**：

- `1 <= text.length <= 20000`
- `text` 仅由小写英文字母组成。

**思路**：**滑动窗口**，时间复杂度 $O(n)$

给定一个字符串，你需要选择两个字符进行交换，这个操作**最多进行一次**，要求使得仅包含相同字符的子串尽可能地长。例如：“bbababaaaa”，可以交换第2（下标从0开始）个字符a与第5个字符b，使得包含相同字符的子串最长为6，即“aaaaaa"。

设 $n$ 为字符串 $text$的长度，下标从0开始，现有一段区间 $[i,j)$（不包括 $j$）由相同的字符 $a$ 构成，并且该区间两边不存在相同的字符 $a$，而整个 $text$ 中 $a$ 出现的次数为 $count[a]$，那么当 $count[a]>j-i$，并且 $i > 0$ 或者 $j < n$ 时，可以将其他地方出现的 $a$ 与 $text[i-1]$或者$text[j]$交换，从而得到更长的一段包含 $a$ 的子串。

交换后，交换过来的 $a$ 可能使得两段连续的 $a$ 拼接在一起，我们假设 $[i,j)$ 是前面的一段，当$text[j+1]=a$时，我们在找到后面的一段 $[j+1, k)$，这两段拼接在一起构成更长的子串。

注意，我们需要重新判断是否有多余的 $a$ 交换到中间来，因此将拼接后的长度 $k-i$ 与 $count[a]$ 取最小值来更新答案。

```java
class Solution {
    public int maxRepOpt1(String text) {
        int[] count = new int[26]; // 记录每个字符出现的次数
        for(int i = 0; i < text.length(); i ++ )
            count[text.charAt(i) - 'a'] ++ ;

        int res = Integer.MIN_VALUE;
        for(int i = 0; i < text.length(); ) {
            // 寻找区间[i,j), 字符串[i,j]重复的字符是text.charAt(i)
            int j = i;
            while(j < text.length() && text.charAt(j) == text.charAt(i)) j ++ ;

            res = Math.max(res, j - i); // 更新答案
            // 可以在子串[i,j)的前(i - 1)或后(j), 交换一次字符
            if(count[text.charAt(i) - 'a'] > j - i) { 
                int k = j + 1; // 寻找区间[j + 1,k), 字符串[j + 1,k]重复的字符是text.charAt(i)
                while(k < text.length() && text.charAt(k) == text.charAt(i)) k ++ ;
                res = Math.max(res, Math.min(k - i, count[text.charAt(i) - 'a']));
            }
            i = j; // 更新i
        }
        return res;
    }
}
```

#### （2）[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/) 

**问题**：

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` **所有字符**的**最小**子串。

如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**例子**：

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

数据范围：

- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 105`
- `s` 和 `t` 由英文字母组成
- 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**思路**：

窗口扩张和收缩的时机

- 窗口扩张，right右移，纳入目标数字，可以看作主旋律，在扩张的间隙去收缩。
  - 如果纳入了一个正好缺失的数组的数字，count就+1
  - 哈希表中对应的数组的出现次数 +1
  - 如果此时count正好等于数组的个数k，则我们找到了一个可行解，此时再扩张没有意义，反而背离“最小窗口”。
  - 此时应该收缩窗口长度，left右移。
- 窗口收缩，舍弃左指针指向的数字，直到条件不再满足，就不收缩了。
  - 如果哈希表中有数组的出现次数因此变0，此时窗口缺失了目标数字，条件被破坏了。
  - 这之前的窗口长度，是当前可行解下的最优解，记录下来。
- 此时要重新扩张窗口，去寻找下一个可行解，再优化，再优中取优

```java
class Solution {
    public String minWindow(String s, String t) {
        // 记录滑动窗口中出现字符串t中的字符的出现的次数，滑动窗口保证每个出现字符串t中的字符至少出现一次
        int[] map = new int['z' - 'A' + 1];
        int[] cnt = new int['z' - 'A' + 1]; // 记录t的每一个不重复的字符

        for(int i = 0; i < t.length(); i ++ ) cnt[t.charAt(i) - 'A'] ++ ;
            
        int start = 0, end = Integer.MAX_VALUE;
        for(int l = 0, r = 0; r < s.length(); r ++ ) { // l 窗口左边界, r 窗口右边界
            int ch = s.charAt(r) - 'A';
            if(cnt[ch] != 0) map[ch] ++ ;
            
            // 左边界更新策略
            while(l <= r && (cnt[s.charAt(l) - 'A'] == 0|| map[s.charAt(l) - 'A'] > cnt[s.charAt(l) - 'A'])) {
                if(cnt[s.charAt(l) - 'A'] != 0) map[s.charAt(l) - 'A'] --;
                l ++ ;
            }

            boolean flag = true;
            for(int i = 0; i < cnt.length; i ++ )
                if(map[i] < cnt[i]) {
                    flag = false;
                    break;
                }

            if(flag && r - l < end - start) { // 更新最小区间
                start = l;
                end = r;
            }
        }

        return end == Integer.MAX_VALUE ? "" : s.substring(start, end + 1);
    }
}
```

#### （3）[632. 最小区间](https://leetcode.cn/problems/smallest-range-covering-elements-from-k-lists/)

**问题**：

你有 `k` 个 **非递减排列** 的整数列表。找到一个 **最小** 区间，使得 `k` 个列表中的每个列表**至少有一个数**包含在其中。

我们定义如果 `b-a < d-c` 或者在 `b-a == d-c` 时 `a < c`，则区间 `[a,b]` 比 `[c,d]` 小。

例子：

```
输入：nums = [[4,10,15,24,26], [0,9,12,20], [5,18,22,30]]
输出：[20,24]
解释： 
列表 1：[4, 10, 15, 24, 26]，24 在区间 [20,24] 中。
列表 2：[0, 9, 12, 20]，20 在区间 [20,24] 中。
列表 3：[5, 18, 22, 30]，22 在区间 [20,24] 中。
```

**数据范围**：

- `nums.length == k`
- `1 <= k <= 3500`
- `1 <= nums[i].length <= 50`
- `-10^5 <= nums[i][j] <= 10^5`
- `nums[i]` 按非递减顺序排列

**思路**：

- 将k个列表合并到一个列表中，并将所有数从小到大进行排序。因为单个整数列表是非递增排序序列，可以考虑多路归并来提高性能。
- 动窗口要保证$k$个列表中每一个列表至少有一个数出现在窗口中，因此窗口内维护的都是合法情况，在所有合法情况中取最小。

```java
class Solution {
    public int[] smallestRange(List<List<Integer>> nums) {
        int n = nums.size();
        List<int[]> arr = new ArrayList<>();
        for(int i = 0; i < nums.size(); i ++ ) 
            for(Integer x : nums.get(i)) arr.add(new int[]{x, i}); // i表示x所在的列表标号
        Collections.sort(arr, (a, b) -> a[0] - b[0]); // 按数组第一位元素的大小从小到大排序

        int left = 0, right = Integer.MAX_VALUE;

        // 滑动窗口要保证k个列表中每一个列表至少有一个数出现在窗口中，因此窗口内维护的都是合法情况，在所有合法情况中取最小
        Map<Integer, Integer> map = new HashMap<>(); // 记录滑动窗口中每个标号出现的次数，滑动窗口保证每个标号至少出现一次
        for(int l = 0, r = 0; r < arr.size(); r ++ ) { // l 窗口左边界, r 窗口右边界
            int[] x = arr.get(r);

            map.put(x[1], map.getOrDefault(x[1], 0) + 1);
            while(l <= r && map.getOrDefault(arr.get(l)[1], 0) > 1) { // 左边界更新策略
                map.put(arr.get(l)[1], map.get(arr.get(l)[1]) - 1);
                l ++ ;
            }

            if(map.size() == n && x[0] - arr.get(l)[0] < right - left) { // 更新最小区间
                left = arr.get(l)[0];
                right = x[0];
            }
        }
        return new int[]{left, right}; 
    }
}
```



#### （4）[30. 串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)



#### （5）[209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

**问题**：

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其和 `≥ target` 的长度最小的 **连续子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

**例子**：

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```

**数据范围**：

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 105`

**思路**：

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int len = Integer.MAX_VALUE, sum = 0;
        for(int l = 0, r = 0; r < nums.length; r ++ ) {
            sum += nums[r]; // 扩张
            while(l <= r && sum - nums[l] >= target) sum -= nums[l ++]; // 收缩
            if(sum >= target) len = Math.min(len, r - l + 1); // 符合条件才更新len
        }
        return len == Integer.MAX_VALUE ? 0 : len;
    }
}
```



#### （6）[567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)

**问题**：

给你两个字符串 `s1` 和 `s2` ，写一个函数来判断 `s2` 是否包含 `s1` 的排列。如果是，返回 `true` ；否则，返回 `false` 。

换句话说，`s1` 的排列之一是 `s2` 的 **子串** 。

例子：

```
输入：s1 = "ab" s2 = "eidbaooo"
输出：true
解释：s2 包含 s1 的排列之一 ("ba").
```

**数据范围**：

- `1 <= s1.length, s2.length <= 104`
- `s1` 和 `s2` 仅包含小写字母

**思路**：

```java
class Solution {

    public boolean check(int[] a, int[] b) { // 当且仅当，两个数组所有元素都一致时，这个两个数组才相等
        for(int i = 0; i < a.length; i ++ ) 
            if(a[i] != b[i]) return false;
        return true;
    }

    public boolean checkInclusion(String s1, String s2) {
        // 滑动窗口定义：滑动窗口的大小始终等于s1的长度(l,r维护)，窗口内的字符都在s1中出现(map和cnt维护)

        int[] map = new int[26]; // 维护滑动窗口中每个元素出现的次数
        int[] cnt = new int[26]; // 维护s1中每个元素出现的次数

        for(int i = 0; i < s1.length(); i ++) cnt[s1.charAt(i) - 'a'] ++ ; // 初始化cnt

        for(int l = 0, r = 0; r < s2.length(); r ++ ) { // l左边界, r右边界
            char ch = s2.charAt(r);

            if(cnt[ch - 'a'] > 0) map[ch - 'a'] ++ ; // 扩张, 如果ch是s1中出现的字符,此字符进入窗口
            else { // 快速收缩, 如果ch不是s1中出现的字符, 则左边界l跳过窗口中的所有元素, l = r + 1, l指向右边界r的下一个值
                l = r + 1;
                Arrays.fill(map, 0); // 重置map
                continue;
            }
            // 当窗口的长度大于目标字符串s1的长度, 且窗口内的字符都在s1中出现, 则搜索使得窗口的长度等于字符串s1的长度
            while(l <= r && r - l + 1 > s1.length()) map[s2.charAt(l ++) - 'a'] -- ; // 正常收缩

            if(r - l + 1 == s1.length() && check(map, cnt)) return true; // 长度相等且字符出现的次数相同时，找到一个解
        }

        return false;
    }
}
```

#### （7）[2747. 统计没有收到请求的服务器数目](https://leetcode.cn/problems/count-zero-request-servers/) 

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

- `1 <= n <= 10^5`
- `1 <= logs.length <= 10^5`
- `1 <= queries.length <= 10^5`
- `logs[i].length == 2`
- **`1 <= logs[i][0] <= n`**
- `1 <= logs[i][1] <= 10^6`
- `1 <= x <= 10^5`
- `x < queries[i] <= 10^6`

**思路**：

此题的特点是所有的查询区间的长度是固定大小$x$。那么一个容易想到的思路是将所有 $logs$ 按时间从小到大排序，将所有查询区间按左/右边界从小到大排序。所有区间按左边界排序后，**不存在区间包含区间的问题**，可以考虑使用滑动窗口求出每个区间收到请求的服务数量。

维护一个滑动窗口，该滑动窗口从左向右滑动，每完成一轮滑动（右边界扩张和左边界收缩），都可以得到一个区间查询的结果。

```java
class Solution {     
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        Arrays.sort(logs, (a, b) -> (a[1] - b[1])); // 所有logs按时间从小到大排序
        
        HashMap<Integer, Integer> map = new HashMap<>(); // map的值表示queries[i]在原数组中的下标
        for(int i = 0; i < queries.length; i ++) map.put(queries[i], i);
        Arrays.sort(queries);

        int[] res = new int[queries.length];
        // servers维护了滑动窗口内每个服务器的出现次数
        int[] servers = new int[100010];
        int cnt = 0; // cnt维护了滑动窗口内不同编号的服务器的数量（没有重复元素）
        for(int l = 0, r = 0, idx = 0; idx < queries.length; idx ++) {// l窗口左边界, r右边界
            // 窗口一共进行queries.length轮滑动, 每次滑动完成一个区间的查询
            while(r < logs.length && logs[r][1] <= queries[idx]) { // 扩张
                if(servers[logs[r][0]] == 0) cnt ++;
                servers[logs[r ++][0]] ++;
            } 
            while(l <= r && l < logs.length && logs[l][1] < queries[idx] - x) { // 收缩
                servers[logs[l][0]] --;
                if(servers[logs[l ++][0]] == 0) cnt --;
            }
            res[map.get(queries[idx]).intValue()] = n - cnt; // 区间查询结果
        }

        return res;
    }
}
```

精简版代码

```java
class Solution {     
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        Arrays.sort(logs, (a, b) -> (a[1] - b[1])); // 所有logs按时间从小到大排序
        
        var ids = new Integer[queries.length]; // ids的值表示queries[i]在原数组中的下标
        for(int i = 0; i < queries.length; i ++) ids[i] = i;
        Arrays.sort(ids, (a, b) -> queries[a] - queries[b]);

        int[] res = new int[queries.length];
        
        int[] servers = new int[100010]; // servers维护了滑动窗口内每个服务器的出现次数
        int l = 0, r = 0; // l窗口左边界, r右边界
        for(var id : ids) { // 窗口一共进行queries.length轮滑动, 每次滑动完成一个区间的查询
            while(r < logs.length && logs[r][1] <= queries[id])  // 扩张
                if(servers[logs[r ++][0]] ++ == 0) n --;
            while(l <= r && l < logs.length && logs[l][1] < queries[id] - x) // 收缩
                if(-- servers[logs[l ++][0]] == 0) n ++;
            res[id] = n; // 区间查询结果
        }
        return res;
    }
}
```

超时代码问题分析：

下面的代码用数组模拟哈希表，本质上来说数组的赋值和查询要比 `HashMap`更快。但是，下面的代码会超时的主要原因是` int[] map = new int[1000010];`，创建数组的速度太慢（`new`长度为100w的数组花费的时间太长）。

```java

class Solution {     
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        Arrays.sort(logs, (a, b) -> (a[1] - b[1])); // 所有logs按时间从小到大排序
        
        int[] map = new int[1000010];
        for(int i = 0; i < queries.length; i ++) { // map的值表示queries[i]在原数组中的下标
            queries[i] -= x;
            map[queries[i]] = i;
        }
        Arrays.sort(queries);
      
        int[] res = new int[queries.length];
        int[] servers = new int[100010]; // servers维护了滑动窗口内每个服务器的出现次数
        int cnt = 0; // cnt维护了滑动窗口内不同编号的服务器的数量（没有重复元素）
      
				// 窗口一共进行queries.length轮滑动, 每次滑动完成一个区间的查询
        for(int l = 0, r = 0, idx = 0; idx < queries.length; idx ++) { // l窗口左边界, r右边界
            while(r < logs.length && logs[r][1] <= queries[idx] + x) { // 扩张
                if(servers[logs[r][0]] == 0) cnt ++;
                servers[logs[r ++][0]] ++;
            } 
                
            while(l <= r && l < logs.length && logs[l][1] < queries[idx]) { // 收缩
                servers[logs[l][0]] --;
                if(servers[logs[l ++][0]] == 0) cnt --;
            }
            res[map[queries[idx]]] = n - cnt; // 区间查询结果
        }

        return res;
    }
}
```



#### （8）[LCP 68. 美观的花束](https://leetcode.cn/problems/1GxJYY/)

**问题**：

力扣嘉年华的花店中从左至右摆放了一排鲜花，记录于整型一维矩阵 `flowers` 中每个数字表示该位置所种鲜花的品种编号。你可以选择一段区间的鲜花做成插花，且不能丢弃。在你选择的插花中，如果每一品种的鲜花数量都不超过 `cnt` 朵，那么我们认为这束插花是 「美观的」。

> - 例如：`[5,5,5,6,6]` 中品种为 `5` 的花有 `3` 朵， 品种为 `6` 的花有 `2` 朵，**每一品种** 的数量均不超过 `3`

请返回在这一排鲜花中，共有多少种可选择的区间，使得插花是「美观的」。

**注意：**

- 答案需要以 `1e9 + 7 (1000000007)` 为底取模，如：计算初始结果为：`1000000008`，请返回 `1`

例子：

```
输入：flowers = [1,2,3,2], cnt = 1

输出：8

解释：相同的鲜花不超过 1 朵，共有 8 种花束是美观的；
长度为 1 的区间 [1]、[2]、[3]、[2] 均满足条件，共 4 种可选择区间
长度为 2 的区间 [1,2]、[2,3]、[3,2] 均满足条件，共 3 种可选择区间
长度为 3 的区间 [1,2,3] 满足条件，共 1 种可选择区间。
区间 [2,3,2],[1,2,3,2] 都包含了 2 朵鲜花 2 ，不满足条件。
返回总数 4+3+1 = 8
```

**数据范围**：

- `1 <= flowers.length <= 10^5`
- `1 <= flowers[i] <= 10^5`
- `1 <= cnt <= 10^5`

**思路**：

用一个滑动窗口来表示一个区间$[l,r]$，要求区间$[l,r]$中每种类别的花出现的次数不超过$cnt$。其中，$l$表示滑动窗口的左边界，$r$表示滑动出口的有边界。

我们可以使用`hashmap`来维护滑动窗口中每种类型的花的出现次数。

每次滑动窗口右边界向右扩张一个单位$r+=1$，元素$flowers[r]$加入滑动窗口。

当新加入窗口的元素 $flowers[r]$出现次数超过 $cnt$时，窗口的左边界不断收缩，直到 $flowers[r]$出现次数不超过 $cnt$。

统计当前窗口与上次窗口中不重叠的区间个数。

```java
class Solution {
    public int beautifulBouquet(int[] flowers, int cnt) {
        int n = flowers.length;
        int mod = (int)1e9+7;

        var map = new HashMap<Integer, Integer>(); // 维护窗口内每束花出现的次数
        int res = 0;
        for(int l = 0, r = 0; r < n; r ++ ) { // l 窗口的左边界, r 窗口的右边界
            int lastl = l, lastr = r - 1; // 记录上一次窗口的左右边界
            map.put(flowers[r], map.getOrDefault(flowers[r], 0) + 1); // 扩张            
            while(l <= r && map.get(flowers[r]) > cnt) // 收缩
                map.put(flowers[l], map.get(flowers[l ++]) - 1);
            // a是当前窗口的大小, b是上一次窗口与当前窗口交集的大小
            int a = r - l + 1, b = (l <= lastr ? lastr - l + 1: 0); 
            res = (res + a * (a + 1) / 2 - b * (b + 1) / 2) % mod; // 加上本次与上次窗口中不重叠的区间个数
        }
        return res;
    }
}
```





#### （9）[最小窗口子序列](https://leetcode.cn/problems/minimum-window-subsequence/)



#### （10）485. 最大连续 1 的个数

**问题**：

给定一个二进制数组 `nums` ， 计算其中最大连续 `1` 的个数。

**思路**：

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int sum = 0, n = nums.length; // sum表示窗口中元素的和
        int l, r; // l,r分别表示窗口的左右边界
        for(l = 0, r = 0; r < n; r ++){
            sum += nums[r]; // 窗口没有0时, 窗口扩张

            if(r - l - 0 >= sum){ // 窗口中有1个及1个以上的0时, 保持窗口大小(l++, r ++), 右移窗口
                sum -= nums[l];
                l ++;
            }
        }
        return r - l; // 最后返回l-r的差值
    }
}
```

#### （11）487. 最大连续 1 的个数 II

**问题**：

给定一个二进制数组 `nums` ，如果最多可以翻转一个 `0` ，则返回数组中连续 `1` 的最大个数。

**思路**：

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int sum = 0, n = nums.length; // sum表示窗口中元素的和
        int l, r; // l,r分别表示窗口的左右边界
        for(l = 0, r = 0; r < n; r ++){ 
            sum += nums[r]; // 窗口中不超过一个0时, 窗口扩张

            if(r - l - 1>= sum){ // 窗口中超过2个及2个以上的0时时, 保持窗口大小(l++,r++), 不断右移窗口
                sum -= nums[l];
                l ++;
            }
        }
        return r - l; // 最后返回l-r的差值
    }
}
```

#### （12）1004. 最大连续 1 的个数 III:writing_hand: 

**问题**：

给定一个二进制数组 `nums` 和一个整数 `k` ，如果可以翻转最多`k` 个 `0` ，则返回 *数组中连续 `1` 的最大个数* 。

**思路**：

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int sum = 0, n = nums.length; // sum表示窗口中元素的和
        int l, r; // l,r分别表示窗口的左右边界
        for(l = 0, r = 0; r < n; r ++){
            sum += nums[r]; // 窗口中只有不超过k个0时, 窗口扩张
            
            if(r - l - k >= sum){ // 窗口中超过k+1个及k+1个以上的0时时, 保持窗口大小(l++,r++), 不断右移窗口
                sum -= nums[l];
                l ++;
            }
        }
        return r - l; // 最后返回l-r的差值
    }
}
```



#### （13）[6900. 统计完全子数组的数目](https://leetcode.cn/problems/count-complete-subarrays-in-an-array/)

**问题**

给你一个由 **正** 整数组成的数组 `nums` 。

如果数组中的某个子数组满足下述条件，则称之为 **完全子数组** ：

- 子数组中 **不同** 元素的数目等于整个数组不同元素的数目。

返回数组中 **完全子数组** 的数目。

**子数组** 是数组中的一个连续非空序列。

例子

```
输入：nums = [1,3,1,2,2]
输出：4
解释：完全子数组有：[1,3,1,2]、[1,3,1,2,2]、[3,1,2] 和 [3,1,2,2] 。
```

**数据范围**

- `1 <= nums.length <= 1000`
- `1 <= nums[i] <= 2000`

**思路**

设指针 $l$ 和 $r$ 分别指向窗口的左右边界，$set$ 集合维护了滑动窗口的内容。

每次向右扩展一步窗口的右边界 $r$，将 $nums[r]$添加到集合 $set$ 中

始终保持窗口内的不同元素的数目等于整个数组不同元素的数目。在此条件下，收缩窗口的左边界，包含窗口内元素的完全子数组个数为 $(n - 1 - r + 1)$。例如 $nums = [1,3,1,2,2]$，此时滑动窗口为 $window=[3,1,2]$，那么包含窗口内元素的完全子数组为$\{[3,1,2],[3,1,2,2]\}$。

```java
class Solution {
    public int countCompleteSubarrays(int[] nums) {
        int n = nums.length;
        
        Set<Integer> set = new HashSet<>();
        for(int i = 0; i < n; i ++) set.add(nums[i]);
        int cnt = set.size(); // 数组nums中不同元素的个数
        
        set.clear();
        int[] st = new int[2010]; // st[i]是元素nums[i]出现的次数
        int res = 0;
        for(int l = 0, r = 0; r < n; r ++) { // l, r分别表示窗口的左右边界
            set.add(nums[r]); // 扩展窗口的右边界
            st[nums[r]] ++; // 计数
            while(set.size() == cnt && l <= r) { // 始终保持窗口的不同元素的数目等于整个数组不同元素的数目
                res += (n - 1 - r + 1); // 统计完全子数组的个数
                st[nums[l]] --; // 收缩
                if(st[nums[l]] == 0) set.remove(nums[l]); // 更新st
                l ++;
            }
        }
        return res;
    }
}
```



### 3、Sparse Table（稀疏表） 

#### （1）[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)（ST）

**问题**：

给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。

返回 *滑动窗口中的最大值* 。

例子：

```
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `-104 <= nums[i] <= 10^4`

- `1 <= k <= nums.length`

**思路**：稀疏表模板题

```java
class Solution {

    public int log2(int x) { // 计算x以2为底的对数
        return (int)(Math.log(x) / Math.log(2) + 1e-10);
    }

    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[][] f = new int[n + 1][22]; // Sparse Table, 下标从1开始, 2^22=4194304远大于10^5
        int[] lg = new int[n + 1];

        for(int i = 1; i <= n; i ++ ) f[i][0] = nums[i - 1]; // 一个数的区间的最大值就是其本身
        for(int i = 0; i <= n; i ++ ) lg[i] = log2(i);

        for(int j = 1; (1 << j) <= n; j ++) // 构建Sparse Table, 2, 4, 8, .., log2(n)
            for(int i = 1; i + (1 << j) - 1 <= n; i ++ ) // 枚举所有的左端点, 区间长度为2^j
                f[i][j] = Math.max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);

        int[] res = new int[n - (k - 1)];
        for(int i = 1; i + k - 1 <= n; i ++) {
            int x = i, y = i + k - 1; // 要查询的区间[x, y]
            int t = lg[y - x + 1]; // 
            res[i - 1] = Math.max(f[x][t], f[y - ((1 << t) - 1)][t]); // 查询
        }
        return res;
    }
}
```



#### （2）[2654. 使数组所有元素变成 1 的最少操作次数](https://leetcode.cn/problems/minimum-number-of-operations-to-make-all-array-elements-equal-to-1/)（二分+ST）

**问题**：

给你一个下标从 **0** 开始的 **正** 整数数组 `nums` 。你可以对数组执行以下操作 **任意** 次：

- 选择一个满足 `0 <= i < n - 1` 的下标 `i` ，将 `nums[i]` 或者 `nums[i+1]` 两者之一替换成它们的最大公约数。

请你返回使数组 `nums` 中所有元素都等于 `1` 的 **最少** 操作次数。如果无法让数组全部变成 `1` ，请你返回 `-1` 。

两个正整数的最大公约数指的是能整除这两个数的最大正整数。

例子：

```
输入：nums = [2,6,3,4]
输出：4
解释：我们可以执行以下操作：
- 选择下标 i = 2 ，将 nums[2] 替换为 gcd(3,4) = 1 ，得到 nums = [2,6,1,4] 。
- 选择下标 i = 1 ，将 nums[1] 替换为 gcd(6,1) = 1 ，得到 nums = [2,1,1,4] 。
- 选择下标 i = 0 ，将 nums[0] 替换为 gcd(2,1) = 1 ，得到 nums = [1,1,1,4] 。
- 选择下标 i = 2 ，将 nums[3] 替换为 gcd(1,4) = 1 ，得到 nums = [1,1,1,1] 。


输入：nums = [2,10,6,14]
输出：-1
解释：无法将所有元素都变成 1 。
```

**数据范围**：

- `2 <= nums.length <= 50`
- `1 <= nums[i] <= 10^6`

**思路**：

首先很容易发现每次只能改变一个数，而>1的数至少需要一次才能变成1，如果已经存在1，那么显然每个数都可以一次变成1，答案就是n-数组中1的数量。

如果数组中不存在1，就要先尝试创造出第一个1，在创造出第一个1后，由于其他数不是1，那么此时的最小次数同上，是n-1。现在考虑创造出1的最小次数，显然是**gcd为1的最小子数组**。因为对于这样的一个子数组，从左右到右取一遍gcd(len-1次操作)就能得到1了。而对于gcd不是1的子数组，显然怎么搞都搞不出1.

所以问题变成了求gcd=1的最小子数组，这里提供一个不需要脑子的做法

因为不带修，所以**ST表直接暴力维护区间GCD**就好了。然后既然要求最小长度，那就直接**二分子数组长度，二分以后暴力check所有这个长度的子数组是否存在gcd为1即可，利用ST表做区间查询**。

时间复杂度大概是O(nlog^2)瓶颈是预处理，但是OI-WIKI上似乎证明了是只有一个log的

```java
class Solution {

    public int gcd(int a, int b) {
        return b != 0 ? gcd(b, a % b) : a;
    }

    public boolean check(int[][] f, int[] lg, int k, int n) {
        for(int i = 1; i + k - 1 <= n; i ++) {
            int l = i, r = i + k - 1;
            int t = lg[r - l + 1];
            if(gcd(f[l][t], f[r - ((1 << t) - 1)][t]) == 1) return true;
        }
        return false;
    }

    public int minOperations(int[] nums) {
        // nums中存在1
        int cnt = 0, n = nums.length; // cnt表示数组中1的个数
        for(int i = 0; i < n; i ++) 
            if(nums[i] == 1) cnt ++ ;
        if(cnt > 0) return n - cnt; // 需要操作 n - cnt, 每一个不为1的元素都需要操作一次才能等于1

        int[][] f = new int[n + 1][22]; // ST,f[i][j]表示区间[i, (1 << j) -1]的最大公约数
        int[] lg = new int[n + 1];
        for(int i = 1; i <= n; i ++) f[i][0] = nums[i - 1]; // 单个数够成的区间的最大公约数是其本身
        for(int i = 2; i <= n; i ++) lg[i] = lg[i >> 1] + 1; // 预处理log2的对数

        for(int j = 1; (1 << j) <= n; j ++ )
            for(int i = 1; i + (1 << j) - 1 <= n; i ++)
                f[i][j] = gcd(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]); // 构建稀疏表

        int l = 1, r = n + 1;
        while(l < r) {
            int mid = (l + r) >> 1;
            if(check(f, lg, mid, n)) r = mid; // 长度为mid的子数组最大公约数为1，则大小大于mid的子数组也为1
            else l = mid + 1; // 否则，增加数组长度, 使得子数组最大公约数尽可能为1
        }
        int d = l;
        if(d == n + 1) return -1; // 无法将所有元素都变成 1
        return d - 1 + n - 1;
    }
}
```

#### （3）[2398. 预算内的最多机器人数目](https://leetcode.cn/problems/maximum-number-of-robots-within-budget/)（二分+ST+前缀和）

**问题**：

你有 `n` 个机器人，给你两个下标从 **0** 开始的整数数组 `chargeTimes` 和 `runningCosts` ，两者长度都为 `n` 。第 `i` 个机器人充电时间为 `chargeTimes[i]` 单位时间，花费 `runningCosts[i]` 单位时间运行。再给你一个整数 `budget` 。

运行 `k` 个机器人 **总开销** 是 `max(chargeTimes) + k * sum(runningCosts)` ，其中 `max(chargeTimes)` 是这 `k` 个机器人中最大充电时间，`sum(runningCosts)` 是这 `k` 个机器人的运行时间之和。

请你返回在 **不超过** `budget` 的前提下，你 **最多** 可以 **连续** 运行的机器人数目为多少。

例子：

```
输入：chargeTimes = [3,6,1,3,4], runningCosts = [2,1,3,4,5], budget = 25
输出：3
解释：
可以在 budget 以内运行所有单个机器人或者连续运行 2 个机器人。
选择前 3 个机器人，可以得到答案最大值 3 。总开销是 max(3,6,1) + 3 * sum(2,1,3) = 6 + 3 * 6 = 24 ，小于 25 。
可以看出无法在 budget 以内连续运行超过 3 个机器人，所以我们返回 3 。


输入：chargeTimes = [11,12,19], runningCosts = [10,8,7], budget = 19
输出：0
解释：即使运行任何一个单个机器人，还是会超出 budget，所以我们返回 0 。
```

**数据范围**：

- `chargeTimes.length == runningCosts.length == n`
- `1 <= n <= 5 * 10^4`
- `1 <= chargeTimes[i], runningCosts[i] <= 10^5`
- `1 <= budget <= 10^15`

**思路**：二分+系数表+前缀和

- 答案一定位于区间 $[0,n]$之间，因此可以采用二分搜索答案。
- 对于给定二分中间值 $mid$，我们需要快速判断**是否存在$mid$个机器人的总开销小于等于$budget$**，并更新二分的左右边界。
- 运行 `k` 个机器人 **总开销** 是 `max(chargeTimes) + k * sum(runningCosts)`。$max(chargeTimes)$ 是求区间的最大值，我们可以采用稀疏表来实现。$sum(runningCosts)$是求区间和，我们可以使用前缀和来实现。
- 二分的时间复杂度是 $O(logn)$；前缀和构造的时间复杂度是 $O(n)$，查询是$O(1)$；稀疏表构造的时间复杂度是 $O(nlogn)$，查询是$O(1)$。总的时间复杂度是 $O(nlogn)$。

```java
class Solution {
    
    public int log2(int x) {
        return (int)(Math.log(x) / (Math.log(2) + 1e-10));
    }
    
    // true: 存在k个机器人的总开销小于等于target
    public boolean check(int[][] f, long[] s, int[] lg, int k, long target) {
        long res = Integer.MAX_VALUE;
        for(int i = 1; i + k - 1 <= s.length - 1; i ++ ) {
            int l = i, r = i + k - 1;

            int t = lg[r - l + 1];
            int charge = Math.max(f[l][t], f[r - ((1 << t) - 1)][t]); // 稀疏表查询
            long sum = s[r] - s[l - 1]; // 前缀和查询

            if (charge + k * sum <= target) return true; // 只要找到存在k个机器人的总开销小于等于target，就返回true
        }
        return false; // 找不到k个机器人的总开销小于等于target, 返回false
    }

    public int maximumRobots(int[] chargeTimes, int[] runningCosts, long budget) {
        int n = chargeTimes.length;
        int[][] f = new int[n + 1][22]; // 稀疏表
        int[] lg = new int[n + 1]; // 预处理以2为底的对数
        long[] s = new long[n + 1]; // 前缀和数组

        for(int i = 1; i <= n; i ++) {
            f[i][0] = chargeTimes[i - 1]; // 一个数的区间的最大值就是其本身
            s[i] = s[i - 1] + runningCosts[i - 1]; // 构造签前缀和数组
            lg[i] = log2(i); // 预处理以2为底的对数
        }

        for(int j = 1; (1 << j) <= n; j ++ ) // 构造稀疏表
            for(int i = 1; i + (1 << j) - 1 <= n; i ++ )
                f[i][j] = Math.max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);

      	// 0表示即使运行任何一个单个机器人，还是会超出 budget
        int l = 0, r = n; // 答案一定位于区间[0,n]之间，因此可以二分搜索答案
        while(l < r) {
            int mid = (l + r + 1) >> 1;
            if(check(f, s, lg, mid, budget)) l = mid;
            else r = mid - 1; 
        }
        return l;
    }
}
```



### 4、Tarjan 算法

#### （1）[1192. 查找集群内的关键连接](https://leetcode.cn/problems/critical-connections-in-a-network/) （求割边）

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

#### （2）[LCP 54. 夺回据点](https://leetcode.cn/problems/s5kipK/) （点双连通分量缩点+求割点+求点双）

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



### 5、KMP

#### （1）[28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

**问题**：

给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。

如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。

例子

```
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0 ，所以返回 0 。
```

**数据范围**：

- `1 <= haystack.length, needle.length <= 104`
- `haystack` 和 `needle` 仅由小写英文字符组成

**思路**：模板题

> $KMP$
>
> ​		在 $KMP$ 算法中，有一个很重要的数组，叫做 $Next$ 数组（也有地方叫 $Fail$ 数组或者前缀函数）。$Next$ 数组巧妙地利用了模板串 $P$ 中**以某个位置结尾的后缀的与 $P$ 的前缀匹配的信息**，来加速 $P$ 在 $S$ 中的匹配过程。
>
> $Next$ 数组的含义
>
> ​		$Next[i]$ 表示 $P$ 中**以 $i$ 为终点的后缀**与**从 $1$ 开始前缀相匹配**，且**匹配长度最大是多少**, 即：
> $$
> \operatorname{Next}[i]=\max _{1 \leq j \leq i-1}\{j\} \text { while } \mathrm{P}[1: j]==\mathrm{P}[i-j+1: i]
> $$

$p$ 和 $s$ 数组**必须**都是从下标 $1$ 开始。 

下面是预处理 $next$数组的过程，其与kmp匹配过程一致：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-07-14 23.37.00.png" alt="截屏2023-07-14 23.37.00" style="zoom: 33%;" />



其中，$i-1$ 和 $i$ 是原字符串的下标，$j$ 和 $j + 1$是模板字符串的下标。其中 $p[i-1]==p[j]$及其之前的字符串都是已经匹配好的。当前正在尝试匹配的位置是 $i$ 和 $j + 1$，位置 $i$ 永远是与 $j + 1$去匹配。

下面是kmp匹配过程：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-07-14 23.41.17.png" alt="截屏2023-07-14 23.41.17" style="zoom: 33%;" />

其中，$i-1$ 和 $i$ 是原字符串的下标，$j$ 和 $j + 1$是模板字符串的下标。其中 $s[i-1]==p[j]$及其之前的字符串都是已经匹配好的。当前正在尝试匹配的位置是 $i$ 和 $j + 1$，位置 $s[i]$ 永远是与 $p[j + 1]$去匹配。

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int m = haystack.length(), n = needle.length();
        char[] s = new char[m + 1], p = new char[n + 1];

        for(int i = 1; i <= Math.max(m, n); i ++) { // p 和 s都是下标从1开始
            if(i <= m) s[i] = haystack.charAt(i - 1);
            if(i <= n) p[i] = needle.charAt(i - 1);
        }
        
        int[] ne = new int[n + 1];

        for(int i = 2, j = 0; i <= n; i ++) { // ne[1] = 0, 如果第一个字母匹配失败了，就从头开始匹配
            while(j != 0 && p[i] != p[j + 1]) j = ne[j]; // 匹配失败则回退
            if(p[i] == p[j + 1]) j ++; // 尝试匹配
            ne[i] = j; // 更新ne
        }

        for(int i = 1, j = 0; i <= m; i ++) {
            while(j != 0 && s[i] != p[j + 1]) j = ne[j]; // 匹配失败则回退
            if(s[i] == p[j + 1]) j ++; // 尝试匹配
            if(j == n) { // 匹配成功
                return i - (n - 1) - 1;
            }
        }
        return -1;
    }
}
```





#### （2）[214. 最短回文串](https://leetcode.cn/problems/shortest-palindrome/)



#### （3）[336. 回文对](https://leetcode.cn/problems/palindrome-pairs/)



#### （4）[686. 重复叠加字符串匹配](https://leetcode.cn/problems/repeated-string-match/)

**问题**：

给定两个字符串 `a` 和 `b`，**寻找重复叠加字符串 `a` 的最小次数**，使得字符串 `b` 成为叠加后的字符串 `a` 的子串，如果不存在则返回 `-1`。

**注意：**字符串 `"abc"` 重复叠加 0 次是 `""`，重复叠加 1 次是 `"abc"`，重复叠加 2 次是 `"abcabc"`。

例子

```
输入：a = "abcd", b = "cdabcdab"
输出：3
解释：a 重复叠加三遍后为 "abcdabcdabcd", 此时 b 是其子串。
```

**数据范围**：

- `1 <= a.length <= 10^4`
- `1 <= b.length <= 10^4`
- `a` 和 `b` 由小写英文字母组成

**思路**：模板题

尝试不断的叠加字符串 $a$，每叠加一次都将叠加后的字符串与模板串 $b$ 进行KMP子串匹配。

此题的关键是寻找叠加次数的上界 $up$，一个比较容易想到的上界是 $up=floor((2*m+n)/m)$，其中 $m$ 是原字符串的长度，$n$ 是模板串的长度。

设 `a = "abcd"`,` b = "cdabcdab"`，最大叠加次数是 $4$，叠加后的字符串是 $abcd|abcd|abcd|abcd$。

如果在最大叠加次数，两字符串依然无法匹配，则返回 -1。

```java
class Solution {

    public boolean kmp(char[] s, char[] p, int m, int n) {
        int[] ne = new int[n + 1];
        for(int i = 2, j = 0; i <= n; i ++ ) {
            while(j != 0 && p[i] != p[j + 1]) j = ne[j];
            if(p[i] == p[j + 1]) j ++;
            ne[i] = j;
        }
        for(int i = 1, j = 0; i <= m; i ++) {
            while(j != 0 && s[i] != p[j + 1]) j = ne[j];
            if(s[i] == p[j + 1]) j ++;
            if(j == n) return true;
        }
        return false;
    }

    public int repeatedStringMatch(String a, String b) {
        int m = a.length(), n = b.length();
        int cnt = n > m ? (int)Math.floor((double)(n / m)) + 2 : 2; // cnt计算是重点
        char[] s = new char[m * cnt+ 1];
        char[] p = new char[n + 1];

        for(int i = 1; i <= n; i ++) p[i] = b.charAt(i - 1);

        int idx = 1;
        for(int i = 1; i <= cnt; i ++) {
            for(int j = 0; j < m; j ++ ) s[idx ++] = a.charAt(j);
            if(kmp(s, p, idx - 1, n)) return i;
        }
        return -1;
    }
}
```





### 6、TopK+多路归并/堆/二分/双指针

#### （1）[378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/)（多路归并）

**问题**：

给你一个 `n x n` 矩阵 `matrix` ，其中每行和每列元素均按升序排序，找到矩阵中第 `k` 小的元素。

请注意，它是 **排序后** 的第 `k` 小元素，而不是第 `k` 个 **不同** 的元素。

你必须找到一个内存复杂度优于 `O(n^2)` 的解决方案。

例子：

```
输入：matrix = [[1,5,9],[10,11,13],[12,13,15]], k = 8
输出：13
解释：矩阵中的元素为 [1,5,9,10,11,12,13,13,15]，第 8 小元素是 13
```

**数据范围**：

- `n == matrix.length`
- `n == matrix[i].length`
- `1 <= n <= 300`
- `-10^9 <= matrix[i][j] <= 10^9`
- 题目数据 **保证** `matrix` 中的所有行和列都按 **非递减顺序** 排列
- `1 <= k <= n^2`

**思路**：

多路归并：核心思想是采用分治，将多路归并转换为二路归并。时间复杂度是 $O(nlogn)$

- 采用归并排序中的分治思想，将$n$个有序序列分成两份序列。
- 对左右两份序列进行递归的划分，直到每一份中只有一个序列。
- 将左右两个任务结果进行二路归并

```java
class Solution {
    // nlogn
    public int[] merger(int[][] matrix, int l, int r) {
        if(l >= r) return matrix[l];

        int mid = l + r >> 1;
        int[] a = merger(matrix, l, mid), b = merger(matrix, mid + 1, r);

        int n = a.length, m = b.length;
        int[] temp = new int[n + m];
        int i = 0, j = 0, k = 0;
        
        while(i < n && j < m) {
            if(a[i] < b[j]) temp[k ++] = a[i ++];
            else temp[k ++] = b[j ++];
        }
        while(i < n) temp[k ++] = a[i ++];
        while(j < m) temp[k ++] = b[j ++];

        return temp;
    }   

    public int kthSmallest(int[][] matrix, int k) {
        int[] arr = merger(matrix, 0, matrix.length - 1);
        return arr[k - 1];
    }
}
```

你能否用一个恒定的内存(即 $O(1)$ 内存复杂度)来解决这个问题?
你能在 $O(n)$ 的时间复杂度下解决这个问题吗?这个方法对于面试来说可能太超前了，但是你会发现阅读这篇文章（ [this paper](http://www.cse.yorku.ca/~andy/pubs/X+Y.pdf) ）很有趣。

#### （2）[373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/) (加法，二维，堆)

**问题**：

给定两个以 **升序排列** 的整数数组 `nums1` 和 `nums2` , 以及一个整数 `k` 。

定义一对值 `(u,v)`，其中第一个元素来自 `nums1`，第二个元素来自 `nums2` 。

请找到和最小的 `k` 个数对 `(u1,v1)`, ` (u2,v2)` ...  `(uk,vk)` 。

例子：

```
输入: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
输出: [1,2],[1,4],[1,6]
解释: 返回序列中的前 3 对数：
     [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```

**数据范围**：

- `1 <= nums1.length, nums2.length <= 10^5`

- `-10^9 <= nums1[i], nums2[i] <= 10^9`
- `nums1` 和 `nums2` 均为升序排列
- `1 <= k <= 1000`

**思路**：[讲清楚为什么一开始要把 (i,0) 都入堆！]([讲清楚为什么一开始要把 (i,0) 都入堆！附题单！ - 查找和最小的 K 对数字 - 力扣（LeetCode）](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/solution/jiang-qing-chu-wei-shi-yao-yi-kai-shi-ya-i0dj/)) 

初步思路：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-05-28 20.56.11.png" alt="截屏2023-05-28 20.56.11" style="zoom:50%;" />

一个复杂的算法：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-05-28 20.58.41.png" alt="截屏2023-05-28 20.58.41" style="zoom:50%;" />

![学生登录用例图 (10).png](https://pic.leetcode-cn.com/1642133430-NIIiwX-%E5%AD%A6%E7%94%9F%E7%99%BB%E5%BD%95%E7%94%A8%E4%BE%8B%E5%9B%BE%20(10).png)

上图中的（i,j）表示的是nums1下标i与nums2下标j，箭头表示的是若选择数对（i,j），那么就需要把数对（i,j+1）与（i+1,j）加入到候选中。
由图可以发现，若（0,1）和（1,0）均被选过，那么（1,1）数对就会在候选中出现2次，也就是造成了重复。

```java
class Solution {

    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        
        int n = nums1.length, m = nums2.length;
        HashMap<List<Integer>, Boolean> map = new HashMap<>(); // 判重
        List<List<Integer>> list = new ArrayList<>(); // 存储结果

        PriorityQueue<List<Integer>> heap = new PriorityQueue<>((a, b) -> { // 小根堆
            return a.get(2) + a.get(3) - (b.get(2) + b.get(3));
        });

        heap.offer(Arrays.asList(new Integer[]{0, 0, nums1[0], nums2[0]})); // 初始化小根堆
        map.put(Arrays.asList(new Integer[]{0, 0, nums2[0], nums2[0]}), true); // 初始化map

        while(list.size() < k && heap.size() != 0) { // 取出最小的k对数字
            List<Integer> pair = heap.poll();
            int x = pair.get(0), y = pair.get(1);

            list.add(Arrays.asList(new Integer[]{nums1[x], nums2[y]}));

            List<Integer> a = null, b = null; // 比(x, y) 大的数字对有(x + 1, y)和(x, y + 1)
            if(x + 1 < n && y < m) a = Arrays.asList(new Integer[]{x + 1, y, nums1[x + 1], nums2[y]}); // 判断不能数组越界
            if(x  < n && y + 1 < m) b = Arrays.asList(new Integer[]{x, y + 1, nums1[x], nums2[y + 1]});

            if(a != null && map.get(a) == null) { // 判重
                heap.offer(a);
                map.put(a, true);
            }
            if(b != null && map.get(b) == null) {
                heap.offer(b);
                map.put(b, true);
            }
        }

        return list;
    }
}
// (0, 0) -> (1, 0), (0, 1)
// (i, j) ->(i + 1, j), (i, j + 1)
// (1, 0) - > (1, 1), (2, 0)
// (0, 1) - > (1, 1), (2, 0)
```

优化后的算法： 

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-05-28 21.00.14.png" alt="截屏2023-05-28 21.00.14" style="zoom:50%;" />

那么如何解决重复问题呢？
（0,1）数对值>=（1,1）数对值
（1,0）数对值>=（1,1）数对值
这说明了当（1,1）作为答案前，（0,1）与（1,0）均已被作为答案。
那么我们只需要保留（0,1）->（1,1）与（1,0）->（1,1）其中一个箭头即可。

<img src="https://pic.leetcode-cn.com/1642134228-zyNpBD-image.png" alt="image.png" style="zoom: 67%;" />

时间复杂度是 $O(klogk）$ 

```java
class Solution {

    public List<List<Integer>> getTopk(int[] a, int[] b, int k) {
        int n = a.length, m = b.length;

        List<List<Integer>> topk = new ArrayList<>(); // 存储结果
        PriorityQueue<int[]> heap = new PriorityQueue<>((x, y) -> x[2] - y[2]);
        for(int i = 0; i < n; i ++ ) heap.offer(new int[]{i, 0, a[i] + b[0]});

        for(int i = 0; i < k && heap.size() != 0; i ++) {
            int[] t = heap.poll();
            int tx = t[0], ty = t[1];
            topk.add(Arrays.asList(new Integer[]{a[tx], b[ty]}));

            if(tx < n && ty + 1 < m) heap.offer(new int[]{ tx, ty + 1, a[tx] + b[ty + 1]});
        }
        return topk;
    }

    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        return getTopk(nums1, nums2, k);
    }
}
```

#### （3）[1439. 有序矩阵中的第 k 个最小数组和](https://leetcode.cn/problems/find-the-kth-smallest-sum-of-a-matrix-with-sorted-rows/)（加法，多维，分治+堆）

**问题**：

给你一个 `m * n` 的矩阵 `mat`，以及一个整数 `k` ，矩阵中的每一行都以非递减的顺序排列。你可以从每一行中选出 1 个元素形成一个数组。返回所有可能数组中的第 k 个 **最小** 数组和。

例子：

```
输入：mat = [[1,3,11],[2,4,6]], k = 5
输出：7
解释：从每一行中选出一个元素，前 k 个和最小的数组分别是：
[1,2], [1,4], [3,2], [3,4], [1,6]。其中第 5 个的和是 7 。
```

**数据范围**：

- `m == mat.length`
- `n == mat.length[i]`
- `1 <= m, n <= 40`
- `1 <= k <= min(200, n ^ m)`
- `1 <= mat[i][j] <= 5000`
- `mat[i]` 是一个非递减数组

**思路**：

朴素算法：堆 + 哈希表

与[373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)的求解思路一致，只不过扩展成成多个维度

```java
class Solution {
    public int kthSmallest(int[][] mat, int k) {
        int n = mat.length, m = mat[0].length;
        
        List<Integer> res = new ArrayList<>();
        HashMap<List<Integer>, Boolean> map = new HashMap<>();
        PriorityQueue<List<Integer>> heap = new PriorityQueue<>((a, b) -> a.get(a.size()-1) - b.get(b.size()-1));

        int sum = 0;
        List<Integer> arr = new ArrayList<>();
        for(int i = 0; i < n; i ++) {
            sum += mat[i][0];
            arr.add(0);
        }
        arr.add(sum);
        map.put(arr, true);
        heap.offer(arr);

        while(res.size() < k) {
            List<Integer> t = heap.poll();
            res.add(t.get(t.size() - 1));

            for(int i = 0; i < n; i ++ ) {
                List<Integer> list = new ArrayList<>(t);
                int a = list.get(i), s = list.get(list.size() - 1);
                
                if(a + 1 >= m) continue;
                list.set(i, a + 1);
                list.set(list.size() - 1, s - mat[i][a] + mat[i][a + 1]);

                if(map.get(list) == null) {
                    heap.offer(list);
                    map.put(list, true);
                }
            }
        }
        return res.get(res.size() - 1);
    }
}
```

优化后的算法：分治+堆

[378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/) 和 [373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/) 两者思想的结合

时间复杂度是 $O(k \times logk \times n)$其中 $n$ 是 $mat$ 矩阵的行数。

```java
class Solution {

    public int[] getTopk(int[] a, int[] b, int k) {
        int n = a.length, m = b.length;

        PriorityQueue<int[]> heap = new PriorityQueue<>((x, y) -> x[2] - y[2]);
        for(int i = 0; i < n; i ++ ) heap.offer(new int[]{i, 0, a[i] + b[0]});

        int[] topk = new int[k];
        int len = 0;
        for(int i = 0; i < k && heap.size() != 0; i ++) {
            int[] t = heap.poll();
            int tx = t[0], ty = t[1];
            topk[i] = t[2];
            len ++ ;
            if(tx < n && ty + 1 < m) heap.offer(new int[]{ tx, ty + 1, a[tx] + b[ty + 1]});
        }
        return Arrays.copyOf(topk, len);
    }

    public int kthSmallest(int[][] mat, int k) {
        int[] topk = mat[0];
        for(int i = 1; i < mat.length; i ++ ) 
            topk = getTopk(topk, mat[i], k);
        return topk[k - 1];
    }
}
```

#### （4）[786. 第 K 个最小的素数分数](https://leetcode-cn.com/problems/k-th-smallest-prime-fraction/)（除法，堆）

**问题**：

给你一个**按递增顺序排序**的数组 `arr` 和一个整数 `k` 。数组 `arr` 由 `1` 和若干 **素数** 组成，且其中所有整数互不相同。

对于每对满足 `0 <= i < j < arr.length` 的 `i` 和 `j` ，可以得到分数 `arr[i] / arr[j]` 。

那么第 `k` 个最小的分数是多少呢? 以长度为 `2` 的整数数组返回你的答案, 这里 `answer[0] == arr[i]` 且 `answer[1] == arr[j]` 。

例子：

```java
输入：arr = [1,2,3,5], k = 3
输出：[2,5]
解释：已构造好的分数,排序后如下所示: 
1/5, 1/3, 2/5, 1/2, 3/5, 2/3
很明显第三个最小的分数是 2/5
```

**数据范围**：

- `2 <= arr.length <= 1000`
- `1 <= arr[i] <= 3 * 104`
- `arr[0] == 1`
- `arr[i]` 是一个 **素数** ，`i > 0`
- `arr` 中的所有数字 **互不相同** ，且按 **严格递增** 排序
- `1 <= k <= arr.length * (arr.length - 1) / 2`

**思路**：与 373. 查找和最小的 K 对数字 的思想一致

朴素算法：堆+哈希表

时间复杂度是 $O(klogk)$

```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        PriorityQueue<double[]> heap = new PriorityQueue<>((a, b) -> (int)Math.signum(a[2] - b[2]));
        boolean st[][] = new boolean[n][n]; // 判重

        heap.offer(new double[]{0, n - 1, (double)arr[0]/arr[n - 1]});
        st[0][n - 1] = true;

        List<int[]> topk = new ArrayList<>();
        while(topk.size() < k) {
            double[] t = heap.poll();
            int tx = (int)t[0], ty = (int)t[1];
            topk.add(new int[]{arr[tx], arr[ty]});

            if(tx + 1 < ty && !st[tx + 1][ty]) {
                heap.offer(new double[]{tx + 1, ty, (double)arr[tx + 1]/arr[ty]});
                st[tx + 1][ty] = true;
            }
            if(tx < ty - 1 && !st[tx][ty - 1]) {
                heap.offer(new double[]{tx, ty - 1, (double)arr[tx]/arr[ty - 1]});
                st[tx][ty - 1] = true;
            }
        }
        return topk.get(k - 1);
    }
}
```

优化后的算法：堆

数组 $arr$ 是一个**按递增顺序排序**的数组。那么最小的分数一定是 $arr[0]/arr[n - 1]$，$n$ 是数组的长度。我们采用优先队列来维护当前的最小的分数。正如上面朴素算法所示，当元素对 $(i,j)$出堆时，$(i+1,j)$和$(i,j-1)$会入堆，因为这个两个元素对是比$(i,j)$次大的元素对。

但是，对于 $[0,1,2,3]$，当$(1,3)$出堆时，$(1,2)$会入堆。当$(0,2)$出堆时，$(1,2)$也会入堆。由于**存在重复元素**对，朴素算法采用了`HashMap`来判重。

使用额外`HashMap`来判重，无疑增大了内存消耗。有没有什么方法可以降低内存占用。

与之前**373. 查找和最小的 K 对数字**的做法一致，我们预先将 $(i,n-1),i=0,...,n-2$元素对入堆。然后，当$(i,j)$出堆时只执行$(i,j-1)$入堆操作。

时间复杂度是 $O(klogk)$

```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        PriorityQueue<double[]> heap = new PriorityQueue<>((a, b) -> (int)Math.signum(a[2] - b[2]));

        for(int i = 0; i < n - 1; i ++)
            heap.offer(new double[]{i, n - 1, (double)arr[i]/arr[n - 1]});

        List<int[]> topk = new ArrayList<>();
        while(topk.size() < k) {
            double[] t = heap.poll();
            int tx = (int)t[0], ty = (int)t[1];
            topk.add(new int[]{arr[tx], arr[ty]});

            if(tx < ty - 1) heap.offer(new double[]{tx, ty - 1, (double)arr[tx]/arr[ty - 1]});
        }
        return topk.get(k - 1);
    }
}
```

#### （5）[719. 找出第 k 小的距离对](https://leetcode-cn.com/problems/find-k-th-smallest-pair-distance/)（减法，数据范围大，二分嵌套二分/双指针）

**问题**：

数对 `(a,b)` 由整数 `a` 和 `b` 组成，其数对距离定义为 `a` 和 `b` 的绝对差值。给你一个整数数组 `nums` 和一个整数 `k` ，数对由 `nums[i]` 和 `nums[j]` 组成且满足 `0 <= i < j < nums.length` 。返回 **所有数对距离中** 第 `k` 小的数对距离。

例子：

```java
输入：nums = [1,3,1], k = 1
输出：0
解释：数对和对应的距离如下：
(1,3) -> 2
(1,1) -> 0
(3,1) -> 2
距离第 1 小的数对是 (1,1) ，距离为 0 。
```

**数据范围**：

- `n == nums.length`
- `2 <= n <= 10^4`
- `0 <= nums[i] <= 10^6`
- `1 <= k <= n * (n - 1) / 2`

**思路**：

**方法一：堆**

我们首先将$nums$数组从小到大排序，这样 $nums$ 是一个按递增顺序排序的数组。那么最小的**距离对**一定是 $|arr[0]-arr[1]|$，设$n$ 是数组的长度。我们采用**优先队列**来维护当前的最小的距离对。当元素对 $(i,j), i<j且i≠j$出堆时，让$(i+1,j+1)$和$(i,j+1)$会入堆，因为这个两个元素对是比$(i,j)$**次大的距离对**。

但是，对于 $[0,1,2,3]$，当$(1,2)$出堆时，$(1,3)$会入堆。当$(0,2)$出堆时，$(1,3)$也会入堆。由于存在重复元素对，**朴素算法**可以采用了`HashMap`来判重。

使用额外`HashMap`来判重，无疑增大了内存消耗。有没有什么方法可以降低内存占用。

与之前**373. 查找和最小的 K 对数字**的做法一致，我们预先将 $(i,i+1),i=0,...,n-2$元素对入堆。然后，当$(i,j)$出堆时**只执行**$(i,j+1)$入堆操作。

优化后的算法的时间复杂度是$O(klogk)$，**考虑到数据范围非常大**，$k=10^8$，时下面的算法依然会超时和超出内存限制。

```java
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        int n = nums.length;
        Arrays.sort(nums);

        PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        for(int i = 0; i < n - 1; i ++) 
            heap.offer(new int[]{i, i + 1, Math.abs(nums[i] - nums[i + 1])});

        int res = 0;
        while(k > 0 && heap.size() != 0) {
            int[] t = heap.poll();
            int tx = t[0], ty = t[1];
            res = t[2];
            k -- ;

            if(ty + 1 < n) heap.offer(new int[]{tx, ty + 1, Math.abs(nums[tx] - nums[ty + 1])});
        }

        return res;
    }
}
```

上述算法的时间复杂度与$k$有关，而$k$与数据的规模$n$有关。**我们能不能设计一个时间复杂度与$k$无关的算法**。

**方法二：排序+二分查找+嵌套二分查找**

先将数组 $nums$ 从小到大进行排序，那么所有数对中第 $k$ 小的数对距离一定出现在在区间$[0,(max(nums)-min(nums))]$中。令$left=0,right=(max(nums)-min(nums))$，我们可以在$[left, right]$区间上采用**二分查找**枚举第 $k$ 小的数对距离。

对于当前搜索的距离 $mid$，查找小于等于距离 $mid$ 的数对的个数 $cnt$。如果 $cnt >= k$，令$right = mid - 1$; 如果 $cnt < k$，令$left = mid + 1$。直到 $left > right$，我们找到了第$k$小的距离$left$。

那么对于给定一个距离 $mid$， 如何查找小于等于距离 $mid$的数对的个数 $cnt$ 呢？

对于方法二，我们采用嵌套的二分查找来解决。对于方法三，我们采用双指针算法来解决。

枚举每一个右端点$j$，在已排序的数组$nums$中，二分查找大于$nums[j]-mid$的**最小**的$i$。那么，以$j$ 为右端点的小于等于$mid$距离对的个数为$j - i$。统计所有的$j-i$之和，即距离小于等于$mid$的数对的个数$cnt$。

时间复杂度分析：排序的时间复杂度为$O(nlogn)$，设$D=max(nums)-min(nums)$，外层二分的时间复杂度为$O(logD)$，内层二分的时间复杂度为 $O(nlogn)$，总的时间复杂度为$O(nlogn+nlogn \times logD)=>O(nlogn \times logD)$

```java
class Solution {
    public int bsearch(int[]nums, int x) { // 在升序数组nums中查找距离小于等于x的数对的个数cnt
        int cnt = 0;
        for(int j = 0; j < nums.length; j ++) { // 枚举每一个数对的右端点j
            int l = 0, r = j; 
            while(l < r) { // 在区间[0, j]找到一个最小的mid使得[mid,j]区间的任意下标与的数对距离都小于等于x
                int mid = l + r >> 1;
                if(nums[j] - nums[mid] <= x) r = mid;
                else l = mid + 1;
            }
            cnt += (j - l); // (j-l)表示以j为右边界的数对的个数(该数对距离小于等于x)
        }
        return cnt;
    }

    public int check(int[] nums, int mid) {
        return bsearch(nums, mid);
    }

    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums); // 从小到大排序
        int n = nums.length;
        
        int l = 0, r = nums[n - 1] - nums[0]; // 二分区间，[0,(max(nums)-min(nums))],第k小的数对距离一定在此区间
        while(l < r) {
            int mid = l + r >> 1;
            if(check(nums, mid) >= k) r = mid;
            else l = mid + 1; 
        }

        return l;
    }
}
```

**方法三：排序+二分查找+双指针**

对于已排序的数组$nums$，初始化左指针$i=0$，从小到大枚举所有数对的右端点$j$，移动左端点直到$nums[j]-[i]<=mid$，那么右端点为$j$且数对的距离小于等于$mid$的数对个数$j-i$，计算这些数目之和。

时间复杂度分析：排序的时间复杂度为$O(nlogn)$，设$D=max(nums)-min(nums)$，二分的时间复杂度为$O(logD)$，双指针算法的时间复杂度为$O(n)$。总的时间复杂度为$O(n(logn+logD))$

```java
class Solution {

    public int bpoint(int[] nums, int mid) {
        int cnt = 0;
        for(int i = 0, j = 0; j < nums.length; j ++ ) {
            while(nums[j] - nums[i] > mid) i ++ ;
            cnt += j - i; // 加上区间[i,j]中所有以j为右边界的数对的个数
        }
            
        return cnt;
    }

    public int check(int[] nums, int mid) {
        return bpoint(nums, mid);
    }

    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums); // 从小到大排序
        int n = nums.length;
        
        int l = 0, r = nums[n - 1] - nums[0]; // 二分区间，[0,(max(nums)-min(nums))],第k小的数对距离一定在此区间
        while(l < r) {
            int mid = l + r >> 1;
            if(check(nums, mid) >= k) r = mid;
            else l = mid + 1; 
        }

        return l;
    }
}
```



#### （6）2040. 两个有序数组的第 K 小乘积（乘法，数据范围大，不适用O(klogk)算法）



#### （7）2386. 找出数组的第 K 大和（子序列和，问题转换，堆）

**问题**：

给你一个整数数组 `nums` 和一个 **正** 整数 `k` 。你可以选择数组的任一 **子序列** 并且对其全部元素求和。

数组的 **第 k 大和** 定义为：可以获得的第 `k` 个 **最大** 子序列和（子序列和允许出现重复）

返回数组的 **第 k 大和** 。

子序列是一个可以由其他数组删除某些或不删除元素排生而来的数组，且派生过程不改变剩余元素的顺序。

**注意：**空子序列的和视作 `0` 。

例子：

```java
输入：nums = [2,4,-2], k = 5
输出：2
解释：所有可能获得的子序列和列出如下，按递减顺序排列：
- 6、4、4、2、2、0、0、-2
数组的第 5 大和是 2 。
```

**数据范围**：

- `n == nums.length`
- `1 <= n <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `1 <= k <= min(2000, 2n)`

**思路**：

此题的$n<=10^5$，显然我们无法暴力穷举所有的子序列。但是，此题的$k<=2000$，我们可以选择时间复杂度为$O(k)$的算法。

**从简化问题开始**  

首先考虑本题的简化问题，给定$n$个**非负**数$a_1,a_2,...,a_n$，求第$k$个**最小**子序列的和。

这是一个经典的问题。我们先把所有数**从小到大排序**，记$(s,i)$表示一个总和为$s$，且最后一个元素是第$i$个元素的子序列。

我们用一个小根堆维护$(s,i)$，一开始堆中只有一个元素$(a_1,1)$。当我们取出堆顶元素$(s,i)$时，我们可以进行以下操作：

- 把$a_{i+1}$接到这个子序列的后面形成新的子序列，也就是将$(s+a_{i+1},i+1)$放入堆中。
- 把子序列中的$a_i$直接替换成$a_{i+1}$，也就是将$(s-a_i+a_{i+1},i+1)$放入堆中。

第$(k-1)$次取出的$(s,i)$中的$s$就是答案($k=1$时答案为空集之和，也就是0)。

这个做法的正确性基于以下事实：

- 这种方法能**不重不漏**地生成所有子序列。
- 每次放进去的数不小于拿出来的数。

**最小和变最大和**

实际上，求第$k$个最大的子序列和，与求第$k$个最小的子序列和是一样的。我们求出$k$小子序列后取反（选择不在答案中的所有元素作为新的答案），就能得到$k$大子序列。因此，所有元素之和减去$k$小子序列和，就能得到$k$大子序列和。

**引入负数**

接下来回到原问题，考虑**给定的数中有负数**的情况。

首先计算 $m$表示所有负数的和，然后将所有负数变成他们的绝对值（这样就回到了全是非负数的情况）。答案就是$m$加上$k$大子序列的和。

为什么这样是对的？（实际上，所有数取绝对值之后，求第$k$大子序列的和。对所有的子序列和将去一个数$m$，**其相对顺序是不变的**。减去$m$前是第$k$子序列，减去$m$后依然是第$k$大子序列。）

考虑到由此得到的$k$大子序列，它实际上唯一对应了一个原有的子序列。我们举个例子：

- 一开始给定整数 ${-3,-2,-1,4,5,6}$;
- 经过转换之后，我们得到$k$大子序列 ${2,1,5,6}$;
- 对于所有在该子序列中的非负数，令它成为答案的一部分，也就是说5和6是答案的一部分；
- 对于所有不在该子序列中的负数，令它成为答案的一部分，也就是说-3是答案的一部分；
- 最后得到的真实答案${-3,5,6}$。

时间复杂度分析，排序的时间复杂度是$O(nlogn)$，k个数出入堆的时间复杂度是$O(klogk)$，总的时间复杂度为$O(nlogn+klogk)$

```java
class Solution {
    public long kSum(int[] nums, int k) {
        int n = nums.length;

        long m = 0, sum = 0; // m表示所有负数之和,sum表示所有数的绝对值之和
        for(int i = 0; i < n; i ++ ) { // 将负数取绝对值 
            if(nums[i] < 0) {
                m += nums[i];
                nums[i] = -nums[i];
            }
            sum += nums[i];
        }

        Arrays.sort(nums); // 从小到大排序

        // 小根堆求绝对值数组k小子序列之和
        PriorityQueue<long[]> heap = new PriorityQueue<long[]>((a, b)-> (int)(a[0]-b[0]) );
        heap.offer(new long[]{nums[0], 0});

        long res = 0; // k = 1时视为空集之和，也就是0
        for(int i = 0; i < k - 1; i ++ ) {
            long[] t = heap.poll();
            long s = t[0];
            int pos = (int)t[1];
            res = s;
            // 两个操作
            if(pos + 1 < n) heap.offer(new long[]{s + nums[pos + 1], pos + 1});
            if(pos + 1 < n) heap.offer(new long[]{s - nums[pos] + nums[pos + 1], pos + 1});
        }

        // 最小和变最大和
        res = sum - res; // 绝对值数组的k大子序列之和
        // 引入负数
        return res + m;
    }
}
```



#### （8）[2583. 二叉树中的第 K 大层和](https://leetcode.cn/problems/kth-largest-sum-in-a-binary-tree/)（分层的层序遍历+堆）

**问题**：

给你一棵二叉树的根节点 `root` 和一个正整数 `k` 。

树中的 **层和** 是指 **同一层** 上节点值的总和。

返回树中**第 `k` 大的层和**（不一定不同）。如果树少于 `k` 层，则返回 `-1` 。

**注意**，如果两个节点与根节点的距离相同，则认为它们在同一层。

例子：

<img src="https://assets.leetcode.com/uploads/2022/12/14/binaryytreeedrawio-2.png" alt="img" style="zoom:80%;" />



```
输入：root = [5,8,9,2,1,3,7,4,6], k = 2
输出：13
解释：树中每一层的层和分别是：
- Level 1: 5
- Level 2: 8 + 9 = 17
- Level 3: 2 + 1 + 3 + 7 = 13
- Level 4: 4 + 6 = 10
第 2 大的层和等于 13 。
```

**数据范围**：

- 树中的节点数为 `n`
- `2 <= n <= 10^5`
- `1 <= Node.val <= 10^6`
- `1 <= k <= n`

思路：

树的层序遍历+大根堆。时间复杂度为$O(nlogn)$，当树的形状是一个长度为$n$的当链表时，可以达到这个最坏的时间复杂度。

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

    public long bfs(TreeNode u, int k) {
        int hh = 0, tt = 0;
        TreeNode[] q = new TreeNode[100010];
        q[0] = u; // 根节点如队列

        PriorityQueue<Long> heap = new PriorityQueue<Long>((a, b)-> -a.compareTo(b));
        while(hh <= tt) { // 当tt < hh时, 队列为空
            int head = hh, tail = tt;
            long sum = 0; // 记录当前层所有元素之和
            while(hh <= tail) { // 遍历树的每一层节点
                TreeNode t = q[hh ++];
                sum += t.val;
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
            heap.offer(sum);
        }
        if(heap.size() < k) return -1;
        while(-- k != 0) heap.poll();
        return heap.poll();
    }

    public long kthLargestLevelSum(TreeNode root, int k) {
        return bfs(root, k);
    }
}
```



#### （9）[1508. 子数组和排序后的区间和](https://leetcode.cn/problems/range-sum-of-sorted-subarray-sums/) （子序列和，二分嵌套双指针+前缀和）

**问题**：

给你一个数组 `nums` ，它包含 `n` 个正整数。你需要计算所有非空连续子数组的和，并将它们按升序排序，得到一个新的包含 `n * (n + 1) / 2` 个数字的数组。

请你返回在新数组中下标为 `left` 到 `right` **（下标从 1 开始）**的所有数字和（包括左右端点）。由于答案可能很大，请你将它对 10^9 + 7 取模后返回。

例子：

```
输入：nums = [1,2,3,4], n = 4, left = 1, right = 5
输出：13 
解释：所有的子数组和为 1, 3, 6, 10, 2, 5, 9, 3, 7, 4 。将它们升序排序后，我们得到新的数组 [1, 2, 3, 3, 4, 5, 6, 7, 9, 10] 。下标从 le = 1 到 ri = 5 的和为 1 + 2 + 3 + 3 + 4 = 13 。
```

**数据范围**:

- `1 <= nums.length <= 10^3`
- `nums.length == n`
- `1 <= nums[i] <= 100`
- `1 <= left <= right <= n * (n + 1) / 2`

**思路**：二分+(双指针+前缀和)+(双指针+前缀和的前缀和)

具体思路：[子数组和排序后的区间和 - 子数组和排序后的区间和 - 力扣（LeetCode）](https://leetcode.cn/problems/range-sum-of-sorted-subarray-sums/solution/zi-shu-zu-he-pai-xu-hou-de-qu-jian-he-by-leetcode-/) 

- 将所有的子数组的和从小到大排序，记前$left - 1$个子数组的和为 $a$，前$right$个子数组的和为$b$
- 那么下标为 $left$ 到 $right$ （下标从 1 开始）的所有数字和为 $b - a$. （有点前缀和求区间和的思想在里面）

要实现此思路，需要解决一下四个子问题：

**问题一**：**给定一个数组，如何计算该数组中第k小的子数组的和？** （**类似题目：2386.找出数组的第K大和**）

采用二分来实现，其时间复杂度是$O(log(D)))$ ，其中 $D=(sum(nums))$

- 确定二分区间：该数组中第 $k$ 小的子数组的和一定位于$[0, sum(nums)]$之间
- 对于二分的中点$mid$，我们都需要找到子数组和小于$mid$的个数$cnt$
- 如果 $cnt >= k$, 那么 $r = mid$
- 如果 $cnt < k$, 那么 $l = mid + 1$

**问题二**：**对于给定的数组 $nums$，如何快速统计出有多少个子数组的和小于等于$mid$？**

预处理出前缀和数组 $s$ ，并采用双指针来求解，其时间复杂度是 $O(n)$

因为要计算子数组的和，就等于要计算区间和，可以了利用前缀和数组，计算区间和的时间复杂度是 $O(1)$

此问题就转换为了，对于前缀和数组(有序)，有多少个数对的距离小于等于mid？(**类似题目:719.找出第k小的距离对**）

双指针算法, 该双指针算法尝试对于区间$[i + 1, j]$, 对于**任意以下标$j$结尾**的长度不超过$j - i$的子数组, 其元素之和都不超过目标值$x$。

```java
int cnt = 0;
for(int i = 0; j = 0; j < s.length; j ++ ) // 前缀和数组下标是从1开始，但是这里枚举需要从0开始
    while(s[j] - s[i] > mid) i ++ ; // 因为子数组的元素个数也可以是1
    cnt += j - i;
```

到此为止，我们就可以计算出原数组中第k小的子数组和是多少了

**问题三**：**已知第$k$个子数组的和是 $x$, 我们如何快速计算前$k$个子数组的和$sum$，以及它们的个数$count$？**

此问题可以转换为，已知前缀和数组，计算有多少个数对的距离小于等于 $x$, 计算其个数$count$和距离之和$sum$？

双指针算法，该双指针算法尝试枚举对于区间$[i + 1, j]$, 对于**任意从下标$i+1$开始**长度不超过$j - i$的子数组, 其元素之和都不超过目标值$x$。

```java
int cnt = 0, sum = 0;
for(int i = 0; j = 1; i < s.length; i ++ ) // 前缀和数组下标是从1开始，但是这里枚举需要从0开始
    while(j < s.length && s[j] - s[i] < x) j ++ ; // 第一步计算所有严格小于x的子数组的和的个数以及它们的和
    j --;
		cnt += j - i;
    // 如何快速计算区间[i,j]中所有以i为左端点的子数组和的之和？要O(1)而不是O(n)
    // s[j] - s[i] 表示区间[i,j]中所有元素之和
    // sum(s[t] - s[i]), t = i, ..., j
    // <=> (s[i + 1] - s[i]) + (s[i + 2] - s[i]) + ... + (s[j] - s[i])
    // <=> (s[i + 1] + s[i + 2] + ... + s[j]) - s[i] * (j - i)
    // <=> (sp[j] - sp[i]) - s[i] * (j - i) // sp是前缀和的区间和
    sum += (sp[j] - sp[i]) - s[i] * (j - i);
sum = (sum + x * (k - cnt)) % MOD;
```

**问题四**: **已知前$left-1$个子数组的和为$a$，前$right$个子数组的和为$b$，计算下标为$left$到$right$(下标从1开始)的所有数字和**

前缀和求区间和的思想: $b - a$

```java
class Solution {
    final int MOD = (int)1e9 + 7; // 1000000007

    public int rangeSum(int[] nums, int n, int left, int right) {
        int[] s = new int[n + 1];
        int[] sp = new int[n + 1];
        for(int i = 1; i <= n; i ++ ) {
            s[i] = s[i - 1] + nums[i - 1];
            sp[i] = (sp[i - 1] + s[i]);
        }
        return getsum(s, sp, right) - getsum(s, sp, left - 1);
    }

    public int getsum(int[] s, int[] sp, int k) {
        int x = findk(s, k);
        int cnt = 0, sum = 0;
        for(int i = 0, j = 1; i < s.length; i ++ ) { // 第一步计算所有严格小于x的子数组的和的个数以及它们的和
            while(j < s.length && s[j] - s[i] < x) j ++ ;
            j --;
            cnt += (j - i);
          	// 对于区间[i + 1, j], 对于任意从下标i+1开始长度不超过(j - i)的子数组, 其元素之和都不超过目标值x
            sum = (sum + sp[j] - sp[i] - s[i] * (j - i)) % MOD;
        }
        sum = (sum + x * (k - cnt)) % MOD; // 第二步计算所有严格等于x的子数组的和的个数以及它们的和
        return sum;
    }

    public int findk(int[] s, int k) { // 给定一个数组，如何计算该数组中第k小的子数组的和？
        int l = 0, r = s[s.length - 1]; 
        while(l < r) {
            int mid = (l + r) >> 1;
            if(check(s, mid) >= k) r = mid;
            else l = mid + 1;
        }
        return l;
    }

    public int check(int[] s, int x) { // 计算 对于给定的数组 nums，如何快速统计出有多少个子数组的和小于等于x？
        int cnt = 0;
        for(int i = 0, j = 0; j < s.length; j ++ ) {
            while(i < s.length && s[j] - s[i] > x) i ++ ;
          	// 对于区间[i + 1, j], 对于任意以下标j结尾的长度不超过(j - i)的子数组, 其元素之和都不超过目标值x
            if (i < s.length) cnt += (j - i);
        }
        return cnt;
    }
}
```













[1982. 从子集的和还原数组](https://leetcode.cn/problems/find-array-given-subset-sums/)



[1675. 数组的最小偏移量](https://leetcode-cn.com/problems/minimize-deviation-in-array/)



[6455. 使所有字符相等的最小成本](https://leetcode.cn/problems/minimum-cost-to-make-all-characters-equal/)

6456. 矩阵中严格递增的单元格数

[6394. 字符串中的额外字符](https://leetcode.cn/problems/extra-characters-in-a-string/)



### 7、最大化最小值（二分+贪心）

[题单](https://leetcode.cn/problems/minimum-time-to-repair-cars/solutions/2177199/er-fen-da-an-pythonjavacgo-by-endlessche-keqf/?envType=daily-question&envId=2023-09-07) 

#### （1）[2517. 礼盒的最大甜蜜度](https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/)

**问题**：

给你一个正整数数组 `price` ，其中 `price[i]` 表示第 `i` 类糖果的价格，另给你一个正整数 `k` 。

商店组合 `k` 类 **不同** 糖果打包成礼盒出售。礼盒的 **甜蜜度** 是礼盒中任意两种糖果 **价格** 绝对差的最小值。

返回礼盒的 **最大** 甜蜜度*。*

例子：

```
输入：price = [13,5,1,8,21,2], k = 3
输出：8
解释：选出价格分别为 [13,5,21] 的三类糖果。
礼盒的甜蜜度为 min(|13 - 5|, |13 - 21|, |5 - 21|) = min(8, 8, 16) = 8 。
可以证明能够取得的最大甜蜜度就是 8 。
```

**数据范围**：

- `1 <= price.length <= 10^5`
- `1 <= price[i] <= 10^9`
- `2 <= k <= price.length`

**思路**：**最大化最小值问题**

二份答案, 设 $D=max(price)-min(price), O(logD)$

答案的区间一定位于$ [0, max(price)-min(price)]$ 中

二分的中值是$m$什么？ 是否可以找到$k$个数，它们两两之差的绝对值的最小值至少是$m$

- 如果可以找到，那么 $l = mid$
- 如果找不到 $r = mid - 1$;

那么，如何快速找到$k$个数，它们两两之差的绝对值至少是$m$？

对于一个已经排好序的数组$price$, 我们迭代这个数组

对于$price[i]$, 我们计算$price[i]$与上一个已经取走的candy之间的差值, 如果差值大于等于$m$, 我们就取走这个candy。如果最后找不到k个这样的糖果，那么就不存在这样一个方案。

总的时间复杂度$O(n * (logn + logD)$。

```java
class Solution {

    public boolean check(int[] price, int x, int k) {
        int last = price[0];
        k -= 1 ;
        for(int i = 1; i < price.length && k > 0; i ++) { // 对于一个已经排好序的数组price, 我们迭代这个数组
            if(price[i] - last >= x) { // 计算price[i]与上一个已经取走的candy之间的差值, 
                last = price[i]; // 如果差值大于等于x, 我们就取走这个candy
                k -- ;
            }
        }
        return k <= 0; // 找不到k个这样的糖果，那么就不存在这样一个方案
    }

    public int maximumTastiness(int[] price, int k) {
        int n = price.length;
        Arrays.sort(price);

        int l = 0, r = price[n - 1] - price[0]; // 答案一定位于 [0, max(price)-min(price)]区间中

        while(l < r) { // 在区间[l,r]中二份查找答案
            int mid = (l + r + 1) >> 1;
            if(check(price, mid, k)) l = mid; // 可以找到k个数，它们两两之差的绝对值的最小值至少是mid
            else r = mid - 1; // 找不到
        }

        return l;
    }
}
```

#### （2）[1552. 两球之间的磁力](https://leetcode.cn/problems/magnetic-force-between-two-balls/)

**问题**：

在代号为 C-137 的地球上，Rick 发现如果他将两个球放在他新发明的篮子里，它们之间会形成特殊形式的磁力。Rick 有 `n` 个空的篮子，第 `i` 个篮子的位置在 `position[i]` ，Morty 想把 `m` 个球放到这些篮子里，使得任意两球间 **最小磁力** 最大。

已知两个球如果分别位于 `x` 和 `y` ，那么它们之间的磁力为 `|x - y|` 。

给你一个整数数组 `position` 和一个整数 `m` ，请你返回最大化的最小磁力。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/08/16/q3v1.jpg" alt="img" style="zoom:75%;" />

```
输入：position = [1,2,3,4,7], m = 3
输出：3
解释：将 3 个球分别放入位于 1，4 和 7 的三个篮子，两球间的磁力分别为 [3, 3, 6]。最小磁力为 3 。我们没办法让最小磁力大于 3 。
```

**数据范围**：

- `n == position.length`
- `2 <= n <= 10^5`

- `1 <= position[i] <= 10^9`
- 所有 `position` 中的整数 **互不相同** 。
- `2 <= m <= position.length`

**思路**：思路同上题一样**二分+贪心**，最大化最小值问题。

```java
class Solution {

    public boolean check(int[] position, int x, int m) {
        int last = position[0];
        m -- ;
        for(int i = 1; i < position.length && m > 0; i ++ )
            if(position[i] - last >= x) {
                last = position[i];
                m -- ;
            }
        return m <= 0;
    }

    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        
        int n = position.length;
        int l = 0, r = position[n - 1] - position[0]; // 最小最大值一定位于此区间
        while(l < r) {
            int mid = (l + r + 1) >> 1;
            if(check(position, mid, m)) l = mid;
            else r = mid - 1;
        }
        return l;
    }
}
```

#### （3）[2528. 最大化城市的最小供电站数目](https://leetcode.cn/problems/maximize-the-minimum-powered-city/)



### 8、最小化最大值（二分+贪心）

#### （1）[2594. 修车的最少时间](https://leetcode.cn/problems/minimum-time-to-repair-cars/)



#### （2）[2560. 打家劫舍 IV](https://leetcode.cn/problems/house-robber-iv/)



#### （3）[2439. 最小化数组中的最大值](https://leetcode.cn/problems/minimize-maximum-of-array/)



#### （4）[2616. 最小化数对的最大差值](https://leetcode.cn/problems/minimize-the-maximum-difference-of-pairs/)



#### （5）[2513. 最小化两个数组中的最大值](https://leetcode.cn/problems/minimize-the-maximum-of-two-arrays/)



### 9、区间合并（贪心）

#### （1）252. 会议室

**问题**：

给定一个会议时间安排的数组 `intervals` ，每个会议时间都会包括开始和结束的时间`intervals[i] = [starti, endi]` ，请你判断一个人是否能够参加这里面的全部会议。

**思路**：**合并区间**，取**并集**

```python
class Solution:
    def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
        intervals.sort() # 按会议的起始时间从小到大排序
        
        res = [] 
        st, ed = -2e9, -2e9 # [st,ed]表示当前维护的区间,[st,ed]表示并集
        
        for i in range(len(intervals)): # 从左往右合并区间, 取并集
            l, r = intervals[i][0], intervals[i][1]
            
            if ed <= l: # [st,ed]与[l,r]不相交, 例如[[13,15],[1,13]]视为不相交
                if st != -2e9:
                    res.append([st,ed])
                st, ed = l, r
            else: # [st,ed]与[l,r]相交
                ed = max(ed, r) # 合并两个区间, 取并集

        if st != -2e9: 
            res.append([st, ed]) # 将最后一个区间加进去
        return len(res) == len(intervals)
```

#### （2）452. 用最少数量的箭引爆气球

**问题**：

在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以纵坐标并不重要，因此只要知道开始和结束的横坐标就足够了。开始坐标总是小于结束坐标。

一支弓箭可以沿着 $x$ 轴从不同点完全垂直地射出。在坐标 $x$ 处射出一支箭，若有一个气球的直径的开始和结束坐标为$x_{start}$，$x_{end}$， 且满足  $x_{start} ≤ x ≤ x_{end}$，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

给你一个数组 `points` ，其中 $points[i] = [x_{start},x_{end}]$，返回引爆所有气球所必须射出的最小弓箭数。

**思路**：**合并区间**，取**交集**，求不重叠区间的个数

```python
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        points.sort()

        st, ed, cnt = -2e10, -2e10, 0 #[st,ed]表示交集, cnt表示交集的个数
        for i in range(len(points)): # 从左往右合并区间, 取交集
            l, r = points[i][0], points[i][1]
            if ed < l:
                if st != -2e10: # [st,ed]与[l,r]不相交
                    cnt += 1
                st, ed = l, r
            else:
                st, ed = max(st, l), min(ed, r) # [st,ed]与[l,r]相交, 取交集
        if st != -2e10: # 将最后一个区间加进去
            cnt += 1
        return cnt
```

#### （3）435. 无重叠区间

**问题**：

给定一个区间的集合 `intervals` ，其中 `intervals[i] = [starti, endi]` 。返回 *需要移除区间的最小数量，使剩余区间互不重叠* 。

**思路**：**合并区间**，取**交集**，求重叠区间的个数

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        intervals.sort()

        st, ed, cnt = -2e10, -2e10, 0 #[st,ed]表示交集, cnt表示交集的个数,即不重叠区间的个数
        for i in range(len(intervals)): # 从左往右合并区间, 取交集
            l, r = intervals[i][0], intervals[i][1]
            if ed <= l:
                if st != -2e10: # [st,ed]与[l,r]不相交
                    cnt += 1
                st, ed = l, r
            else:
                st, ed = max(st, l), min(ed, r) # [st,ed]与[l,r]不交, 取交集
        if st != -2e10: # 将最后一个区间加进去
            cnt += 1
        return len(intervals) - cnt # 求重叠区间的个数
```

#### （4）[646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/)

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

此题与 *给定一堆课的开始时间和结束时间，问怎么选课可以使得上的课的数量最多* 非常类似。数对的 $left_i$ 可以看作是起始时间，$right_i$ 可以看作是结束时间，起始时间 $left_i$ < 结束时间 $right_i$。数对链的最大长度就等于可以上的数量最多的课。

#### （5）[986. 区间列表的交集](https://leetcode.cn/problems/interval-list-intersections/)

**问题**

给定两个由一些 **闭区间** 组成的列表，`firstList` 和 `secondList` ，其中 `firstList[i] = [starti, endi]` 而 `secondList[j] = [startj, endj]` 。每个区间列表都是成对 **不相交** 的，并且 **已经排序** 。

返回这 **两个区间列表的交集** 。

形式上，**闭区间** `[a, b]`（其中 `a <= b`）表示实数 `x` 的集合，而 `a <= x <= b` 。

两个闭区间的 **交集** 是一组实数，要么为空集，要么为闭区间。例如，`[1, 3]` 和 `[2, 4]` 的交集为 `[2, 3]` 。

例子

<img src="https://assets.leetcode.com/uploads/2019/01/30/interval1.png" alt="img" style="zoom: 50%;" />

```
输入：firstList = [[0,2],[5,10],[13,23],[24,25]], secondList = [[1,5],[8,12],[15,24],[25,26]]
输出：[[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
```

数据范围：

- `0 <= firstList.length, secondList.length <= 1000`
- `firstList.length + secondList.length >= 1`
- `0 <= starti < endi <= 10^9`
- `endi < starti+1`
- `0 <= startj < endj <= 10^9`

**思路**：

**二路归并**与**区间合并取交集**相结合，细节见代码注释

时间复杂度是 $O(N)$

```java
class Solution {
    public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        int[][] a = firstList, b = secondList;
        int n = a.length, m = b.length;

        int i = 0, j = 0;
        List<int[]> t = new ArrayList<>();
        int st = -0x3f3f3f3f, ed = -0x3f3f3f3f;
        while(i < n && j < m) { // 二路归并的思想
            // 以区间的左端点作为二路归并的比较依据
            int l = a[i][0] <= b[j][0] ? a[i][0] : b[j][0];
            int r = a[i][0] <= b[j][0] ? a[i ++ ][1] : b[j ++][1];
            if(ed < l) { // [l, r]与[st,ed]不相交
                st = l; ed = r; // 直接更新[st,ed]为[l,r]
            } else { // [l, r] 与 [st, ed] 存在交集
                // intersection 是[l,r]与[st,ed]相交的部分
                int[] intersection = new int[]{Math.max(st, l), Math.min(ed, r)};
                // 由于firstList和secondList都是已经排好序，且相邻区间是不相交的
                // st 更新为[l,r]与[st,ed]右端点的最小值, ed 更新为[l,r]与[st,ed]右端点的最大值
                // 这样更新的原因是firstList和secondList的下一个区间的左端点一定大于ed或者r
                st = Math.min(ed, r); ed = Math.max(ed, r);
                t.add(intersection);
            }
        }

        while(i < n) { // firstList还存在部分区间没有考虑
            int l = a[i][0], r = a[i ++][1];
            if(ed < l) break;
            else {
                int[] intersection = new int[]{Math.max(st, l), Math.min(ed, r)};
                st = Math.min(ed, r); ed = Math.max(ed, r);
                t.add(intersection);
            }
        }

        while(j < m) { // secondList还存在部分区间没有考虑
            int l = b[j][0], r = b[j ++][1];
            if(ed < l) break;
            else {
                int[] intersection = new int[]{Math.max(st, l), Math.min(ed, r)};
                st = Math.min(ed, r); ed = Math.max(ed, r);
                t.add(intersection);
            }
        }

        int[][] res = new int[t.size()][2];
        for(int k = 0; k < t.size(); k ++)
            res[k] = t.get(k);
        return res;
    }
}
```

#### （6）[2848. 与车相交的点](https://leetcode.cn/problems/points-that-intersect-with-cars/)



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

