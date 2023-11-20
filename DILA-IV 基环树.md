# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之基环树

### 1、基环树

#### （1）[2360. 图中的最长环 ](https://leetcode.cn/problems/longest-cycle-in-a-graph/) 

**问题**

给你一个 `n` 个节点的 **有向图** ，节点编号为 `0` 到 `n - 1` ，其中每个节点 **至多** 有一条出边。

图用一个大小为 `n` 下标从 **0** 开始的数组 `edges` 表示，节点 `i` 到节点 `edges[i]` 之间有一条有向边。如果节点 `i` 没有出边，那么 `edges[i] == -1` 。

请你返回图中的 **最长** 环，如果没有任何环，请返回 `-1` 。

一个环指的是起点和终点是 **同一个** 节点的路径。

例子

![img](https://assets.leetcode.com/uploads/2022/06/08/graph4drawio-5.png)

```
输入：edges = [3,3,4,2,3]
输出去：3
解释：图中的最长环是：2 -> 4 -> 3 -> 2 。
这个环的长度为 3 ，所以返回 3 。
```

**数据范围**：

- `n == edges.length`
- `2 <= n <= 10^5`
- `-1 <= edges[i] < n`
- `edges[i] != i`

**思路**：

```java
class Solution {

    int[] d; // d[i] 表示节点i的入度

    public boolean topsort(int n, int ne[]) {
        int[] q = new int[n + 10];
        int hh = 0, tt = -1;

        // 所有入度为0的节点入队列
        for(int i = 0; i < n; i ++)
            if(d[i] == 0)
                q[++ tt] = i;
    
        while(hh <= tt) {
            int t = q[hh ++];
            if(ne[t] == -1) continue;
            int j = ne[t];
            d[j] --;
            if(d[j] == 0) q[++ tt] = j;
        }

        return tt + 1 == n; // return true 表示该基环森林没有环
    }

    public int longestCycle(int[] edges) {
        // edges 内向基环树, 每个点只发出一条有向边, edges[i]表示节点i指向 edges[i]的一条有向边
        int n = edges.length;
        
        // 统计所有节点的入度
        d = new int[n + 10];
        for(int i = 0; i < n; i ++) {
            int a = i, b = edges[i];
            if(b == -1) continue;
            d[b] ++;
        }

        if(topsort(n, edges)) return -1;

        // 枚举基环森林中的所有环
        int res = -1;
        for(int start = 0; start < n; start ++) {
            if(d[start] <= 0) continue;

            int len = 0;
            for(int i = start; ; i = edges[i]) {
                d[i] = -1;
                len ++;
                if(edges[i] == start) break;
            }
            res = Math.max(res, len); // 最长的环
        }

        return res;
    }
}
```

#### （2）[2127. 参加会议的最多员工数](https://leetcode.cn/problems/maximum-employees-to-be-invited-to-a-meeting/)

**问题**

一个公司准备组织一场会议，邀请名单上有 `n` 位员工。公司准备了一张 **圆形** 的桌子，可以坐下 **任意数目** 的员工。

员工编号为 `0` 到 `n - 1` 。每位员工都有一位 **喜欢** 的员工，每位员工 **当且仅当** 他被安排在喜欢员工的旁边，他才会参加会议。每位员工喜欢的员工 **不会** 是他自己。

给你一个下标从 **0** 开始的整数数组 `favorite` ，其中 `favorite[i]` 表示第 `i` 位员工喜欢的员工。请你返回参加会议的 **最多员工数目** 。

例子

![img](https://assets.leetcode.com/uploads/2021/12/14/ex1.png)

```
输入：favorite = [2,2,1,2]
输出：3
解释：
上图展示了公司邀请员工 0，1 和 2 参加会议以及他们在圆桌上的座位。
没办法邀请所有员工参与会议，因为员工 2 没办法同时坐在 0，1 和 3 员工的旁边。
注意，公司也可以邀请员工 1，2 和 3 参加会议。
所以最多参加会议的员工数目为 3 。
```

**数据范围**：

- `n == favorite.length`
- `2 <= n <= 10^5`
- `0 <= favorite[i] <= n - 1`
- `favorite[i] != i`

**思路**：

```java
class Solution {

    int[] d; // d[i] 表示节点i的入度
    ArrayList<Integer>[] rene; // 反图

    public boolean topsort(int n, int[] ne) {
        int[] q = new int[n + 10];
        int hh = 0, tt = -1;
        
        // 所有入度为0的节点入队列
        for(int i = 0; i < n; i ++)
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++];
            int j = ne[t];
            d[j] --;
            if(d[j] == 0) q[++ tt] = j;
        }

        return tt + 1 == n; // return true 表示该基环森林没有环
    }

    // 在反图上遍历
    public int rdfs(int u, int depth) { // 返回该内内向基环树中最长的树枝长度
        if(rene[u] == null) return depth;;

        int res = depth;
        for(Integer v : rene[u])
            if(d[v] == 0)
                res = Math.max(res, rdfs(v, depth + 1));
        return res;
    }

    public int maximumInvitations(int[] favorite) {
        // favorite 内向基环树, 每个点只发出一条有向边, favorite[i]表示节点i指向favorite[i]的一条有向边
        int n = favorite.length;

        d = new int[n + 10];
        rene = new ArrayList[n + 10];

        for(int i = 0; i < n; i ++) {
            int a = i, b = favorite[i];
            d[b] ++ ;

            if(rene[b] == null) rene[b] = new ArrayList<Integer>();
            rene[b].add(a);
        }

        if(topsort(n, favorite)) return 0; // 没有环，直接返回0

        int ans = 0, res = 0;
        for(int start = 0; start < n; start ++) { // 枚举基环森林中的每一颗基环树
            if(d[start] <= 0) continue;

            // 收集内向基环树中环上的点
            List<Integer> ring = new ArrayList<>(); // 存储环上的节点
            for(int i = start; ; i = favorite[i]) {
                d[i] = -1; // 标记
                ring.add(i);
                if(favorite[i] == start) break;
            }

            int len = ring.size();
            if(len == 2) { // 基环树中的环如果有2个点
                // 以环上的点为起点，向非环上的点遍历
                for(Integer u : ring) 
                    len += rdfs(u, 0); // 累加每个节点对应的最长树枝
                res += len;
            } else ans = Math.max(ans, len); // 基环树中的环如果有超过2个节点
        }
        ans = Math.max(ans, res);
        return ans;
    }
}
```

#### （3）[2876. 有向图访问计数](https://leetcode.cn/problems/count-visited-nodes-in-a-directed-graph/)

**问题**

现有一个有向图，其中包含 `n` 个节点，节点编号从 `0` 到 `n - 1` 。此外，该图还包含了 `n` 条有向边。

给你一个下标从 **0** 开始的数组 `edges` ，其中 `edges[i]` 表示存在一条从节点 `i` 到节点 `edges[i]` 的边。

想象在图上发生以下过程：

- 你从节点 `x` 开始，通过边访问其他节点，直到你在 **此过程** 中再次访问到之前已经访问过的节点。

返回数组 `answer` 作为答案，其中 `answer[i]` 表示如果从节点 `i` 开始执行该过程，你可以访问到的不同节点数。

例子

![img](https://assets.leetcode.com/uploads/2023/08/31/graaphdrawio-1.png)

```
输入：edges = [1,2,0,0]
输出：[3,3,3,4]
解释：从每个节点开始执行该过程，记录如下：
- 从节点 0 开始，访问节点 0 -> 1 -> 2 -> 0 。访问的不同节点数是 3 。
- 从节点 1 开始，访问节点 1 -> 2 -> 0 -> 1 。访问的不同节点数是 3 。
- 从节点 2 开始，访问节点 2 -> 0 -> 1 -> 2 。访问的不同节点数是 3 。
- 从节点 3 开始，访问节点 3 -> 0 -> 1 -> 2 -> 0 。访问的不同节点数是 4 。
```

**数据范围**：

- `n == edges.length`
- `2 <= n <= 10^5`
- `0 <= edges[i] <= n - 1`
- `edges[i] != i`

**思路**：

```java
class Solution {

    int[] ne; // 内向基环树, 每个点只发出一条有向边, ne[i]表示i指向ne[i]的一条有向边
    ArrayList<Integer>[] rene; // 反图
    int[] d; // d[i] 表示节点i的入度,

    public void topsort(int n) {
        int[] q = new int[n + 10];
        int hh = 0, tt = -1;

        // 所有入度为0的节点入队列
        for(int i = 0; i < n; i ++)
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++];
            int j = ne[t];
            d[j] --;
            if(d[j] == 0) q[++ tt] = j;
        }
    }

    // 在反图上遍历
    public void rdfs(int u, int depth, int[] res) {
        res[u] = depth;
        if(rene[u] == null) return;
        for(Integer v : rene[u])
            if(d[v] == 0)
                rdfs(v, depth + 1, res);
    }


    public int[] countVisitedNodes(List<Integer> edges) {
        int n = edges.size();
        
        ne = new int[n + 10];
        rene = new ArrayList[n + 10];
        d = new int[n + 10];
        
        // 建图
        for(int i = 0; i < n; i ++) {
            int a = i, b = edges.get(i);
            ne[a] = b; 

            if(rene[b] == null) rene[b] = new ArrayList<Integer>();
            rene[b].add(a);

            d[b] ++ ;
        }

        // 拓扑排序, 剪枝
        topsort(n);

        int[] res = new int[n];

        // 找环上的任意一点
        for(int start = 0; start < n; start ++) {
            if(d[start] <= 0) continue;

            // 收集环上的点
            List<Integer> ring = new ArrayList<>(); 
            for(int i = start; ; i = ne[i]) {
                d[i] = -1;
                ring.add(i);
                if(ne[i] == start) break;
            }

            // 以环上的点为起点，向非环上的点遍历
            for(Integer u : ring) 
                rdfs(u, ring.size(), res);
        }

        return res;
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
