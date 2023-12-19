# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第四部分之最小生成树

### 1、Kruskal  算法

#### （1）1168. 水资源分配优化:writing_hand: 

**问题**：

村里面一共有 `n` 栋房子。我们希望通过建造水井和铺设管道来为所有房子供水。

对于每个房子 `i`，我们有两种可选的供水方案：

- 一种是直接在房子内建造水井，成本为 `wells[i]`；
- 另一种是从另一口井铺设管道引水，数组 `pipes` 给出了在房子间铺设管道的成本，其中每个 `pipes[i] = [house1, house2, cost]` 代表用管道将 `house1` 和 `house2` 连接在一起的成本。当然，连接是双向的。

请你帮忙计算为所有房子都供水的最低总成本。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203121355256.png" alt="img" width="160" />

**注意**：

- `1 <= n <= 10000`
- `wells.length == n`
- `1 <= pipes.length <= 10000`
- `1 <= pipes[i][0], pipes[i][1] <= n`
- `pipes[i][0] != pipes[i][1]`

**稀疏图**且**无自环**，重边不影响 `kruskal` 算法求最小生成树

**思路**：`Kruskal `算法

建立一个虚拟头结点，编号为0，可以连接到任何的房子，虚拟头结点0到结点`i`的边权`weight[i]`是挖水井的消耗`wells[i]`。

因此就变成了一个非常普通的 `Kruskal` 问题，求最小生成树

```java
class Edge {
    int a, b, w;
    Edge(int a, int b, int w) {
        this.a = a; this.b = b; this.w = w;
    }
}

class Solution {

    int[] p; // 并查集

    public int find(int x) { // 返回x的祖宗结点
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public int minCostToSupplyWater(int n, int[] wells, int[][] pipes) {
        p = new int[n + 1]; // 加入一个虚拟结点, 编号为0, 房子编号从1开始
        for(int i = 0; i < p.length; i ++) p[i] = i; // 初始化并查集

        Edge[] edges = new Edge[n + pipes.length]; // 虚拟结点向每一个结点连一条边, 边权为wells[i]
        int idx = 0;
        for(int i = 0; i < wells.length; i ++) 
            edges[idx ++] = new Edge(0, i + 1, wells[i]);
        for(int i = 0; i < pipes.length; i ++)
            edges[idx ++] = new Edge(pipes[i][0], pipes[i][1], pipes[i][2]);
        
        Arrays.sort(edges, (e1, e2) -> e1.w - e2.w); // 所有边按权值从小到大排序

        int res = 0; // res生成树的最小边权
        for(int i = 0; i < edges.length; i ++ ) { // 求最小生成树的边权之和 
            int x = find(edges[i].a), y = find(edges[i].b);
            if(x != y) {
                p[x] = y;
                res += edges[i].w;
            }
        }
        return res;
    }
}
```

#### （2）1135. 最低成本联通所有城市

问题：

想象一下你是个城市基建规划者，地图上有 `n` 座城市，它们按以 `1` 到 `n` 的次序编号。

给你整数 `n` 和一个数组 `conections`，其中 `connections[i] = [xi, yi, costi]` 表示将城市 `xi` 和城市 `yi` 连接所要的`costi`（**连接是双向的**）。

返回连接所有城市的**最低成本**，每对城市之间**至少**有一条路径。如果无法连接所有 `n` 个城市，返回 `-1`

该 **最小成本** 应该是所用全部连接成本的总和。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203121404518.png" alt="img" width="150" />

```
输入：n = 3, conections = [[1,2,5],[1,3,6],[2,3,1]]
输出：6
```

注意：

- `1 <= n <= 10^4`
- `1 <= connections.length <= 10^4`
- `1 <= xi, yi <= n`
- `xi != yi`

稀疏图且无自环

**思路**：`Kruskal` 算法，求最小生成树

```java
class Solution {

    int[] p; // 并查集

    public int find(int x){ // 返回x的祖宗结点
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public int minimumCost(int n, int[][] connections) {
        p  = new int[n + 1];
        for(int i = 0; i < p.length; i ++) p[i] = i; // 初始化并查集

        Arrays.sort(connections, (e1, e2) -> e1[2] - e2[2]); // 按权值从小到大排序所有的边

        int res = 0, cnt = 0;
        for(int i = 0; i < connections.length; i ++){
            int x = find(connections[i][0]), y = find(connections[i][1]), w = connections[i][2];
            if(x != y) { // 合并两个集合
                p[x] = y;
                res += w;
                cnt ++ ;
            }
        }
        if(cnt < n - 1) return -1; // 非连通图
        return res;
    }
}
```

#### （3）[1584. 连接所有点的最小费用](https://leetcode.cn/problems/min-cost-to-connect-all-points/)





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
