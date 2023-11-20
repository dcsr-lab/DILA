# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之FloodFill



### 1、FloodFill

#### （1）[1254. 统计封闭岛屿的数目](https://leetcode.cn/problems/number-of-closed-islands/) 

**问题**：

二维矩阵 `grid` 由 `0` （土地）和 `1` （水）组成。岛是由最大的4个方向连通的 `0` 组成的群，封闭岛是一个 `完全` 由1包围（左、上、右、下）的岛。

请返回 *封闭岛屿* 的数目。

例子：

<img src="https://assets.leetcode.com/uploads/2019/10/31/sample_3_1610.png" alt="img" style="zoom:60%;" />

```
输入：grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]
输出：2
解释：
灰色区域的岛屿是封闭岛屿，因为这座岛屿完全被水域包围（即被 1 区域包围）。
```

**数据范围**：

- `1 <= grid.length, grid[0].length <= 100`
- `0 <= grid[i][j] <=1`

**思路**：FloodFill模板题

注意：封闭岛要求四周都被水包围。超出边界时，此岛不是封闭岛

```java
class Solution {

    boolean[][] st = new boolean[110][110];

    public boolean floodfill(int[][] grid, int start, int end) {
        int n = grid.length, m = grid[0].length;

        Pair<Integer, Integer>[] q = new Pair[n * m];
        int hh = 0, tt = 0;
        q[0] = new Pair<Integer, Integer>(start, end);
        st[start][end] = true;

        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
        boolean res = true;
        while(hh <= tt) {
            Pair<Integer, Integer> t = q[hh ++];

            for(int i = 0; i < dx.length; i ++ ) {
                int tx = t.getKey() + dx[i], ty = t.getValue() + dy[i];

                if(tx < 0 || tx >= n || ty < 0 || ty >= m) { // 封闭岛要求四周都被水包围。超出边界时，此岛不是封闭岛
                    res = false;
                    continue;
                };
                if(grid[tx][ty] == 1) continue;
                if(st[tx][ty]) continue;

                st[tx][ty] = true;
                q[++ tt] = new Pair<Integer, Integer>(tx, ty);

            }
        }

        return res;
    }

    public int closedIsland(int[][] grid) {
        int res = 0, n = grid.length, m = grid[0].length;
        for(int i = 0; i < n; i ++)
            for(int j = 0; j < m; j ++ )
                if(grid[i][j] != 1 && !st[i][j] && floodfill(grid, i, j)) res ++;

        return res;
    }
}
```

#### （2）[面试题 16.19. 水域大小](https://leetcode.cn/problems/pond-sizes-lcci/) 

**问题**：

你有一个用于表示一片土地的整数矩阵`land`，该矩阵中每个点的值代表对应地点的海拔高度。若值为0则表示水域。

由垂直、水平或对角连接的水域为池塘。**池塘的大小是指相连接的水域的个数**。

编写一个方法来**计算矩阵中所有池塘的大小，返回值需要从小到大排序**。

例子：

```
输入：
[
  [0,2,1,0],
  [0,1,0,1],
  [1,1,0,1],
  [0,1,0,1]
]
输出： [1,2,4]
```

**数据范围**：

- `0 < len(land) <= 1000`
- `0 < len(land[i]) <= 1000`

**思路**：FloodFill 模板题

```java
class Solution {

    boolean[][] st = new boolean[1000][1000];

    public int floodfill(int[][] g, int s, int e) {
        int n = g.length, m = g[0].length;

        Pair<Integer, Integer>[] q = new Pair[n * m];
        int hh = 0, tt = 0;
        q[0] = new Pair<Integer, Integer>(s, e);
        st[s][e] = true;
    
        int[] dx = new int[]{0, 1, 1, 1, 0, -1, -1, -1}, dy = new int[]{1, 1, 0, -1, -1, -1, 0, 1};
        while(hh <= tt) {
            var t = q[hh ++ ];

            for(int i = 0; i < dx.length; i ++ ) {
                int tx = t.getKey() + dx[i], ty = t.getValue() + dy[i];
                if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue;
                if(g[tx][ty] != 0) continue;
                if(st[tx][ty]) continue;

                st[tx][ty] = true;
                q[++ tt] = new Pair<Integer, Integer>(tx, ty);
            }
        }
        return tt + 1;
    }

    public int[] pondSizes(int[][] land) {
        int n = land.length, m = land[0].length;

        List<Integer> arr = new ArrayList<>();
        for(int i = 0; i < n; i ++ )
            for(int j = 0; j < m; j ++)
                if(land[i][j] == 0 && !st[i][j] )
                    arr.add(floodfill(land, i, j));

        Collections.sort(arr);
        int[] res = new int[arr.size()];
        for(int i = 0; i < arr.size(); i ++ )
            res[i] = arr.get(i);
        return res;
    }
}
```

#### （3）200. 岛屿数量

**问题**：

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

例子：

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**数据范围**：

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` 的值为 `'0'` 或 `'1'`

**思路**：**求连通块的数量**，**FloodFill**

```python
from collections import deque

class Solution:

    def __init__(self):
        self.st = [] # 状态数组
        self.g = [] # 存储地图
        self.n, self.m = 0, 0 # 地图的行数和列数

    def bfs(self, x, y): # FloodFill
        q = deque([(x, y)]) # 用列表初始化一个队列, 队头元素入队列
        self.st[x][y] = True
       
        dx, dy = [0, 1, 0, -1], [1, 0, -1, 0]
        while q:
            t = q.popleft() # 队头元素出队列

            for i in range(4): # 遍历上下左右四个方向
                tx, ty = t[0] + dx[i], t[1] + dy[i]
                if tx < 0 or tx >= self.m or ty < 0 or ty >= self.n:
                    continue
                if self.st[tx][ty] or self.g[tx][ty] == "0":
                    continue
                q.append((tx, ty))               
                self.st[tx][ty] = True
        
    def numIslands(self, grid: List[List[str]]) -> int:
        self.m, self.n = len(grid), len(grid[0])
        self.g = grid
        self.st = [[False for j in range(self.n) ] for i in range(self.m)] # 初始化二维数组

        res = 0 # 记录岛屿的数量
        for i in range(self.m):
            for j in range(self.n):
                if (not self.st[i][j]) and self.g[i][j] == "1":
                    self.bfs(i, j)
                    res += 1 
        return res
```

#### （4）694. 不同岛屿的数量

**问题**：

给定一个非空 01 二维数组表示的网格，一个岛屿由四连通（上、下、左、右四个方向）的 `1` 组成，你可以认为网格的四周被海水包围。

请你计算这个网格中共有**多少个形状不同的岛屿**。两个岛屿被认为是相同的，当且仅当一个岛屿可以通过**平移变换**（不可以旋转、翻转）和另一个岛屿重合。

**示例**：

```python
11011
10000
00001
11011
```

给定上图，返回结果 `3` 。

**思路**：**FloodFill**和**HashSet**

- 每次`FloodFill`搜索一个岛屿时，将每个坐标点相对于**初始坐标点(x, y)**的**相对坐标**记录下来
- 如果两个岛屿的相对作坐标相同，则这两个岛屿是形状相同的岛屿
- 使用 `HashSet` 去重，最后`set`集合的大小就是不同形状的岛屿数量

```python
from collections import deque

class Solution:

    def __init__(self):
        self.st = [] # 判重数组
        self.g = [] # 地图
        self.m, self.n = 0, 0
        self.land = set()

    def bfs(self, x, y): # FloodFill
        q = deque([(x, y)]) # 队列
        self.st[x][y] = True

        res = [] # 存储岛屿所有坐标点与(x,y)的相对位置
        dx, dy = [0, 1, 0, -1], [1, 0, -1, 0] # 偏移量
        while q:
            t = q.popleft() # 队头元素出队列
            res.append((t[0] - x, t[1] - y))

            for i in range(4): # 遍历上下左右四个方向
                tx, ty = t[0] + dx[i], t[1] + dy[i]
                if tx < 0 or tx >= self.m or ty < 0 or ty >= self.n:
                    continue
                if self.st[tx][ty] or self.g[tx][ty] == 0:
                    continue
                q.append((tx, ty))
                self.st[tx][ty] = True

        self.land.add(tuple(res)) # 列表是可变序列不能用作哈希, 将列表转化为元组

    def numDistinctIslands(self, grid: List[List[int]]) -> int:
        self.m, self.n = len(grid), len(grid[0])
        self.g = grid
        self.st = [[False for j in range(self.n)] for i in range(self.m)]

        for i in range(self.m):
            for j in range(self.n):
                if (not self.st[i][j]) and self.g[i][j]:
                    self.bfs(i, j)

        return len(self.land)
```

#### （5）302. 包含全部黑色像素的最小矩形

**问题**：

图片在计算机处理中往往是使用二维矩阵来表示的。

给你一个大小为 `m x n` 的二进制矩阵 `image` 表示一张黑白图片，`0` 代表白色像素，`1` 代表黑色像素。

黑色像素相互连接，也就是说，图片中只会有一片连在一块儿的黑色像素。像素点是水平或竖直方向连接的。

给你两个整数 `x` 和 `y` 表示某一个黑色像素的位置，请你找出包含全部黑色像素的最小矩形（与坐标轴对齐），并返回该矩形的面积。

你必须设计并实现一个时间复杂度低于 `O(mn)` 的算法来解决此问题。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203032142399.jpeg" alt="img" width="240" />

**思路**：**FloodFill**

```python
from collections import deque

class Solution:

    def __init__(self):
        self.st = [] # 判重数组
        self.g = [] # 图
        self.m, self.n = 0, 0

    def bfs(self, x, y): # FloodFill, 返回包含全部黑色像素的最小矩形的面积
        q = deque([(x, y)]) # 初始化队列
        self.st[x][y] = True

        x1, x2, y1, y2 = 2e9, -2e9, 2e9, -2e9 # 最小矩形的最小x, 最大x, 最小y, 最大y
        dx, dy = [0, 1, 0, -1], [1, 0, -1, 0] # 偏移量
        while q:
            t = q.popleft()
            x1, x2, y1, y2 = min(x1, t[0]), max(x2, t[0]), min(y1,t[1]), max(y2, t[1])

            for i in range(4):
                tx, ty = t[0] + dx[i], t[1] + dy[i]
                if tx < 0 or tx >= self.m or ty < 0 or ty >= self.n:
                    continue
                if self.st[tx][ty] or self.g[tx][ty] == "0":
                    continue
                q.append((tx, ty))
                self.st[tx][ty] = True

        return (x2 - x1 + 1) * (y2 - y1 + 1) # 长X宽

    def minArea(self, image: List[List[str]], x: int, y: int) -> int:
        self.m, self.n = len(image), len(image[0])
        self.st = [[False for j in range(self.n)] for i in range(self.m)] # 初始化判重数组
        self.g = image

        return self.bfs(x, y)
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
