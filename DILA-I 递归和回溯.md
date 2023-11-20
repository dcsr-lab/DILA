# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之递归和回溯



### 1、递归和回溯

回朔法的重要思想在于： 通过枚举法，对所有可能性进行遍历。 但是枚举的顺序是 一条路走到黑，发现黑之后，退一步，再向前尝试没走过的路。直到所有路都试过。因此回朔法可以简单的理解为： 走不通就退一步的方枚举法就叫回朔法。而这里回退点也叫做回朔点。

#### （1）38. 外观数列

**问题**

给定一个正整数 `n` ，输出外观数列的第 `n` 项。

「外观数列」是一个整数序列，从数字 1 开始，序列中的每一项都是对前一项的描述。

你可以将其视作是由递归公式定义的数字字符串序列：

- `countAndSay(1) = "1"`
- `countAndSay(n)` 是对 `countAndSay(n-1)` 的描述，然后转换成另一个数字字符串。

前五项如下：

```java
1.     1
2.     11
3.     21
4.     1211
5.     111221
```

**思路**

```java
class Solution {

    public String dfs(int u) {
        if(u == 1) return "1"; // 递归结束

        String str = dfs(u - 1); // 求外观数列的第 u - 1 项
        char[] chs = str.toCharArray();

        String res = "";
        for(int i = 0; i < chs.length; ) { // 根据外观数列的第 u - 1 项 求第 u 项
            int j = i + 1;
            while(j < chs.length && chs[j] == chs[i]) j ++ ; // 找到下一个不同数的位置

            res = res + (j - i) + chs[i]; // j - i 是当前相同数的个数
            i = j;
        }
        return res;
    }

    public String countAndSay(int n) {
        return dfs(n);
    }
}
```

#### （2）51. N 皇后

**问题**

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208191514742.jpeg" alt="img" width="400px" />

**思路** 

皇后们的约束条件：**不能同行**，**不能同列**，**不能同斜线**

搜索皇后的位置，可以**抽象为一棵树**（棋盘搜索）：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208191512589.png" alt="image-20220819151218407" width="600px" />

矩阵的高就是这颗树的高度，矩阵的宽就是树形结构中每一个节点的宽度

用皇后们的约束条件，来回溯搜索这颗树，**只要搜索到了树的叶子节点，说明就找到了皇后们的合理位置了**

**求出所有方案的表示**

```java
class Solution {
    List<List<String>> solutions = new ArrayList<>();

    // 检测在(x, y)摆放一个皇后是否满足要求（按照从上到下, 从左到右的方式搜索）
    public boolean check(int x, int y, char[][] g) {
        for(int r = x - 1; r >= 0; r --) // (x, y) 所在的列, 且在(x,y)之上的位置
            if(g[r][y] == 'Q') return false;
        for(int i = x, j = y; i >= 0 && j >= 0; i -- , j --) // (x, y)所在左对角线的左上部分
            if(g[i][j] == 'Q') return false;
        for(int i = x, j = y; i >= 0 && j < g[0].length; i --, j ++) // (x, y)所在右对角线的右上部分
            if(g[i][j] == 'Q') return false;

        return true;
    }

    public void dfs(int r, int n, char[][] g) {
        if(r == n) { // 搜索到一种摆放皇后的方案
            List<String> result = new ArrayList<>();
            for(int i = 0; i < n; i ++ )
                result.add(new String(g[i]));
            solutions.add(result);
        }

        for(int c = 0; c < n; c ++ ) // 遍历所有可以摆放的列[(r,0),(r,1), ...], 对应树结构中的孩子结点
            if(check(r, c, g)) { // 检测在(r, c)的位置上摆放皇后是否满足要求
                g[r][c] = 'Q';
                dfs(r + 1, n, g); // 搜索如何在下一行摆放一个皇后
                g[r][c] = '.'; // 恢复现场
            }
    }

    public List<List<String>> solveNQueens(int n) {
        char[][] g = new char[n][n]; // 棋盘
        for(int i = 0; i < n; i ++) Arrays.fill(g[i], '.'); // 初始化的棋盘上没有任意一个皇后

        dfs(0, n, g);
        return solutions;
    }
}
```

**求出方案数**

```java
class Solution {

    // 检测在(x, y)摆放一个皇后是否满足要求（按照从上到下, 从左到右的方式搜索）
    public boolean check(int x, int y, char[][] g) {
        for(int r = x - 1; r >= 0; r --) // (x, y) 所在的列, 且在(x,y)之上的位置
            if(g[r][y] == 'Q') return false;
        for(int i = x, j = y; i >= 0 && j >= 0; i -- , j --) // (x, y)所在左对角线的左上部分
            if(g[i][j] == 'Q') return false;
        for(int i = x, j = y; i >= 0 && j < g[0].length; i --, j ++) // (x, y)所在右对角线的右上部分
            if(g[i][j] == 'Q') return false;

        return true;
    }

    public int dfs(int r, int n, char[][] g) {
        // 搜索到一种摆放皇后的方案
        if(r == n) return 1;

        int res = 0;
        for(int c = 0; c < n; c ++ ) // 遍历所有可以摆放的列[(r,0),(r,1), ...], 对应树结构中的孩子结点
            if(check(r, c, g)) { // 检测在(r, c)的位置上摆放皇后是否满足要求
                g[r][c] = 'Q';
                res += dfs(r + 1, n, g); // 搜索如何在下一行摆放一个皇后
                g[r][c] = '.'; // 恢复现场
            }
        return res;
    }

    public int totalNQueens(int n) {
        char[][] g = new char[n][n]; // 棋盘
        for(int i = 0; i < n; i ++) Arrays.fill(g[i], '.'); // 初始化的棋盘上没有任意一个皇后

        return dfs(0, n, g);
    }
}
```

#### （3）37. 解数独 

**问题**

编写一个程序，通过填充空格来解决数独问题。

数独的解法需 **遵循如下规则**：

1. 数字 `1-9` 在每一行只能出现一次。
2. 数字 `1-9` 在每一列只能出现一次。
3. 数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次。（请参考示例图）

数独部分空格内已填入了数字，空白格用 `'.'` 表示。

<div align="center">
    <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208191651487.png" alt="img" width="200px" />
    <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208191653975.png" alt="img" width="200px" />
</div>




**思路**

解数独的约束条件：**数字 `1-9` 在每一行只能出现一次**，**数字 `1-9` 在每一列只能出现一次**，**数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次**

搜索每一个位置上所填的数时，可以**抽象为一棵树**（棋盘搜索）：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208192034545.png" alt="37.解数独" width="800px" />

待填入数的个数就是这颗树的高度，可以填入数的个数(`1~9`)是树形结构中每一个节点的宽度

用解数独的约束条件，来回溯搜索这颗树，**只要搜索到了树的叶子节点，说明就找到了一种合适的方案**

```java
class PII {
    int x, y;
    PII(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Solution {
    LinkedList<PII> coords = new LinkedList<>(); // 棋盘中所有待填充的位置的集合
    int n = 0;

    public boolean check(int x, int y, int v, char[][] g) { // 
        for(int i = 0; i < n; i ++ ) { // (x, y) 所在的行和列都没有与v相同的字符
            if(g[x][i] != '.' && g[x][i] - '0' == v) return false;
            if(g[i][y] != '.' && g[i][y] - '0' == v) return false;
        }

        PII st = new PII(x / 3 * 3, y / 3 * 3); // (x, y) 所在小宫格左上角坐标
        PII ed = new PII(st.x + 2, st.y + 2); // (x, y) 所在小宫格右小角坐标

        // 判断(x, y)所在小宫格内是否有与v相同的字符
        for(int i = st.x; i <= ed.x ; i ++)
            for(int j = st.y; j <= ed.y; j ++)
                if(g[i][j] - '0' == v) return false;

        return true;
    }

    public boolean dfs(int x, int y, char[][] g) {

        for(int i = 1; i <= n; i ++ ) // 枚举(x, y)所在的位置可以填充的数字(1,2,3,...,9)
            if(check(x, y, i, g)) {
                g[x][y] = (char)('0' + i);
                if(coords.isEmpty()) return true; // 所有的位置都按要求填充好, 即找到一个解
                PII pos = coords.removeFirst();
        
                if(dfs(pos.x, pos.y, g)) return true;
                
                // 恢复现场
                coords.addFirst(pos);
                g[x][y] = '.';
            }

        return false;
    }

    public void solveSudoku(char[][] board) {
        n = board.length;
        for(int i = 0; i < n; i ++ )
            for(int j = 0; j < n; j ++) 
                if(board[i][j] == '.') coords.addLast(new PII(i ,j));

        PII pos = coords.removeFirst();
        dfs(pos.x, pos.y, board);
    }
}
```

#### （4）[240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/) 

**问题**

编写一个高效的算法来搜索 `m x n` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

例子

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/25/searchgrid.jpg" alt="img" style="zoom:50%;" />



```
输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 20
输出：false
```

**数据范围**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= n, m <= 300`
- `-10^9 <= matrix[i][j] <= 10^9`
- 每行的所有元素从左到右升序排列
- 每列的所有元素从上到下升序排列
- `-10^9 <= target <= 10^9`

**思路**

$DFS$ 搜索问题。

**确定枚举方向**：$DFS$ 每次枚举的方向是 $(x+0,y+1)$, $(x+1,y+0)$, $(x+0,y-1)$，即右，下，左。

**确定枚举顺序**：依据矩阵的每行的元素从左到右升序排列，每列的元素从上到下升序排列的特点。**$DFS$枚举顺序也要按照从右下左的顺序枚举。**

```java
class Solution {

    int[] dx = new int[]{0, 1, 0}, dy = new int[]{1, 0, -1}; // 右/下/左
    int n, m;
    boolean[][] st; // 判重矩阵

    public boolean dfs(int[][] matrix, int x, int y, int target) {
        
        for(int i = 0; i <  dx.length; i ++) {
            int tx = x + dx[i], ty = y + dy[i];
            if(tx < 0 || tx >= n || ty < 0 || ty >= m) continue; // 越界
            if(st[tx][ty]) continue; // 已经走过了
            if(matrix[tx][ty] > target) continue; // 大于目标值
            if(matrix[tx][ty] == target) return true; // 找到了目标

            st[tx][ty] = true;
            if(dfs(matrix, tx, ty, target)) return true;
        }
        return false;
    }

    public boolean searchMatrix(int[][] matrix, int target) {
        n = matrix.length;
        m = matrix[0].length;
        st = new boolean[n][m];
        if(matrix[0][0] == target) return true; // 特判
        
        st[0][0] = true;
        return dfs(matrix, 0, 0, target);
    }
}
```

#### （5）[39. 组合总和](https://leetcode.cn/problems/combination-sum/)

**问题**

给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有 **不同组合** ，并以列表形式返回。你可以按 **任意顺序** 返回这些组合。

`candidates` 中的 **同一个** 数字可以 **无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

例子

```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```

**数据范围**

- `1 <= candidates.length <= 30`
- `2 <= candidates[i] <= 40`
- `candidates` 的所有元素 **互不相同**
- `1 <= target <= 40`

**思路**

```java
class Solution {

    List<List<Integer>> res = new ArrayList<>();
    int[] way = new int[45];

    public void dfs(int u, int start, int s, int[] candidates, int target) {
        
        if(s == target) { // 找到一种解决方案
            List<Integer> arr = new ArrayList<>();
            for(int i = 1; i < u; i ++) 
                arr.add(way[i]);
            res.add(arr);
            return ;
        }

        for(int i = start; i < candidates.length; i ++) {
            if(s + candidates[i] > target) break; // 特判
            way[u] = candidates[i];
            dfs(u + 1, i, s + candidates[i], candidates, target);
            way[u] = 0;
        }
    }

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(1, 0, 0, candidates, target);
        return res;
    }
}
```

#### （6）[40. 组合总和 II](https://leetcode.cn/problems/combination-sum-ii/)

**问题**

给定一个候选人编号的集合 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的**每个数字在每个组合中只能使用 一次** 。

**注意：**解集不能包含重复的组合。 

例子

```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**数据范围**

- `1 <= candidates.length <= 100`
- `1 <= candidates[i] <= 50`
- `1 <= target <= 30`

**思路**

```java
class Solution {

    List<List<Integer>> res = new ArrayList<>();
    int[] way = new int[35];
    
    public void dfs(int u, int start, int s, int[] candidates, int target) {
        
        if(s == target) {
            List<Integer> arr = new ArrayList<>();
            for(int i = 1; i < u; i ++) 
                arr.add(way[i]);
            res.add(arr);
            return ;
        }
        boolean[] st = new boolean[55]; // 去重
        for(int i = start; i < candidates.length; i ++) {
            if(st[candidates[i]]) continue; 

            if(s + candidates[i] > target) break;
            way[u] = candidates[i];
            dfs(u + 1, i + 1, s + candidates[i], candidates, target);
            way[u] = 0;
            
            st[candidates[i]] = true;
        }
    }

    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(1, 0, 0, candidates, target);
        return res;
    }
}
```



#### （7）[216. 组合总和 III](https://leetcode.cn/problems/combination-sum-iii/)

**问题**

找出所有相加之和为 `n` 的 `k` 个数的组合，且满足下列条件：

- 只使用数字1到9
- 每个数字 **最多使用一次** 

返回 *所有可能的有效组合的列表* 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。

例子

```
输入: k = 3, n = 9
输出: [[1,2,6], [1,3,5], [2,3,4]]
解释:
1 + 2 + 6 = 9
1 + 3 + 5 = 9
2 + 3 + 4 = 9
没有其他符合的组合了。
```

**数据范围**

- `2 <= k <= 9`
- `1 <= n <= 60`

**思路**

```java
class Solution {

    List<List<Integer>> res = new ArrayList<>();
    int[] ways = new int[10];

    public void dfs(int u, int start, int s, int k, int n) {
        if(u - 1 + 9 - start + 1 < k) return ;
        if(u == k + 1 && s == n) {
            ArrayList<Integer> arr = new ArrayList<>();
            for(int i = 1; i < u; i ++)
                arr.add(ways[i]);
            res.add(arr);
        }

        for(int i = start; i < 10; i ++) {
            if(s + i > n) break; // 特判
            ways[u] = i;
            dfs(u + 1, i + 1, s + i, k, n);
            ways[u] = 0;
        }
    }

    public List<List<Integer>> combinationSum3(int k, int n) {
        dfs(1, 1, 0, k, n);
        return res;
    }
}
```

#### （8）[77. 组合](https://leetcode.cn/problems/combinations/)

**问题**

给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

你可以按 **任何顺序** 返回答案。

例子

```
输入：n = 4, k = 2
输出：
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**数据范围**

- `1 <= n <= 20`
- `1 <= k <= n`

**思路**

```java
class Solution {

    List<List<Integer>> res = new ArrayList<>();
    int[] way = new int[26];

    // 不重复不遗漏
    public void dfs(int u, int start, int n, int k) {
        if(u - 1 + n - start + 1 < k) return ; // 可选的数不足 k 个
        if(u == k + 1) { // 找到一种组合方案
            List<Integer> arr = new ArrayList<>();
            for(int i = 1; i <= k; i ++) 
                arr.add(way[i]);
            res.add(arr);
            return ;
        }

        for(int i = start; i <= n; i ++ ) {
            way[u] = i;
            dfs(u + 1, i + 1, n, k);
            way[u] = 0;
        }
    }

    public List<List<Integer>> combine(int n, int k) {
        dfs(1, 1, n, k);
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
