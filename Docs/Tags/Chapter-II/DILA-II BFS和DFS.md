# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>
> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之BFS和DFS



### 1、BFS

#### （1）[1091. 二进制矩阵中的最短路径](https://leetcode.cn/problems/shortest-path-in-binary-matrix/)

**问题**：

给你一个 `n x n` 的二进制矩阵 `grid` 中，返回矩阵中最短 **畅通路径** 的长度。如果不存在这样的路径，返回 `-1` 。二进制矩阵中的 畅通路径 是一条从 **左上角** 单元格（即，`(0, 0)`）到 右下角 单元格（即，`(n - 1, n - 1)`）的路径，该路径同时满足下述要求：

- 路径途经的所有单元格都的值都是 `0` 。
- 路径中所有相邻的单元格应当在 **8 个方向之一** 上连通（即，相邻两单元之间彼此不同且共享一条边或者一个角）。

**畅通路径的长度** 是该路径途经的单元格总数。

例子：

<img src="https://assets.leetcode.com/uploads/2021/02/18/example2_1.png" alt="img" style="zoom: 25%;" />

```
输入：grid = [[0,0,0],[1,1,0],[1,1,0]]
输出：4
```

**数据范围**：

- `n == grid.length`
- `n == grid[i].length`
- `1 <= n <= 100`
- `grid[i][j]` 为 `0` 或 `1`

**思路**：广度优先搜索

**8 个方向定义** 

```java
int[] dx = new int[]{1, 1, 0, -1, -1, -1, 0, 1};
int[] dy = new int[]{0, 1, 1, 1, 0, -1, -1, -1};
```

完整代码

```java
class Solution {

    public int bfs(int[][] g, int n) {

        if(g[0][0] == 1) return -1; // 特判
        if(g[0][0] == 0 && n == 1) return 1; // 特判

        int hh = 0, tt = 0;
        Pair<Integer, Integer>[] q = new Pair[n * n];
        q[0] = new Pair<Integer, Integer>(0, 0);
        Map<Pair<Integer, Integer>, Boolean> st = new HashMap<>();

        int[] dx = new int[]{1, 1, 0, -1, -1, -1, 0, 1};
        int[] dy = new int[]{0, 1, 1, 1, 0, -1, -1, -1};
        int layer = 1;
        while(hh <= tt) {
            int head = hh, tail = tt;
            layer += 1;
            while(hh <= tail) {
                Pair<Integer, Integer> t = q[hh ++];

                for(int i = 0; i < dx.length; i ++) {
                    int tx = t.getKey() + dx[i];
                    int ty = t.getValue() + dy[i];
                
                    if(tx >= n || tx < 0 || ty >= n || ty < 0) continue;
                    if(g[tx][ty] == 1) continue;
                    if(st.getOrDefault(new Pair<Integer, Integer>(tx, ty), false)) continue;

                    st.put(new Pair<Integer, Integer>(tx, ty), true);
                    q[++ tt] = new Pair<Integer, Integer>(tx, ty);

                    if(tx == n - 1 && ty == n - 1) return layer;
                }
            }
        }

        return -1;
    }

    public int shortestPathBinaryMatrix(int[][] grid) {
        return bfs(grid, grid.length);
    }
}
```

#### （2）[LCP 41. 黑白翻转棋](https://leetcode.cn/problems/fHi6rV/) （:o:）

**问题**：

在 `n*m` 大小的棋盘中，有黑白两种棋子，黑棋记作字母 `"X"`, 白棋记作字母 `"O"`，空余位置记作 `"."`。当落下的棋子与其他相同颜色的棋子在行、列或对角线完全包围（中间不存在空白位置）另一种颜色的棋子，则可以翻转这些棋子的颜色。

<img src="https://pic.leetcode-cn.com/1630396029-eTgzpN-6da662e67368466a96d203f67bb6e793.gif" alt="1.gif" style="zoom:40%;" />

<img src="https://pic.leetcode-cn.com/1630396240-nMvdcc-8e4261afe9f60e05a4f740694b439b6b.gif" alt="2.gif" style="zoom:40%;" />

<img src="https://pic.leetcode-cn.com/1630396291-kEtzLL-6fcb682daeecb5c3f56eb88b23c81d33.gif" alt="3.gif" style="zoom:40%;" />

「力扣挑战赛」黑白翻转棋项目中，将提供给选手一个未形成可翻转棋子的棋盘残局，其状态记作 `chessboard`。若下一步可放置一枚黑棋，请问选手最多能翻转多少枚白棋。

**注意**：

- 若翻转白棋成黑棋后，棋盘上仍存在可以翻转的白棋，将可以 **继续** 翻转白棋
- 输入数据保证初始棋盘状态无可以翻转的棋子且存在空余位置

例子：

```
输入：chessboard = [".......",".......",".......","X......",".O.....","..O....","....OOX"]

输出：4

解释：
可以选择下在 [6,3] 处，能够翻转白方四枚棋子。
```

![803f2f04098b6174397d6c696f54d709.gif](https://pic.leetcode-cn.com/1630393770-Puyked-803f2f04098b6174397d6c696f54d709.gif)

**数据范围**：

- `1 <= chessboard.length, chessboard[i].length <= 8`
- `chessboard[i]` 仅包含 `"."、"O"` 和 `"X"`

**思路**：**此题考查的是BFS的思想**

题目给出的是一个大小为 $n \times m$的黑白棋盘chessboard，其中黑棋用‘X’，白棋用'O'表示，空余位置用'.'表示。

现在我们可以在棋盘中的一个空余位置下一个黑棋，现在我们称「翻转操作」为：**若存在在放置黑棋的位置的行、列或者对角线有另一颗黑棋能完全包围（中间不存在空白位置）白棋，那么我们可以翻转这些白棋，使其变为黑棋，并且这些翻转后得到的新黑棋同样可以进行「翻转操作」。**现在我们需要求在放置一次黑棋的情况下最多能翻转多少颗白棋。

我们可以用「广度优先搜索」来解决这个问题，我们对每一个空余位置尝试黑棋放置，用一个队列来存储正在进行「翻转操作」的黑棋位置，若队列非空，我们**从队列中取出队首元素，进行行、列和对角线 8 个方向判断是否有可以翻转的白棋——判断沿着方向是否是连续的一段白棋并以另一颗黑棋结尾。若有可以翻转的白棋，则将这些白旗进行翻转，并加入队列中**。直至队列为空表示一次放置黑棋结束。

初始可以放置黑棋的全部位置中可以翻转的白棋个数最大值即为最后的答案。

```java
class Solution {

    public boolean check(char[][] g, boolean[][] st, int tx, int ty, int dx, int dy, List<Pair<Integer, Integer>> arr) {
        int n = g.length, m = g[0].length;
        int x = tx, y = ty;
        do {
            tx += dx; ty += dy;
            if(tx < 0 || tx >= n || ty < 0 || ty >= m) return false; // 越界
            if(g[tx][ty] == '.') return false; // 空白
            if(st[tx][ty] || g[tx][ty] == 'X') return true; // 遇到黑棋则返回
            arr.add(new Pair<Integer, Integer>(tx, ty)); // 将所有的连续的白棋放入arr中
            
        } while(true);
    }

    public int bfs(char[][] g, int x, int y) { // 返回以(x,y)位置作为落子的位置，能够翻转的白棋数
        int n = g.length, m = g[0].length;

        Pair<Integer, Integer>[] q = new Pair[n * m]; // 队列
        int hh = 0, tt = 0; // 队头指针和队尾指针
        q[0] = new Pair<Integer, Integer>(x, y); // 初始化队列

        boolean[][] st = new boolean[n][m]; // 如果(i,j)位置上的白棋被翻转为黑棋，则st[i][j]为true

        int[] dx = {0, 1, 1, 1, 0, -1, -1, -1}, dy = {1, 1, 0, -1, -1, -1, 0, 1};

        while(hh <= tt) { // 队列不为空
            var t = q[hh ++]; // 取出队头元素
            int tx = t.getKey(), ty = t.getValue();

            for(int i  = 0; i < dx.length; i ++ ) { // 枚举八个方向，从当前位置(dx,ty)出发，翻转白棋    
                List<Pair<Integer, Integer>> arr = new ArrayList<>();
                if(check(g, st, tx, ty, dx[i], dy[i], arr)) {
                    for(var e : arr) {
                        q[++ tt] = e; // 将该方向上所有可以翻转的白棋，入队
                        st[e.getKey()][e.getValue()] = true; // 更新判重数组
                    }
                }
            }
        }
        return tt;
    }

    public int flipChess(String[] chessboard) {
        int n = chessboard.length, m = chessboard[0].length();

        char[][] g = new char[n][m];
        for(int i = 0; i < n; i ++)
            g[i] = chessboard[i].toCharArray(); // 转换成字符数组

        int res = 0;
        for(int i = 0; i < n; i ++ ) { // 将棋盘中所有的空位置作为落子的位置，搜索最多能翻转的白棋数
            for(int j = 0; j < m ;j ++ )
                if(g[i][j] == '.') res = Math.max(res, bfs(g, i, j));
        }
        return res;
    }
}
```



### 2、DFS

#### （1）[980. 不同路径 III](https://leetcode.cn/problems/unique-paths-iii/)

**问题**：

在二维网格 `grid` 上，有 4 种类型的方格：

- `1` 表示起始方格。且只有一个起始方格。
- `2` 表示结束方格，且只有一个结束方格。
- `0` 表示我们可以走过的空方格。
- `-1` 表示我们无法跨越的障碍。

返回在四个方向（上、下、左、右）上行走时，从起始方格到结束方格的不同路径的数目**。**

**每一个无障碍方格都要通过一次，但是一条路径中不能重复通过同一个方格**。

 例子

```java
输入：[[1,0,0,0],[0,0,0,0],[0,0,2,-1]]
输出：2
解释：我们有以下两条路径：
1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2)
2. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2)
```

**数据范围**：

- `1 <= grid.length * grid[0].length <= 20`

**思路**：经典的DFS遍历矩阵问题的变体d

```java
class Solution {
    int[] start, end;
    int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
    boolean[][] st;
    int[][] g;
    int n = 0, m = 0, cnt = 0;

    public int dfs(int x, int y, int k) {
        if(x == end[0] && y == end[1]) return k - 1 == cnt ? 1 : 0; // 到达目的，并且核对所有空位置是否都走过

        int res = 0;
        for(int i = 0; i < dx.length; i ++) {
            int tx = x + dx[i], ty = y + dy[i];
            if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue;
            if(g[tx][ty] == -1) continue;
            if(st[tx][ty]) continue;

            st[tx][ty] = true;
            res += dfs(tx, ty, k + 1);
            st[tx][ty] = false;
        }
        return res;
    }
 
    public int uniquePathsIII(int[][] grid) {
        n = grid.length; m = grid[0].length;
        st = new boolean[n][m];
        g = grid;
        int barrier = 0;

        for(int i = 0; i < n; i ++)
            for(int j = 0; j < m; j ++) {
                if(grid[i][j] == 1) start = new int[]{i, j};
                if(grid[i][j] == 2) end = new int[]{i, j};
                if(grid[i][j] == -1) barrier ++;
            }
        
        cnt = n * m - 2 - barrier;
        st[start[0]][start[1]] = true;
        int res = dfs(start[0], start[1], 0);
        return res;
    }
}
// [[1,0,0,0],
//  [0,0,0,0],
//  [0,0,2,-1]]
```

#### （2）[1457. 二叉树中的伪回文路径](https://leetcode.cn/problems/pseudo-palindromic-paths-in-a-binary-tree/)



#### （3）[1466. 重新规划路线](https://leetcode.cn/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)





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
