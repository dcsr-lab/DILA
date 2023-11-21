# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之拓扑排序

### 1、拓扑排序

#### （1）5300. 有向无环图中一个节点的所有祖先

**问题**：

给你一个正整数 `n` ，它表示一个 **有向无环图** 中节点的数目，节点编号为 `0` 到 `n - 1` （包括两者）。

给你一个二维整数数组 `edges` ，其中 `edges[i] = [fromi, toi]` 表示图中一条从 `fromi` 到 `toi` 的单向边。

请你返回一个数组 `answer`，其中 `answer[i]`是第 `i` 个节点的所有 **祖先** ，这些祖先节点 **升序** 排序。

如果 `u` 通过一系列边，能够到达 `v` ，那么我们称节点 `u` 是节点 `v` 的 **祖先** 节点。

<img src="https://assets.leetcode.com/uploads/2019/12/12/e1.png" alt="img" width="270px" />

**思路**：拓扑排序

- 基于 **拓扑排序** 从祖先节点出发依次遍历后代节点
- 当前节点可直接利用父节点的祖先信息，减少重复计算

```java
class Solution {
    
    int[] h, e, ne; // 邻接表
    int idx, n, m; 
    int[] q, d; // q 是队列, d[i]表示结点i的入队
    
    public void add(int a, int b){ // 添加a->b的一条有向边
        e[idx] = b; ne[idx] = h[a]; h[a] = idx++;
    }
    
    public List<List<Integer>> topsort(){
        Map<Integer, Set<Integer>> hash = new HashMap<>(); // 记录结点key的所有祖宗结点为value
        
        int hh = 0, tt = -1; // 队列初始化    
        /* 找到所有度为 0 的结点入队列 */
        for(int i = 0; i < n; i ++ )
            if(d[i] == 0){
                q[++ tt] = i;
                hash.put(i, new TreeSet<Integer>()); // 初始化hash
            }
            
        while(hh <= tt){
            int t = q[hh ++]; // 队头元素出队列      
            Set<Integer> tp = hash.get(t);
            
            for(int i = h[t]; i != -1; i = ne[i]){ // 遍历 t 的所有出边
                int j = e[i];
                d[j] --; // 删除从 t->j 的边
                
                Set<Integer> tmp = hash.getOrDefault(j, new TreeSet<Integer>());
                tmp.add(t);
                for(Integer val : tp) tmp.add(val);
                hash.put(j, tmp); // 更新j的祖宗结点, 包括t及其t的祖宗结点
                
                if(d[j] == 0) q[++ tt] = j; // j 之前的结点已经放好位置了
            }
        }   
        
        List<List<Integer>> res = new ArrayList<>();
        for(int i = 0; i < n; i ++)
            res.add(new ArrayList<>(hash.get(i)));
        return res;
    }
    
    public List<List<Integer>> getAncestors(int n, int[][] edges) {
        this.n = n; m = edges.length;
        h = new int[n + 10]; e = new int[m + 10]; ne = new int[m + 10];
        q = new int[n + 10]; d = new int[n + 10];
        
        Arrays.fill(h, -1); // 初始化头指针数组
        
        for(int i = 0; i < m; i ++) {
            int a = edges[i][0], b = edges[i][1];
            add(a, b);
            d[b] ++ ;
        }
        
        return topsort();
    }
}
```

#### （2）[207. 课程表](https://leetcode.cn/problems/course-schedule/)

**问题**

你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi]` ，表示如果要学习课程 `ai` 则 **必须** 先学习课程 `bi` 。

- 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。

**请你判断是否可能完成所有课程的学习？**如果可以，返回 `true` ；否则，返回 `false` 。

示例

```
输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：false
解释：总共有 2 门课程。学习课程 1 之前，你需要先完成课程 0 ；并且学习课程 0 之前，你还应先完成课程 1 。这是不可能的。
```

**数据范围**

- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= 5000`
- `prerequisites[i].length == 2`
- `0 <= ai, bi < numCourses`
- `prerequisites[i]` 中的所有课程对 **互不相同**

**思路** 判断是否存在一个合理的拓扑序列

```java
class Solution {

    final int N = 5010;
    int[] h = new int[N], e = new int[N], ne = new int[N]; // 邻接表
    int[] d = new int[N], q = new int[N];
    int idx = 0;

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++;
    }

    public boolean topsort(int n) {
        int hh = 0, tt = -1; // 初始化队头指针和队尾指针

        for(int i = 0; i < n; i ++ ) // 寻找入度为0的点
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++]; // 队头元素出队列

            // 遍历t的所有出边
            for(int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j] --; // 删除从t->j的边
                if(d[j] == 0) q[++ tt] = j;
            }
        }
        return tt == n - 1;
    }

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int n = numCourses;
        Arrays.fill(h, -1);

        for(int i = 0; i < prerequisites.length; i ++) {
            int a = prerequisites[i][0], b = prerequisites[i][1];
            add(b, a); // 要修课程a, 必修先修课程b，增加一条从b指向a的边
            d[a] ++; // a节点的入度加1
        }

        return topsort(n);
    }
}
```

#### （3）[210. 课程表 II](https://leetcode.cn/problems/course-schedule-ii/)

**问题**

现在你总共有 `numCourses` 门课需要选，记为 `0` 到 `numCourses - 1`。给你一个数组 `prerequisites` ，其中 `prerequisites[i] = [ai, bi]` ，表示在选修课程 `ai` 前 **必须** 先选修 `bi` 。

-  例如，想要学习课程 `0` ，你需要先完成课程 `1` ，我们用一个匹配来表示：`[0,1]` 。

返回你为了学完所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回 **任意一种** 就可以了。如果不可能完成所有课程，返回 **一个空数组** 。

示例

```
输入：numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
输出：[0,2,1,3]
解释：总共有 4 门课程。要学习课程 3，你应该先完成课程 1 和课程 2。并且课程 1 和课程 2 都应该排在课程 0 之后。
因此，一个正确的课程顺序是 [0,1,2,3] 。另一个正确的排序是 [0,2,1,3] 。
```

**数据范围**

- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
- `prerequisites[i].length == 2`
- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
- `prerequisites[i].length == 2`

**思路**：求出任意一个可行的拓扑序列

```java
class Solution {

    int[] h, e, ne; // 邻接表
    int idx = 0;
    int[] q, d;

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public int[] topsort(int n) {
        int hh = 0, tt = -1;

        for(int i = 0; i < n; i ++) 
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++];
            // 遍历t的所有出边
            for(int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j] -- ; // 删除从t->j的边
                if(d[j] == 0) q[++ tt] = j;
            }
        }
        if(tt != n - 1) return new int[0];
        return q;
    }

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int n = numCourses;

        h = new int[n + 10];
        e = new int[n * n  +10];
        ne = new int[n * n + 10];

        q = new int[n];
        d = new int[n];

        Arrays.fill(h, -1);

        for(int i = 0; i < prerequisites.length; i ++) {
            int a = prerequisites[i][0], b = prerequisites[i][1];
            add(b, a); // 要修课程a, 必修先修课程b，增加一条从b指向a的边
            d[a] ++ ; // a节点的入度加1
        }

        return topsort(n);
    }
}
```

#### （4）课程表 III



#### （5）[1462. 课程表 IV](https://leetcode.cn/problems/course-schedule-iv/)

**问题**

你总共需要上 `numCourses` 门课，课程编号依次为 `0` 到 `numCourses-1` 。你会得到一个数组 `prerequisite` ，其中 `prerequisites[i] = [ai, bi]` 表示如果你想选 `bi` 课程，你 **必须** 先选 `ai` 课程。

- 有的课会有直接的先修课程，比如如果想上课程 `1` ，你必须先上课程 `0` ，那么会以 `[0,1]` 数对的形式给出先修课程数对。

先决条件也可以是 **间接** 的。如果课程 `a` 是课程 `b` 的先决条件，课程 `b` 是课程 `c` 的先决条件，那么课程 `a` 就是课程 `c` 的先决条件。

你也得到一个数组 `queries` ，其中 `queries[j] = [uj, vj]`。对于第 `j` 个查询，您应该回答课程 `uj` 是否是课程 `vj` 的先决条件。

返回一个布尔数组 `answer` ，其中 `answer[j]` 是第 `j` 个查询的答案。

示例

<img src="https://assets.leetcode.com/uploads/2021/05/01/courses4-3-graph.jpg" alt="img" style="zoom:50%;" />

```
输入：numCourses = 3, prerequisites = [[1,2],[1,0],[2,0]], queries = [[1,0],[1,2]]
输出：[true,true]
```

**数据范围**

- `2 <= numCourses <= 100`
- `0 <= prerequisites.length <= (numCourses * (numCourses - 1) / 2)`
- `prerequisites[i].length == 2`
- `0 <= ai, bi <= n - 1`
- `ai != bi`
- 每一对 `[ai, bi]` 都 **不同**
- 先修课程图中没有环。
- `1 <= queries.length <= 104`
- `0 <= ui, vi <= n - 1`
- `ui != vi`

**思路**：拓扑排序

```java
class Solution {
    
    boolean[][] condition;

    int[] h, ne, e;
    int idx = 0;
    int[] d, q;

    public void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public void topsort(int n) {
        int hh = 0, tt = -1;

        for(int i = 0; i < n; i ++) 
            if(d[i] == 0)
                q[++ tt] = i;

        while(hh <= tt) {
            int t = q[hh ++];
            // 遍历t的所有出边
            for(int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j] -- ; // 删除从t->j的边

                condition[t][j] = true;
                for(int k = 0; k < n; k ++)
                    condition[k][j] = (condition[k][j] | condition[k][t]);
                    
                if(d[j] == 0) q[++ tt] = j;
            }
        }
    }

    public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
        int n = numCourses;
        condition = new boolean[n][n];

        h = new int[n];
        ne = new int[n * n];
        e = new int[n * n];

        d = new int[n];
        q = new int[n + 10];

        Arrays.fill(h, -1);

        for(int i = 0; i < prerequisites.length; i ++ ) {
            int a = prerequisites[i][0], b = prerequisites[i][1];
            add(a, b);
            d[b] ++ ;
        }

        topsort(n);

        List<Boolean> res = new ArrayList<>();
        for(int i = 0; i < queries.length; i ++) {
            int a = queries[i][0], b = queries[i][1];
            res.add(condition[a][b]);
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
