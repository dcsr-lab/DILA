# 剑指 Offer(专项突破)算法笔记

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>




> 引言
>
> 此文章是记录**剑指Offer(专项突破版)**的刷题笔记，包括**图、树、数组、链表、前缀和、二分、动态规划、滑动窗口、哈希表、双指针、枚举、位运算、优先队列、并查集、模拟、字典树、单调栈、栈、单调队列、队列、拓扑排序**等常见的数据结构和算法
>
> :man:：`Laplace`	:timer_clock: ：`2021-11-07`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)   :information_source:: [`LeetCode`](https://leetcode.cn/)  



<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202270958660.png" alt="image.png" width="900px" />

### 1、图

##### （1）剑指 Offer II 105. 岛屿的最大面积

**BFS 算法**求岛屿的面积

```java
class Pair{ // 坐标位置
    int x, y;
    Pair(int x, int y){
        this.x = x;
        this.y = y;
    }
}

class Solution {

    int[][] g; // 地图
    boolean[][] st; // 判重数组
    int m, n;
    int res = 0; // 最大岛屿的面积

    public int bfs(int x, int y){ // 求以(x,y)为起点的岛屿的面试
        Queue<Pair> q = new ArrayDeque<>(); // 队列
        q.offer(new Pair(x, y));
        st[x][y] = true;

        int[] dx = {1, 0, -1, 0}, dy = {0, -1, 0, 1}; // 偏移量
        int cnt = 0;
        while(!q.isEmpty()) {
            Pair t = q.poll();
            cnt ++;

            for(int i = 0; i < 4; i ++){
                int tx = t.x + dx[i], ty = t.y + dy[i];
                if(tx < 0 || tx >= m || ty < 0 || ty >= n) continue;
                if(g[tx][ty] != 1) continue;
                if(st[tx][ty]) continue;

                q.offer(new Pair(tx, ty));
                st[tx][ty] = true;
            }
        }
        return cnt;
    }

    public int maxAreaOfIsland(int[][] grid) {
        g = grid;
        m = grid.length; n = grid[0].length;
        st = new boolean[m][n];

        for(int i = 0; i < m; i ++) // 求所有岛屿的最大面积
            for(int j = 0; j < n; j ++)
                if(g[i][j] == 1 && !st[i][j])
                    res = Math.max(res, bfs(i,j));
        return res;
    }
}
```

##### （2）剑指 Offer II 106. 二分图

**染色法判定二分图**

```java
class Solution {
    int[] color; // color[i]表示编号为i的结点染的颜色, 0表示未染色, 1和2表示已染色,i的取值[0,n-1]
    int n;
    int[][] g; // 邻接表, g[u] 是一个节点数组，由节点u的邻接节点组成
    
    public boolean dfs(int u, int c){ //从u结点开始搜连通块,将连通块中的所有结点染色,相邻结点染成相反颜色
        color[u] = c;

        for(int i = 0; i < g[u].length; i ++) { // 遍历u的邻接点
            int j = g[u][i];
            if(color[j] == 0){ // 给j结点染色
                if(!dfs(j, 3 - c)) return false;
            } else if(color[j] == c) return false; // 相邻的结点染得颜色相同, 则不是二分图
        }
        return true;
    }

    public boolean isBipartite(int[][] graph) {
        n = graph.length;
        final int N = n + 10; 
        g = graph;
        color = new int[N];

        boolean res = true;
        for(int i = 0; i < n; i ++){ // 遍历所有结点
            if(color[i] == 0){ // i 没有染色, 从 i 开始搜索连通块, 并染色
                if(!dfs(i, 1)){
                    res = false;
                    break;
                }
            }
        }
        return res;
    }
}
```

##### （3）剑指 Offer II 110. 所有路径

**问题**：给定一个有`n`个节点的有向无环图，用二维数组`graph`表示，请找到**所有从`0`到`n-1`的路径**并输出（不要求按顺序）

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202072248142.jpeg" alt="img" width="260px" />

**思路**：从`0`开始**DFS**搜索所有可以到达`n-1`的路径并存储

```java
class Solution {

    boolean[][] g; // 邻接矩阵 g[i][j]为true表示i->j存在一条有向边
    int n; // 结点个数   
    List<Integer> path = new ArrayList<>();
    List<List<Integer>> res = new ArrayList<>(); // 存储所有的从0~n-1的路径

    public void dfs(int u){ // dfs 搜索所有的路径并存储
        if(u == n - 1){ // 找到一条路径
            res.add(new ArrayList<>(path));
            return ;
        }
        for(int i = 0; i < n; i ++){ // 遍历u的所有邻接点
            if(g[u][i]){
                path.add(i);
                dfs(i);
                path.remove(path.size()-1); // 恢复现场
            } 
        }
    } 

    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        if(graph.length == 0) return res;
        n = graph.length;

        g = new boolean[n][n];
        for(int i = 0; i < n; i ++ ) // 构建邻接矩阵
            for(int j = 0; j < graph[i].length; j ++) // 无自环和重边
                g[i][graph[i][j]] = true;

        path.add(0);
        dfs(0);
        return res;
    }
}
```

### 2、树

##### （1）剑指 Offer II 044. 二叉树每层的最大值

**问题**：给定一棵二叉树的根节点 `root` ，请找出该二叉树中每一层的最大值

**思路**：二叉树的**层序遍历**遍历二叉树的每一层

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
    final int N = 10010;

    public List<Integer> bfs(TreeNode root){
        TreeNode[] q = new TreeNode[N];
        int hh = 0, tt = 0;
        q[0] = root; 

        List<Integer> res = new ArrayList<>(); // 存储二叉树智能每一层的最大值
        while(hh <= tt){
            int head = hh, tail = tt;
            
            int maxv = 0x80000000; // 表示每一层的最大值 0x80000000 表示 -2147483648
            while(hh <= tail){ // 按层进行遍历
                TreeNode t = q[hh ++];
                maxv = Math.max(maxv, t.val);

                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
            res.add(maxv);
        }
        return res;
    }

    public List<Integer> largestValues(TreeNode root) {
        if(root == null) return new ArrayList<>();
        return bfs(root);
    }
}
```

##### （2）剑指 Offer II 052. 展平二叉搜索树

**问题**：给你一棵二叉搜索树，请 **按中序遍历** 将其重新排列为一棵递增顺序搜索树(即**单链表)**，使树中最左边的节点成为树的根节点，并且每个节点没有左子节点，只有一个右子节点

**思路**：按照**中序遍历**的顺序将其串成一个单链表

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

    TreeNode cur, pre, h;

    public void dfs(TreeNode u){
        if(u == null) return ;
        dfs(u.left);
        cur = u; // 记录当前结点
        if(h == null) h = cur; // 记录单链表的头结点
        if(pre != null) pre.right = cur;// 按照中序遍历的顺序将所有节点串成单链表
        pre = cur; // 更新前继结点
        pre.left = null; // 每个节点没有左子节点
        dfs(u.right);
    }

    public TreeNode increasingBST(TreeNode root) {
        dfs(root);
        return h;
    }
}
```

##### （3）剑指 Offer II 056. 二叉搜索树中两个节点之和 

**问题**：给定一个二叉搜索树的根节点 `root` 和一个整数 `k` , 请判断该二叉搜索树中**是否存在两个节点它们的值之和等于 `k`** 。假设二叉搜索树中节点的值均唯一 

**思路**：**中序遍历和双指针**

- 该问题本质上是**判断一个有序数组是否存在两个数之和等于指定的数**
- 因为是搜索树，所以中序遍历的结果是排序好的集合 
- 之后按照排序好的数组的双指针的模式来解题 
- 用**两个指针**分别指向集合的头和尾，求和 
- 若是大于目标值，则尾部指针前移  
- 若是小于目标值，则头部指针后移 
- 循环直到找到目标值，若是两个指针碰撞也没有找到目标值，则返回`false` 

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
    final int N = 10010;
    int[] a = new int[N]; // 二叉搜索树的中序序列
    int idx; // 二叉搜索树的结点个数

    public void dfs(TreeNode u){ 
        if(u == null) return ; 
        dfs(u.left); 
        a[idx ++] = u.val; 
        dfs(u.right); 
    }

    public boolean findTarget(TreeNode root, int k) {
        dfs(root);
        for(int i = 0, j = idx - 1; i < j;){ // 设置两个指针, 一个指向数组头,一个指向数组尾
            if(a[i] + a[j] == k) return true;
            if(a[i] + a[j] < k) i ++ ; // a[i] + a[j] 之和小于k, 试图增大a[i]
            else j --; // a[i] + a[j] 之和大于k, 试图减小a[j]
        }
        return false; 
    }
}
```

##### （4）剑指 Offer II 045. 二叉树最底层最左边的值

**问题**：给定一个二叉树的根节点 `root`，请找出该二叉树的**最底层最左边节点**的值。假设二叉树中至少有一个节点。

**思路**：二叉树的**层序遍历**求最后一层第一个结点

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

    final int N = 10010;

    public int bfs(TreeNode root){ // 二叉树的层序遍历
        TreeNode[] q = new TreeNode[N];
        int hh = 0, tt = 0;
        q[0] = root;

        int res = 0; // 记录最后一层的第一个元素, 即为最底层最左边结点
        while(hh <= tt) {
            int head = hh, tail = tt;
            if(hh <= tail) res = q[hh].val; // 记录每一层的第一个元素
            while(hh <= tail){
                TreeNode t = q[hh ++];
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
        }
        return res;
    }

    public int findBottomLeftValue(TreeNode root) {
        return bfs(root);
    }
}
```

##### （5）剑指 Offer II 046. 二叉树的右侧视图

**问题**：给定一个二叉树的 根节点 `root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

**思路**：二叉树的**层序遍历**，记录每一层的最后一个结点

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
    final int N = 110;

    public List<Integer> bfs(TreeNode root){ // 二叉树的层序遍历
        TreeNode[] q = new TreeNode[N];
        int hh = 0, tt = 0;
        q[0] = root;

        List<Integer> res = new ArrayList<>(); // 存储二叉树的最右侧的所有结点
        while(hh <= tt){
            int head = hh, tail = tt;
            if(hh <= tail) res.add(q[tail].val); // 二叉树的最右侧的所有结点即层序遍历每一层右侧的结点
            while(hh <= tail){
                TreeNode t = q[hh ++];
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;    
            }
        }
        return res;
    }

    public List<Integer> rightSideView(TreeNode root) {
        if(root == null) return new ArrayList<Integer>();
        return bfs(root);
    }
}
```

##### （6）剑指 Offer II 053. 二叉搜索树中的中序后继

**问题**：给定一棵二叉搜索树和其中的一个节点 `p` ，找到该节点在树中的中序后继。如果节点没有中序后继，请返回 `null` 。节点 `p` 的后继是值比 `p.val` 大的节点中键值最小的节点，即按中序遍历的顺序节点 `p` 的下一个节点。

**解法一**：**中序遍历**记录**前继结点**和**当前结点**。如果前继结点等于`p`，那么当前结点就是我们要找到中序后继

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
class Solution {

    TreeNode pre, cur, res; // 前继结点、当前结点和最终结果

    public void dfs(TreeNode u, TreeNode p){ // 中序遍历
        if(u == null) return ;

        dfs(u.left, p);
        cur = u;
        if(pre == p) res = cur; // 如果前继结点是p, 当前结点就是p的后继
        pre = cur;
        dfs(u.right, p);
    }

    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        dfs(root, p);
        return res;
    }
}
```

**解法二**：**(最快的解法)**

二叉搜索树的特性：左孩子 < 根 < 右孩子

-   如果`root.val <= p.val`答案一定在`root`的右子树；否则，答案一定在`root`的左子树或者就是`root`

`root=2, p = 1`

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202052148994.png" alt="img" width="120px" />

`root=5, p = 6`

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202052150982.png" alt="img" width="240px" />

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
class Solution {
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        if (root == null) return null;
        if (root.val <= p.val) // 如果 root.val <= p.val 答案一定在root的右子树
            return inorderSuccessor(root.right, p);
        TreeNode left = inorderSuccessor(root.left, p); // 否则，答案一定在 root 的左子树 或者 就是root
        return left == null ? root : left;
    }
}
```

##### （7）剑指 Offer II 054. 所有大于等于节点的值之和

**问题**：

给定一个二叉搜索树，请将它的每个节点的值替换成树中大于或者等于该节点值的所有节点值之和

**解法一**：**二叉树中序遍历和前缀和**

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
    final int N = 10010;
    TreeNode[] inorder = new TreeNode[N]; // 二叉搜索树中序序列为有序序列, 下标从1开始存储
    int[] a = new int[N], s = new int[N]; // 原数组, 前缀和数组, 两个数组下标都从1开始存储
    int idx = 0; // 最后表示结点个数

    public void dfs(TreeNode u){ // 二叉树的中序遍历
        if(u == null) return ;
        dfs(u.left);
        inorder[++ idx] = u;
        dfs(u.right);
    }

    public TreeNode convertBST(TreeNode root) {

        dfs(root);

        for(int i = 1; i <= idx; i ++){
            a[i] = inorder[i].val;
            s[i] = s[i-1] + a[i]; // 求前缀和
        }

        for(int i = 1; i <= idx; i ++) 
            // 它的每个节点的值替换成树中大于或者等于该节点值的所有节点值之和=>求区间和[i,idx]
            inorder[i].val = s[idx] - s[i-1];
        return root;
    }
}
```

**解法二**：逆向中序遍历，累加结点值之和，并更新当前结点的值

```java
class Solution {
    TreeNode pre, cur; // 前继结点和当前结点

    public void dfs(TreeNode u){ // 中序遍历, 采用右根左的顺序遍历
        if(u == null) return ;
        dfs(u.right);
        cur = u;
        if(pre != null) u.val += pre.val; // 更新当前结点值, 将当前结点的值累加上前继结点的值
        pre = cur;
        dfs(u.left);
    }

    public TreeNode convertBST(TreeNode root) {
        dfs(root);
        return root;
    }
}
```

##### （8）剑指 Offer II 049. 从根节点到叶节点的路径数字之和

**问题**：

给定一个二叉树的根节点 `root` ，树中每个节点都存放有一个 `0` 到 `9` 之间的数字。

每条从根节点到叶节点的路径都代表一个数字：例如，从根节点到叶节点的路径 `1 -> 2 -> 3` 表示数字 `123` 。

计算从根节点到叶节点生成的所有数字之和 。

**思路**：**先序遍历**求和

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

    int[] nums = new int[20]; // 存储由从根节点到叶节点的路径表示的数字, 左边是高位
    int idx;
    int res = 0;

    public int get(int[] a, int n){ // 求具体的数字
        int res = 0;
        for(int i = 0; i < n; i ++) res = res * 10 + a[i];
        return res;
    }

    public void dfs(TreeNode u){ // 递归搜索计算从根节点到叶节点的所有路径, 计算数字之和
        if(u == null) return ;
        nums[idx ++] = u.val;
        if(u.left == null && u.right == null) res += get(nums, idx); // 如果是叶子结点, 累加和
        dfs(u.left);
        dfs(u.right);
        idx --; // 恢复现场
    }

    public int sumNumbers(TreeNode root) {
        dfs(root); // 前序遍历
        return res;
    }
}
```

##### （9）剑指 Offer II 050. 向下的路径节点之和:writing_hand: 

**问题**：

给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 路径的数目。

**路径**不需要从根节点开始，也不需要在叶子节点结束，但是**路径方向必须是向下**的（**只能从父节点到子节点**）

例如：`targetSum=8`

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202072228482.jpeg" alt="img" width="370px" />

**思路**：**双递归**

- 第一个递归，遍历二叉树所有的结点
- 第二个递归，从某一个结点触发，遍历所有可能的路径，该路径的节点之和等于 `targetSum`

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

    int cnt = 0; // 二叉树里节点值之和等于 targetSum 的 路径 的数目

    // 从当前结点出发, 能否找到所有可能的路径, 该路径的节点值之和等于targetSum
    public void pathFind(TreeNode u, int targetSum, int sum){
        sum += u.val;
        if(targetSum == sum) cnt ++ ;
        if(u.left != null) pathFind(u.left, targetSum, sum);
        if(u.right != null) pathFind(u.right, targetSum, sum);
    }

    public void dfs(TreeNode u, int targetSum){ // 递归搜索二叉树的所有结点, 针对每一个结点搜索路径
        if(u == null) return ;
        pathFind(u, targetSum, 0); // 从当前结点出发, 能否找到一条路径, 该路径的节点值之和等于targetSum
        dfs(u.left, targetSum);
        dfs(u.right, targetSum);
    }

    public int pathSum(TreeNode root, int targetSum) {
        dfs(root, targetSum); // 前序遍历
        return cnt;
    }
}
```

##### （10）剑指 Offer II 051. 节点之和最大的路径:writing_hand: 

**问题**：

**路径**被定义为一条从树中**任意节点出发**，**沿父节点-子节点**连接，**达到任意节点**的序列。同一个节点在一条路径序列中至多出现一次 。该路径至少包含一个节点，且不一定经过根节点。

路径和是路径中各节点值的总和。

给定一个二叉树的根节点 `root` ，返回其**最大路径和**，即所有路径上节点值之和的最大值。

**思路**：

这道题与 [剑指 Offer 42. 连续子数组的最大 ](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)和有很大的相似之处。

以下图为模型分析，规定**只由子节点往父节点**延生的**路径**为 “**直径**”，由一条 “**直径**” 和另一条 “**直径**” 以及**它们的交点**组成的**路径**为 “**曲径**”，如下图中 b→a 和 c→a 就是 “直径”， b→a←c 就是 “曲径”。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202072240932.jpeg" alt="788032dadc057891f7739c30a0ab895.jpg" width="600px" />

用递归的思想考虑该问题，对于一个节点`a`，若左孩子节点`b`返回以节点`b`为终点的 “直径” 的最大路径和 `left`，右孩子节点`c`返回以节点`c`为终点的 “直径” 的最大路径和`right`。

因为若 `left` 和 `right` 为负数那么就应该舍弃，为了方便取 `right = Math.max(0, right)` ，`left = Math.max(0, left)`。则节点 `a` 返回的最大 “直径” 和的值就是 `a + Math.max(left, right)`。

因为题目中要求所有符合条件（所有的 “直径” 和 “曲径”）的路径的最大路径和，所有记录所有 “曲径” 的最大值就是结果值，`ret = Math.max(ret, left + right + root.val)`

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

    int res = Integer.MIN_VALUE; // 从所有[结点的曲径的最大路径和]中取最大值

    public int dfs(TreeNode u){ // 求以u为终点的直径的最大路径和
        if(u == null) return 0; // 如果u为空结点, 则以空结点为终点的直径的最大路径和为0

        int left = Math.max(0, dfs(u.left)); // 以u的左孩子为终点的直径的最大路径和, 如果为负数,则舍弃
        int right = Math.max(0, dfs(u.right)); // 以u的右孩子为终点的直径的最大路径和,如果为负数, 则舍弃
        res = Math.max(res, u.val + left + right); // u的曲径的最大路径和
        return u.val + Math.max(left, right); // 返回以u为终点的最大路径和
    }

    public int maxPathSum(TreeNode root) {
        dfs(root); // 后序遍历
        return res;
    }
}
```

##### （11）剑指 Offer II 047. 二叉树剪枝

**问题**：

给定一个二叉树 **根节点** `root` ，树的每个节点的值要么是 `0`，要么是 `1`。请剪除该二叉树中所有节点的值为 `0` 的子树。

节点 `node` 的子树为 `node` 本身，以及所有 `node` 的后代。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202270927224.png" alt="img" width="600px" />

**思路**：**后序遍历**

- 后序遍历并递归删除值为`0`的叶子结点
- 删除完左右子树后，判断根结点是否满足删除的条件

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
    public TreeNode pruneTree(TreeNode root) { // 后序遍历
        if(root.left == null && root.right == null && root.val == 0) // 叶子结点且值为0,则剪除
            return null;

        if(root.left != null) root.left = pruneTree(root.left); // 修剪左子树
        if(root.right != null) root.right = pruneTree(root.right); // 修剪右子树
        if(root.left == null && root.right == null && root.val == 0) return null; // 修剪根结点
        else return root;
    }
}
```

##### （12）剑指 Offer II 043. 往完全二叉树添加节点

**问题**：

**完全二叉树**是每一层（除最后一层外）都是完全填充（即，节点数达到最大，第 `n` 层有 `2^(n-1)` 个节点）的，并且所有的节点都尽可能地集中在左侧。

设计一个用完全二叉树初始化的数据结构 `CBTInserter`，它支持以下几种操作：

- `CBTInserter(TreeNode root)` 使用根节点为 `root` 的给定树初始化该数据结构；
- `CBTInserter.insert(int v)`  向树中插入一个新节点，节点类型为 `TreeNode`，值为 `v` 。使树保持完全二叉树的状态，并返回插入的新节点的父节点的值；
- `CBTInserter.get_root()` 将返回树的根节点。

**思路**：**完全二叉树的性质**和**层序遍历**

- 完全二叉树的层序遍历可以用一个下标从1开始的一维数组存储
- 向完全二叉树中添加节点，等价于向一维数组尾部添加一个数
- 更新新增节点的父结点信息

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
class CBTInserter {

    List<TreeNode> arr = new ArrayList<>();
    int hh = 1, tt = 1; // 数组模拟队列, hh是队头指针, tt是队尾指针

    public CBTInserter(TreeNode root) {
        arr.add(new TreeNode()); // 占位, 使得数组从下标1的位置开始存储
        arr.add(root); // 初始化队列

        while(hh <= tt){ // 层序遍历
            TreeNode t = arr.get(hh ++);
            if(t.left != null) {arr.add(t.left); ++ tt;}
            if(t.right != null) {arr.add(t.right); ++ tt;}
        }
    }
    
    public int insert(int v) {
        TreeNode u = new TreeNode(v);
        arr.add(u); ++ tt; // 完全二叉树中插入新结点
        TreeNode p = arr.get(tt / 2); // 求新结点的父结点
        if(p.left == null) p.left = u; // 更新父结点信息
        else p.right = u;
        return p.val;
    }
    
    public TreeNode get_root() {
        return arr.get(1);
    }
}
```



### 3、数组

##### （1）剑指 Offer II 074. 合并区间

**思路**：

- 将所有区间按左端点从小到大排序
- 维护一个区间，与后面一个区间进行比较，分三种情况（区间1与区间2不相交，区间1不包含区间2 或 区间2不包含区间1，区间1包含区间2）

```java
class Pair{
    int l, r;
    Pair(int l, int r){
        this.l = l;
        this.r = r;
    }
}

class Solution {
    public int[][] merge(int[][] intervals) {
        ArrayList<Pair> segs = new ArrayList<>();
        for(int i = 0; i < intervals.length; i ++)
            segs.add(new Pair(intervals[i][0], intervals[i][1]));
        Collections.sort(segs, (p1,p2)->{ // 将所有区间按左端点从小到大排序
            if(p1.l != p2.l) return p1.l - p2.l;
            else return p1.r - p2.r;
        });

        ArrayList<Pair> res = new ArrayList<>();
        int st = -0x3f3f3f3f, ed = -0x3f3f3f3f;
        for(Pair item : segs){
            if(ed < item.l){ // [st,ed]与当前区间互不重叠
                if(st != -0x3f3f3f3f) res.add(new Pair(st,ed));//[st,ed]与当前区间互不重叠,且不是初始空区间,则加入结果集
                st = item.l; ed = item.r; // 指向下一个区间
            } else ed = Math.max(ed, item.r); // [st,ed]与当前区间有交集(重叠或者包含当前区间), 取两个区间的并集
        } 
        if(st != -0x3f3f3f3f) res.add(new Pair(st, ed)); // 将最后一个区间加入结果集

        intervals = new int[res.size()][2];
        for(int i = 0; i < res.size(); i ++){
            intervals[i][0] = res.get(i).l;
            intervals[i][1] = res.get(i).r;      
        }
        return intervals;
    }
}
```



### 4、链表

##### （1）剑指 Offer II 022. 链表中环的入口节点:writing_hand: （快慢指针）

相同的题目：[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/) 

**问题**：给定一个链表，返回链表开始入环的第一个节点。 从链表的头节点开始沿着 `next` 指针进入环的第一个节点为环的入口节点。如果链表无环，则返回 `null`。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。**注意，`pos` 仅仅是用于标识环的情况，并不会作为参数传递到函数中。**

**说明：**不允许修改给定的链表。

例子：

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```

**数据范围**：

- 链表中节点的数目范围在范围 `[0, 10^4]` 内
- `-10^5 <= Node.val <= 10^5`
- `pos` 的值为 `-1` 或者链表中的一个有效索引

**思路**：

**判断链表是否有环**

- 设置快慢指针分别为`fast`和`slow`，初始时都指向链表头`head`
- `slow`每次走一步`slow=slow.next`；`fast`每次走两步`fast=fast.next.next`
- 由于`fast`比`slow`走的快，如果有环，那么`fast`一定先进入环，而`slow`后进入环
- 两个指针进入环后，经过若干操作后**两个指针定能在环上相遇**，即可判断一个链表是有环。

​		**证明**：当`slow`进入环时，`fast`早已进入环。**因为`fast`每次比`slow`多走一步且`fast`与`slow`的距离小于环的长度，所以`fast`与`slow`相遇时，`slow`所走的距离不超过环的长度**。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202012148680.png" alt="image-20220201214811569" width="220px" />

**求环的入口点**

- 设置两个指针，一个指向`head`，一个指向相遇点
- 两个指针同步移动，均为一次走一步，再次相遇点即为环的入口点

​	**证明**：设头结点到环的入口点的距离为 $a$，环的入口点沿着环的方向到相遇点的距离为 $x$，环长为 $r$，相遇时 `fast` 绕过了 $n$ 圈。则有 $2(a+x)=a+nr+x$，即$a=nr-x$。从头结点到环的入口点的距离等于 $n$ 倍的环长减去环的入口点到相遇点的距离，因此可以用上述方法求入口点。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202012152550.png" alt="image-20220201215222499" width="180" />

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode f = head, s = head; // 快指针和慢指针初始时都指向头结点
        while(s != null && f != null && f.next != null){
            s = s.next; // 慢指针走一步
            f = f.next.next; // 快指针走两步
            if(s == f) break; // 快慢指针相遇则存在环
        }

        if(s == null || f == null || f.next == null) return null; // 链表中没有环

        ListNode h = head; // h 从头结点开始出发, s 从相遇点开始出发
        while(h != s){ // h 和 s 每次移动一步, 相遇的位置即为环的入口点
            h = h.next;
            s = s.next;
        }

        return h;
    }
}
```

##### （2）剑指 Offer II 028. 展平多级双向链表

**问题**：

多级**双向链表**中，除了指向**下一个节点**和**前一个节点指针**之外，它还有一个**子链表指针**，可能指向单独的双向链表。这些子列表也可能会有一个或多个自己的子项，依此类推，生成多级数据结构，如下面的示例所示。

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/multilevellinkedlist.png" alt="img" width="800px" />

给定位于列表第一级的头节点，请**扁平化列表**，即将这样的多级双向链表**展平成普通的双向链表**，使所有结点出现在单级双链表中。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202202056232.png" alt="img" width="1000px" />

**思路**：**递归**

递归的目标是将`head`指针所指的多级链表展平

递归返回的是`head`指针所指的多级链表展平后的单级链表的最后一个结点

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node prev;
    public Node next;
    public Node child;
};
*/

class Solution {
    public Node dfs(Node head){
        Node last = null; // 标记 head 所指向的多级列表展平后最后一个结点
        for(Node i = head; i != null; ){
            if(i.next == null) last = i; // 记录 i 所在一级的最后一个结点
            if(i.child != null) { // 当 i 结点有 child 结点时
                Node post = i.next; // 标记 i 的后继结点

                Node cur = dfs(i.child); // 展平 i 的 child所指向的多级列表, cur指向展平后的最后一个结点
                if(i.next == null) last = cur; // i没有后继结点且i有child结点, 更新 last

                cur.next = i.next; // 将 cur 与 i的后继结点相连接
                if(i.next != null) i.next.prev = cur;

                i.next = i.child; i.child.prev = i; i.child = null; // 将 i 和 child 结点相连接

                i = post; // i 指向 i 所在一级的后继
            } else i = i.next;
        }
            
        return last;
    }

    public Node flatten(Node head) {
        dfs(head);
        return head;
    }
}
```

##### （3）剑指 Offer II 077. 链表排序:writing_hand: （归并排序）

**问题**：给定链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202211002719.jpeg" alt="img" width="400px" />

**思路**：**归并排序**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */

class Solution {
    public ListNode mergeSort(ListNode h){
        if(h == null || h.next == null) return h; // 区间中只有一个结点，则返回此结点
        ListNode s = h, f = h; // 通过快慢指针找到链表的中点
        while(f.next != null && f.next.next != null) {
            s = s.next; f = f.next.next;
        }
        ListNode mid = s.next; s.next = null;
        ListNode i = mergeSort(h), j = mergeSort(mid); // 排序

        ListNode res = new ListNode(), k = res; // 归并
        while(i != null && j != null){
            if(i.val <= j.val) { k = k.next = i; i = i.next; }
            else { k = k.next = j; j = j.next; }
        }

        if(i != null) k.next = i; 
        if(j != null) k.next = j;
        return res.next;
    }

    public ListNode sortList(ListNode head) {
        return mergeSort(head);
    }
}
```

##### （4）剑指 Offer II 078. 合并排序链表（多路归并）

**问题**：给定一个链表数组，每个链表都已经按升序排列。请将所有链表合并到一个升序链表中，返回合并后的链表。

**思路**：分治和**多路归并**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode merge(ListNode[] q, int l, int r){
        if(l == r) return q[l];

        int mid = l + r >> 1;
        ListNode i = merge(q, l, mid), j = merge(q, mid + 1, r); // 分治

        ListNode res = new ListNode(), k = res; // 归并
        while(i != null && j != null){
            if(i.val <= j.val) {k = k.next = i; i = i.next; }
            else {k = k.next = j; j = j.next;}
        }

        if(i != null) k.next = i;
        if(j != null) k.next = j;
        return res.next;
    }

    public ListNode mergeKLists(ListNode[] lists) {
        if(lists == null || lists.length == 0) return null;
        return merge(lists, 0, lists.length - 1);
    }
}
```

##### （5）剑指 Offer II 027. 回文链表

**问题**：给定一个链表的 **头节点** `head` **，**请判断其是否为回文链表。如果一个链表是回文，那么链表节点序列从前往后看和从后往前看是相同的。

**思路**：

- 找到链表的**中点**，奇数即为中间的结点，偶数为中间的两个结点中左边结点
- **反转**后半段链表
- 比较前后两段链表，判断是否相同

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    
    public ListNode reverse(ListNode h){ // 返回反转后链表的头指针
        ListNode cur = null, p = h;
        while(p != null){
            ListNode t = p.next;
            p.next = cur; // 反转
            cur = p; p = t; // 移动指针
        }
        return cur;
    }

    public boolean isPalindrome(ListNode head) {
        ListNode s = head, f = head;
        while(f.next != null && f.next.next != null){//奇数即为中间的结点,偶数为中间的两个结点中左边结点
            s = s.next;
            f = f.next.next;
        }

        ListNode l = head, r;
        r = reverse(s.next); // 反转链表的后半部分
        while(l != null && r != null){ // 双指针判断回文链表
            if(l.val != r.val) return false;
            l = l.next;
            r = r.next;
        }

        return true;
    }
}
```

##### （6）剑指 Offer II 026. 重排链表

**问题**：

给定一个单链表 `L` 的头节点 `head` ，单链表 `L` 表示为：

 $L_0 → L_1 → … → L_{n-1} → L_n$ 
请将其重新排列后变为：

$L_0 → L_n → L1 → L_{n-1} → L_2 → L_{n-2} → …$

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

**思路**：

- 找到链表的**中点**，奇数即为中间的结点，偶数为中间的两个结点中左边结点
- **反转**后半段链表
- 以中点为界，将链表分成两段
- 将两段链表**归并**成一段链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {

    public ListNode reverse(ListNode h){ // 反转链表
        ListNode cur = null, p = h;
        while(p != null){
            ListNode t = p.next;
            p.next = cur;
            cur = p; p = t;
        }
        return cur;
    }    

    public ListNode findMid(ListNode h){ // 找到链表的中点
        ListNode s = h, f = h;
        while(f.next != null && f.next.next != null){
            s = s.next;
            f = f.next.next;
        }
        return s;
    }

    public void reorderList(ListNode head) {
        ListNode mid = findMid(head), t = reverse(mid.next);
        mid.next = null; // 分成前后两个链表

        ListNode res = new ListNode(), k = res, i = head, j = t; // 归并成一条链表
        while(i != null && j != null){
            k = k.next = i; i = i.next;
            k = k.next = j; j = j.next;
        }
        if(i != null) k.next = i;
        if(j != null) k.next = j;
        head = t;
    }
}
```

##### （7）剑指 Offer II 021. 删除链表的倒数第 n 个结点（双指针）

**问题**：给定一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**思路**：**双指针**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
  
        ListNode i, j, res = new ListNode(0, head), pre = res;
        int cnt = 1;
        for(i = head, j = head; i != null; i = i.next){ // 找到倒数第n个结点
            if(cnt > n) {pre = j; j = j.next;}
            cnt ++;
        }
        pre.next = j.next; // 删除倒数第n个结点
        return res.next;
    }
}
```

##### （8）剑指 Offer II 025. 链表中的两数相加

**问题**：

给定两个 **非空链表** `l1`和 `l2` 来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

可以假设除了数字 `0` 之外，这两个数字都不会以零开头。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202211019559.png" alt="img" width="340px" />

**思路**：

- **反转**两个链表
- 模拟**高精度加法**，从低位依次相加
- 将结果链表反转

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {

    public ListNode reverse(ListNode h){
        ListNode cur = null, p = h;
        while(p != null){
            ListNode t = p.next;
            p.next = cur;
            cur = p; p = t;
        }
        return cur;
    }

    public ListNode addTwoNumbers(ListNode l1, ListNode l2) { // 类比高精度加法
        ListNode a = reverse(l1), b = reverse(l2);
        
        int t = 0; // 进位
        ListNode res = new ListNode(), i;
        for(i = res; a != null || b != null; i = i.next){
            if(a != null) { t += a.val; a = a.next; }
            if(b != null) { t += b.val; b = b.next; }
            i.next = new ListNode(t % 10, null);
            t /= 10; // 下一位的进位
        }
        if(t != 0) i.next = new ListNode(1, null); // 还有一个进位要补上
        return reverse(res.next);
    }
}
```

##### （9）面试题 02.01. 移除重复节点（哈希表）

**问题**：编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

**思路**：哈希表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        boolean[] st = new boolean[200010]; // 哈希表, 判重

        for(ListNode i = head, pre = null; i != null; i = i.next){ 
            if(!st[i.val]) { // 没出现过, 标记一下
                st[i.val] = true;
                pre = i;
            }
            else pre.next = i.next; // i出现过, 删除重复结点
        }
        return head;
    }
}
```

##### （10）剑指 Offer II 031. 最近最少使用缓存

**问题**：

运用所掌握的数据结构，设计和实现一个 [LRU (Least Recently Used，最近最少使用) 缓存机制](https://baike.baidu.com/item/LRU) 。

实现 `LRUCache` 类：

- `LRUCache(int capacity)` 以正整数作为容量 `capacity` 初始化 LRU 缓存
- `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
- `void put(int key, int value)` 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

**思路**：**哈希表**和**双向循环链表**

- 为了实现 `LRU`，需要在内存中维护一个所有页面的链表。为了实现链表各个节点的随机访问，可以用哈希表维护起来
- **当一个页面被访问时，将这个页面移到链表表头（更新优先级）**。这样就能保证**链表表尾的页面是最近最久未访问的**
- 在`get`和`put`操作后都需要更新优先级
- 缓存容量溢出，需要进行页面置换，将链表尾的页面换出

```java
class ListNode{
    int key, val;
    ListNode left, right;
    ListNode() { this.val = -1;}
    ListNode(int key) { this.key = key; this.val = -1;}
}
class LRUCache {

    ListNode h; // 双向循环链表的头指针, 每一个结点都是一个页面
    Map<Integer, ListNode> hash;
    int len, capacity; // len 表示缓存实际大小, capacity表示缓存容量

    public void add(ListNode u){ // 向双向循环链表的链表头添加一个指定结点
        u.right = h.right;
        h.right = u;
        u.left = h;
        if(u.right != h) u.right.left = u;
        if(h.left == h) h.left = u;
    }

    public void delete(ListNode u){ //  删除双向循环链表的指定结点
        u.left.right = u.right;
        u.right.left = u.left;
    }

    public LRUCache(int capacity) {
        h = new ListNode(); // 初始化双向循环链表的头指针
        h.left = h; h.right = h;

        hash = new HashMap<>();

        len = 0;
        this.capacity = capacity;
    }
    
    public int get(int key) {
        if(hash.containsKey(key)) {
            delete(hash.get(key)); // 更新优先级
            add(hash.get(key));
            return hash.get(key).val;
        } else return -1;
    }
    
    public void put(int key, int value) {
        if(!hash.containsKey(key)){
            if(len >= capacity) { // 当缓存容量达到上限时，在写入新数据之前删除最久未使用的数据值
                hash.remove(h.left.key);
                delete(h.left);
                len --;
            }
            hash.put(key, new ListNode(key));
            add(hash.get(key)); // 插入表头
            len ++;
        } else {
            delete(hash.get(key)); // 更新优先级
            add(hash.get(key));
        }
        hash.get(key).val = value;
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

##### （11）24. 两两交换链表中的节点（递归）

**问题**：给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202211034339.jpeg" alt="img" width="300px" />

**思路**：**递归**

- 找终止条件：本题终止条件很明显，当递归到**链表为空**或者**链表只剩一个元素**的时候，没得交换了，自然就终止了。
- 找返回值：返回给上一层递归的值应该是**已经交换完成后的子链表**。
- 单次的过程：因为递归是重复做一样的事情，所以从宏观上考虑，只用考虑某一步是怎么完成的。我们假设待交换的俩节点分别为`head`和`next`，`next`的应该接受上一级返回的子链表(参考第2步)。就相当于是一个**含三个节点的链表交换前两个节点**，就很简单了

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {

    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) return head;

        ListNode res = head.next;
        head.next = swapPairs(res.next);
        res.next = head;
        
        return res;
    }
}
```

##### （12）2181. 合并零之间的节点（递归）

**问题**：

给你一个链表的头节点 `head` ，该链表包含由 `0` 分隔开的一连串整数。链表的 **开端** 和 **末尾** 的节点都满足 `Node.val == 0` 。

对于每两个相邻的 `0` ，请你将它们之间的所有节点合并成一个节点，其值是所有已合并节点的值之和。然后将所有 `0` 移除，修改后的链表不应该含有任何 `0` 。

返回修改后链表的头节点 `head` 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202211044445.png" alt="img" width="600px" />

**思路**：**递归**

- 找终止条件：当递归到链表只剩一个元素且链表结点的值为 `0`，没得合并了，递归自然结束
- 找返回值：返回给上一层递归的值应该是**已经合并完成后子链表**
- 单次过程：每两个相邻的 `0` ，将它们之间的所有节点合并成一个节点

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
   
    public ListNode mergeNodes(ListNode head) {
        if(head.val == 0 && head.next == null) // 当递归到链表只剩一个元素且链表结点的值为 0，没得合并了，递归结束
            return head.next;
        
        int res = 0;
        ListNode i; // 每两个相邻的 0 ，将它们之间的所有节点合并成一个节点
        for(i = head.next; i.val != 0; i = i.next)
            res += i.val;
        head.next.val = res;
        
        head.next.next = mergeNodes(i);
        
        return head.next; // 返回给上一层递归的值应该是已经合并完成后子链表
    }
}
```



### 5、前缀和

##### （1）剑指 Offer II 013. 二维子矩阵的和

```java
class NumMatrix {

    int[][] s; // 前缀和数组, 下标都从1开始
    int m, n;

    public NumMatrix(int[][] matrix) { // matrix 下标是从0开始
        m = matrix.length + 1;
        n = matrix[0].length + 1;
        s = new int[m][n];

        for(int i = 1; i < m; i ++) // 初始化前缀和数组
            for(int j = 1; j < n; j ++)
                s[i][j] = matrix[i-1][j-1] + s[i][j-1] + s[i-1][j] - s[i-1][j-1];

    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) { // 此处给出的下标是从0开始
        row1 ++; col1 ++; row2 ++; col2 ++; // 前缀和数组的下标要求从1开始
        return s[row2][col2] - s[row2][col1-1] - s[row1-1][col2] + s[row1-1][col1-1];
    }
}

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * int param_1 = obj.sumRegion(row1,col1,row2,col2);
 */
```

##### （2）724. 寻找数组的中心下标

**问题**：

给你一个整数数组 `nums` ，请计算数组的**中心下标** 。

数组**中心下标**是数组的一个下标，其**左侧所有元素相加的和**等于**右侧所有元素相加的和**。

如果中心下标位于数组最左端，那么左侧数之和视为 0 ，因为在下标的左侧不存在元素。这一点对于中心下标位于数组最右端同样适用。

如果数组有多个中心下标，应该返回**最靠近左边**的那一个。如果数组不存在中心下标，返回 -1 。

**思路**：求前缀和，计算某个元素左侧元素之和与右侧元素之和是否相等

```java
class Solution {
    public int pivotIndex(int[] nums) {
        int n = nums.length;
        // s[0]心下标位于数组最左端，那么左侧数之和视为 0
        // s[n + 1]-s[n]心下标位于数组最右端，那么有侧数之和视为0
        int[] s = new int[n + 2]; //前缀和数组, 

        for(int i = 1; i <= n; i ++) s[i] = s[i - 1] + nums[i - 1];
        s[n + 1] = s[n]; // 初始化最后一个占位数

        // 枚举每一个位置判断其是否是中心下标
        for(int i = 1; i <= n; i ++)
            if(s[s.length - 1] - s[i] == s[i - 1])
                return i - 1;
        return - 1;
    }
}
```

##### （3）剑指 Offer II 010. 和为 k 的子数组:writing_hand: 

**问题**：给定一个**整数**数组和一个整数 `k` **，**请找到该数组中**和为 `k` 的连续子数组的个数**。

**思路**：

此题与“滑动窗口问题类似“，但是不能用滑动窗口来求解，滑动窗口要求数组为**正整数**

但是，可以使用**哈希表**和**前缀和**来求解，时间复杂度 $O(n)$

- 利用哈希表记录以当前下标 `i` 结尾的子数组之前的所有子数组的前缀和
- 利用下标`i`结尾的子数组的`前缀和 - k` ，即可得知前缀和与`k`的差值 `x`
- 再利用哈希表获取前缀和为`x`的子数组个数
- 即可知道以下标`i`结尾的子数组含有多少个和为`k`的子数组

记录出每一个前缀和就相当于记录了所有子数组的和，因为两个前缀和相减就是它们之间子数组的和

```java
class Solution { // 滑动窗口适用与是"正整数数组"
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> hash = new HashMap<>(); // key为前缀和, value表示前缀和key的前缀数组个数
        int[] s = new int[nums.length + 1]; // 前缀和数组

        int cnt = 0;
        hash.put(s[0], 1); // 初始化hash

        for(int i = 1; i < s.length; i ++){
            s[i] = s[i - 1] + nums[i - 1]; // 求前缀和

            int diff = s[i] - k;
            if(hash.containsKey(diff)) cnt += hash.get(diff);// 以i结尾的子数组中,和为k的子数组个数
            hash.put(s[i], hash.getOrDefault(s[i], 0) + 1);
        }
        return cnt;
    }
}
```

##### （4）974. 和可被 K 整除的子数组:writing_hand: 

**问题**：

给定一个**整数**数组 `nums` 和一个整数 `k` ，返回其中元素之和**可被 `k` 整除的**（连续、非空） **子数组** 的数目。

**子数组** 是数组的 **连续** 部分

**思路**：

该题与上一题类似，但是应用了**同余定理**和**负数取模**

令 $s[i]=nums[0]+nums[1]+…+nums[i]$。那么每个连续子数组的和 $sum(i,j)$ 就可以写成 $s[j] - s[i-1]$（其中 $0<i<j$）的形式。此时，判断子数组的和能否被 $k$ 整除就等价于判断 $(s[j]−s[i−1]) \mod k==0$，根据**同余定理**，只要 $s[j] \mod k==s[i−1] \mod k$，就可以保证上面的等式成立

- 因此我们可以考虑对数组进行遍历，在遍历同时统计答案。
- 当我们遍历到第 $i$ 个元素时，我们统计以 $i$ 结尾的符合条件的子数组个数。我们可以维护一个以**前缀和模 $k$ 的值**为键，**出现次数**为值的**哈希表** `hash`，在遍历的同时进行更新。
- 这样在计算以 $i$ 结尾的符合条件的子数组个数时，根据上面的分析，答案即为 $[0..i−1]$ 中前缀和模 $k$ 也为 $s[i] \bmod k$ 的位置个数，即 $hash[s[i] \mod k]$。
- 最后的答案即为以每一个位置为数尾的符合条件的子数组个数之和。需要注意的一个边界条件是，我们需要**对哈希表初始化**，记录 $hash[0]=1$，这样就考虑了**前缀和本身被 $k$ 整除的情况**。


**注意**：不同的语言**负数取模**的值不一定相同，有的语言为负数，对于这种情况需要特殊处理

```java
class Solution {
    public int getMod(int a, int b){ // 得到 a % b 的正余数
        return (a % b + b) % b;
    }

    public int subarraysDivByK(int[] nums, int k) {
        // key为前缀和对k取模的正余数, 表示对k取模余数为key的前缀数组的个数
        int[] hash =  new int[k];
        int[] s = new int[nums.length + 1];

        hash[getMod(s[0], k)] = 1; // 初始化哈希表
        int cnt = 0;

        for(int i = 1; i < s.length; i ++){
            s[i] = s[i - 1] + nums[i - 1];
            // 同余定理：如果s[i] % k == s[j] % k, 那么s[i+1,j] % k == 0
            // 找出以i为结尾数组中，和可被 k 整除的子数组的个数
            int mod = getMod(s[i], k);
            cnt += hash[mod];
            hash[mod] ++;
        }
        return cnt;
    }
}
```

##### （5）523. 连续的子数组和

**问题**：

给你一个**整数**数组 `nums` 和一个整数 `k` ，编写一个函数来判断该数组是否含有同时满足下述条件的连续子数组：

- 子数组大小至少为 `2` ，且
- 子数组元素总和为 `k` 的倍数。

如果存在，返回 `true` ；否则，返回 `false` 。

**思路**：与上一题思路类似

```java
class Solution {
    public int getMod(int a, int b){
        return (a % b + b) % b;
    }

    public boolean checkSubarraySum(int[] nums, int k) {
        if(nums.length < 2) return false; // 特判
        int n = nums.length;
        HashMap<Integer, Integer> hash = new HashMap<>();//以下标为value结尾的子数组前缀和是key, 且value最小
        int[] s = new int[n + 10];

        hash.put(getMod(s[0], k), 0); // 初始化哈希表
        for(int i = 1; i <= n; i ++){
            s[i] = s[i - 1] + nums[i - 1];

            int mod = getMod(s[i], k);
            if(hash.containsKey(mod)){
                if(i - hash.get(mod) >= 2) return true; // 判断满足条件的连续子数组长度是否大于等于2
            } else hash.put(mod, i);
        }
        return false;
    }
}
```

##### （6）930. 和相同的二元子数组

**问题**：

给你一个**二元数组** `nums` ，和一个整数 `goal` ，请你统计并返回有多少个**和为 `goal` 的 非空 子数组**。

**子数组** 是数组的一段连续部分

**注意**：

- `1 <= nums.length <= 3 * 104`
- **`nums[i]` 不是 `0` 就是 `1`**
- `0 <= goal <= nums.length`

**思路**：

**解法一**：**前缀和**与**数组模拟哈希表**

因为`num[i]`取值不是`0`就是`1`，因此前缀和最大的取值不超过`nums.length`，因此可以使用数组模拟哈希表

```java
class Solution {
    public int numSubarraysWithSum(int[] nums, int goal) {        
        int n = nums.length;
        int[] hash = new int[n + 10];
        int[] s = new int[n + 10];
        hash[s[0]] = 1; // 初始化哈希表
        
        int cnt = 0;
        for(int i = 1; i <= n; i ++){
            s[i] = s[i - 1] + nums[i - 1];

            int diff = s[i] - goal;
            if(diff >= 0) cnt += hash[diff];
            hash[s[i]] ++ ;
        }
        return cnt;
    }
}
```

**解法二**：**滑动窗口**

因为数组中**不存在负数**，所有元素都是非负数，所以可以使用滑动窗口求解

由于存在0的个数较多，在判零的过程中，时间花费较多

```java
class Solution {
    public int numSubarraysWithSum(int[] nums, int goal) {        
        int res = 0;
        int sum = 0; // 窗口内元素的和
        for(int l = 0, r = 0; r < nums.length; r ++){
            sum += nums[r];
            while(l <= r && sum > goal) sum -= nums[l ++];
            if(l <= r && sum == goal){
                int cnt = 0; // 当窗口内的元素和等于goal时, cnt表示窗口[l,l+cnt)都是连续的0,有cnt个0
                for(int i = l; i < r && nums[i] == 0; i ++) cnt ++;
                res += cnt + 1; // [l,r]的子数组中, 和等于goal的子数组有cnt+1个
            }
        }
        return res;
    }
}
```

##### （7）1248. 统计「优美子数组」:writing_hand: 

**问题**：

给你一个**整数**数组 `nums` 和一个整数 `k`。如果某个连续子数组中**恰好有 `k` 个奇数**数字，我们就认为这个子数组是**「优美子数组」**。

请返回这个数组中 **「优美子数组」** 的数目。

**思路**：此题与上一题思路类似

- 将数组`nums`中的**奇数变为1**，**偶数变为0**，此时`nums[i]` 不是 `0` 就是 `1`
- ”某个连续子数组中恰好有 `k` 个奇数数字，求这样连续子数组的个数“，可以转化为**统计并返回有多少个和为 `k` 的 连续子数组**

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        int n = nums.length;
        int[] a = new int[n + 10], s = new int[n + 10];
        int[] hash = new int[n + 10];

        int cnt = 0;
        hash[s[0]] = 1;
        for(int i = 1; i <= n; i ++){
            a[i] = nums[i - 1] % 2; // 奇数为 1, 偶数为 0
            s[i] = s[i - 1] + a[i];

            int diff = s[i] - k;
            if(diff >= 0) cnt += hash[diff];
            hash[s[i]] ++ ;
        }
        return cnt;
    }
}
```

##### （8）剑指 Offer II 012. 左右两边子数组的和相等

**前缀和**

```java
class Solution {

    int[] a, s; // 原数组 前缀和数组
    int n; // 元素的个数

    public int pivotIndex(int[] nums) {
        n = nums.length;
        a = new int[n + 10];
        s = new int[n + 10];
        
        for(int i = 1; i <= n; i ++){ // 原数组和前缀和数组从下标1的位置处存储
            a[i] = nums[ i - 1];
            s[i] = s[i - 1] + a[i];
        }
        // 如果中心下标位于数组最左端，那么左侧数之和视为 0(s[0] == 0)
        // 如果中心下标位于数组最右端，那么右侧数之和视为 0(s[n]-s[n] == 0)
        for(int i = 1; i <= n; i ++) // 枚举所有的中心下标
            if(s[i - 1] == s[n]-s[i]) // 求第一个满足要求的中心下标
                return i - 1;
        return -1;
    }

}
```

##### （9）剑指 Offer II 011. 0 和 1 个数相同的子数组

**问题**：给定一个二进制数组 `nums` , 找到含有**相同数量的 `0` 和 `1` 的最长连续子数组**，并返回该子数组的长度。`nums[i]` 不是 `0` 就是 `1`

思路：将问题进行转换

- 将原数组中所有的 `0` 边为`-1`
- “找到含有**相同数量的 `0` 和 `1` 的最长连续子数组**的长度”可以等价转换为找到**和为 0 的最长连续子数组**
- 此问题就转化为了经典的**哈希表与前缀和**问题了

```java
class Solution {
    // value 表示所有前缀和为key的数组[l,r]中,右下标的最小值  
    Map<Integer, Integer> hash = new HashMap<>();
    int[] a, s; //原数组和前缀和数组, 下标从 1 开始
    int n;

    public int findMaxLength(int[] nums) {
        n = nums.length;
        a = new int[n + 10];
        s = new int[n + 10];

        int res = 0; // 相同数量的 0 和 1 的最长连续子数组的长度
        hash.put(s[0], 0); // 哈希表初始化

        for(int i = 1; i <= n; i ++){
            if(nums[i - 1] == 0) a[i] = -1; // 将所有的0转变为 -1 
            else a[i] = nums[i - 1];

            s[i] = s[i - 1] + a[i]; // 求前缀和

            int diff = s[i] - 0;
            if(hash.containsKey(diff)) // 以i结尾的子数组中,和为0的最长连续子数组
                res = Math.max(res, i - hash.get(diff)); 
            else hash.put(s[i], i);
        }
        return res;
    }
}
```



### 6、二分

##### （1）剑指 Offer II 072. 求平方根

**问题**：给定一个非负整数 `x` ，计算并返回 `x` 的平方根，即实现 `int sqrt(int x)` 函数。正数的平方根有两个，只输出其中的正数平方根。如果平方根不是整数，输出只保留整数的部分，小数部分将被舍去。

**思路**：**实数二分**

```java
class Solution {
    final double eps = 1e-6; // 精度

    public boolean check(double y, double x){
        if(y * y - x > eps) return true;
        else return false;
    }

    public double bsearch(double l, double r, double x){//l二分的左边界,r二分的右边界,srqt(x)二分的值
        while(r - l > eps){
            double mid = (l + r)/2;
            if(check(mid, x)) r = mid;
            else l = mid;
        }
        return r; // 因为是r - l > eps, 取右边界更精确一点
    }

    public int mySqrt(int x) {
        if(x == 1 || x == 0) return x; // 特判
        return (int)bsearch((double)1, (double)x, (double)x);
    }
}
```

##### （2）剑指 Offer II 068. 查找插入位置

**问题**：给定一个**排序的**整数数组 `nums` 和一个整数目标值 `target` ，请在数组中找到 `target` ，并返回其下标。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

**思路**：二分查找寻找上界

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
       int l = 0, r = nums.length - 1;
       while(l < r){
           int mid = l + r >> 1;
           if(nums[mid] < target) l = mid + 1;
           else r = mid;
       }
       if(nums[l] < target) return l + 1; // 特判, 要找的值不在数组内, 要插入到数组的最右侧
       return l;
    }
}
```

##### （3）剑指 Offer II 069. 山峰数组的顶部:writing_hand: 

**问题**：

符合下列属性的数组 `arr` 称为 **山峰数组**（**山脉数组）** ：

- `arr.length >= 3`
- 存在 `i`（`0 < i < arr.length - 1`）使得：
  - `arr[0] < arr[1] < ... arr[i-1] < arr[i]`
  - `arr[i] > arr[i+1] > ... > arr[arr.length - 1]`

给定由整数组成的山峰数组 `arr` ，返回任何满足 `arr[0] < arr[1] < ... arr[i - 1] < arr[i] > arr[i + 1] > ... > arr[arr.length - 1]` 的下标 `i` ，即山峰顶部。

**思路**：**二分找最值点**

```java
class Solution {
    public int bsearch(int[] a){
        int l = 0, r = a.length - 1;
        while(l < r){
            int mid = l + r >> 1;
            if(a[mid] < a[mid + 1]) l = mid + 1;
            else r = mid;
        }
        return l;
    }

    public int peakIndexInMountainArray(int[] arr) {
        return bsearch(arr);
    }
}
```



### 7、动态规划

##### （1）剑指 Offer II 099. 最小路径之和

**问题**：

给定一个包含非负整数的 $m \times n$ 网格 `grid` ，请找出一条从**左上角**到**右下角**的路径，使得**路径上的数字总和**为**最小**。

一个机器人每次只能**向下**或者**向右**移动一步

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202072252969.jpeg" alt="img" width="200px"/>

**思路**：

```java
class Solution {
    // f[i][j] 表示从(0,0)到(i,j)的最小路径和
    // f[i][j] = min(f[i][j-1], f[i-1][j]) + a[i][j], a[i][j]非负
    // 可以到达(i,j)点为(i,j-1)和(i-1,j)
    // 那么起点到达(i,j)的最小路径之和,转化为起点到达(i,j-1)的最小路径之和与起点到(i-1,j)的最小路径之和取最小值, 然后加上a[i][j]

    int[][] f; // 下标从1开始
    int m, n;
    
    public int minPathSum(int[][] grid) { // grid下标从0开始
        m = grid.length + 1; n = grid[0].length + 1;

        f = new int[m][n]; // 初始化数组, 比原数组扩展一行, 扩展一列
        Arrays.fill(f[0], Integer.MAX_VALUE); // 第一行初始化为最大值
        for(int i = 0; i < m; i ++) f[i][0] = Integer.MAX_VALUE; // 第一列初始化为最大值
        f[0][1] = 0; f[1][0] = 0; // 特定的两个位置要初始化为0
    
        for(int i = 1; i < m; i ++) 
            for(int j = 1; j < n; j ++)
                f[i][j] = Math.min(f[i][j-1], f[i-1][j]) + grid[i-1][j-1]; // 状态转移
        
        return f[m - 1][n - 1]; // 从左上角到右下角的所有路径上的最小数字总和
    }
}
```

##### （2）剑指 Offer II 098. 路径的数目

**问题**：

一个机器人位于一个 $m \times n$ 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能**向下**或者**向右**移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问**总共有多少条不同的路径**？

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202072254727.png" alt="img" width="300px" />

**思路**：

```java
class Solution {

    // f[i][j] 表示从(0,0)到达(i,j)的路径数量
    // f[i][j] = f[i][j-1] + f[i-1][j]
    // 可以到达(i,j)点为(i,j-1)和(i-1,j)
    // 那么起点到达(i,j)的路径数目,转化为起点到达(i,j-1)的路径数目和起点到(i-1,j)的路径数目之和

    public int uniquePaths(int m, int n) {    
        int[][] f = new int[m + 1][n + 1];
        f[1][1] = 1; // 初始化数组, f[0][i]和f[i][0]都默认初始化为0

        for(int i = 1; i <= m; i ++) 
            for(int j = 1; j <= n; j ++){ // 状态转移
                if(i == 1 && j == 1) continue; // 特判,f[1][1]无需进行状态转移,因为已经提起初始化了
                f[i][j] = f[i][j-1] + f[i-1][j];
            }      
        return f[m][n];
    }
}
```

##### （3）剑指 Offer II 103. 最少的硬币数目

**问题**

给定不同面额的硬币 `coins` 和一个总金额 `amount`。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 `-1`。

你可以认为每种硬币的数量是无限的。

**思路**

此问题类似**完全背包问题**，采用动态规划求解，使用滚动数组优化

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208161454628.png" alt="image-20220816145457475" width="750px" />

**朴素DP** $O(n^2m)$

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int m = amount;
        int[][] f = new int[n + 1][m + 1]; // f[i][j] 从前i个硬币中选, 总价值等于j的所有方案的集合, 所用到的硬币数量最少是f[i][j]

        for(int i = 0; i <= n; i ++ )
            Arrays.fill(f[i], 0x3f3f3f3f);
        for(int i = 0; i <= n; i ++ )
            f[i][0] = 0;

        for(int i = 1; i <= n; i ++ )
            for(int j = 1; j <= m; j ++) 
                for(int k = 0; j >= k * coins[i - 1]; k ++ )
                    f[i][j] = Math.min(f[i][j], f[i - 1][j - k * coins[i - 1]] + k);
            
        if(f[n][m] == 0x3f3f3f3f) return -1;
        return f[n][m];
    }
}
```

**优化k** $O(n^2)$ 

$O(NV)$
$$
f[i][j]=\max \left(f[i-1][j], f[i-1][j-coins[i]]+1,f[i-1][j-2coins[i]]+2, \ldots\right)\\
\\ f[i][j-coins[i]]=\max \left(f[i-1][j-coins[i]], f[i-1][j-2coins[i]]+1,f[i-1][j-3coins[i]]+2, \ldots\right)
$$
得到状态转移方程：$f[i][j] = max(f[i-1][j], f[i][j-coins[i]] + 1)$ 

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int m = amount;
        int[][] f = new int[n + 1][m + 1]; // f[i][j] 从前i个硬币中选, 总价值等于j的所有方案的集合, 所用到的硬币数量最少是f[i][j]

        for(int i = 0; i <= n; i ++ ) {
            Arrays.fill(f[i], 0x3f3f3f3f);
            f[i][0] = 0;
        }

        for(int i = 1; i <= n; i ++ )
            for(int j = 1; j <= m; j ++) {
                f[i][j] = f[i - 1][j];
                if(j >= coins[i - 1]) f[i][j] = Math.min(f[i][j], f[i][j - coins[i - 1]] + 1);
            }
                
        if(f[n][m] == 0x3f3f3f3f) return -1;
        return f[n][m];
    }
}
```

**滚动数组优化**

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int m = amount;
        int[] f = new int[m + 1]; // f[i][j] 从前i个硬币中选, 总价值等于j的所有方案的集合, 所用到的硬币数量最少是f[i][j]

        Arrays.fill(f, 0x3f3f3f3f);
        f[0] = 0;

        for(int i = 1; i <= n; i ++ )
            for(int j = 1; j <= m; j ++) 
                if(j >= coins[i - 1]) f[j] = Math.min(f[j], f[j - coins[i - 1]] + 1);  
        
        if(f[m] == 0x3f3f3f3f) return -1;
        return f[m];
    }
}
```

### 8、滑动窗口:writing_hand: 

##### （1）剑指 Offer II 008. 和大于等于 target 的最短子数组:writing_hand: 

**问题**：

给定一个含有 $n$ 个**正整数**的数组和一个正整数 $target$ 

找出该数组中满足其和 $≥ target$ 的**长度最小的连续子数组** $[nums_l, nums_l+1, ..., nums_r-1, nums_r]$ ，并返回其长度。如果不存在符合条件的子数组，返回 0 

**思路**：时间复杂度$O(n)$，空间复杂度$O(1)$

- 使用**滑动窗口**维护和大于等于$target$的连续子数组
- 每次向窗口右侧添加一个元素（窗口扩大）
- 当窗口中所有元素的和大于等于 $target$ 时，记录此时的窗口长度，同时删除最早加入到窗口的元素（窗口缩小）
- 循环上一步，直到当窗口中所有元素的和小于 $target$ 
- 循环第二步，直到所有元素都添加到窗口中一次

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        // 滑动窗口维护满足和≥target的连续子数组
        int sum = 0; // 窗口中所有元素的和
        int res = Integer.MAX_VALUE; // 窗口的最小长度

        for(int l = 0, r = 0; r < nums.length; r ++){
            sum += nums[r]; // 窗口扩大, 向窗口右侧中添加元素
            while(l <= r && sum >= target){ // 如果窗口中所有元素的和>= target
                res = Math.min(res, r - l + 1); // 记录窗口小长度
                sum -= nums[l ++]; // 从窗口左侧删除元素
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

##### （2）剑指 Offer II 009. 乘积小于 K 的子数组:writing_hand: 

**问题**：

给定一个**正整数**数组 `nums`和整数 `k` ，请找出该数组内乘积**小于** `k` 的**连续的子数组的个数**

**思路**：

- 使用滑动窗口维护数组内乘积小于`k`的连续子数组
- 每次向出口的右侧添加一个元素（窗口扩大）
- 当窗口内所有元素的乘积大于等于`k`时，删除最早加入窗口的元素（窗口缩小）
- 循环上一步, 直到窗口内所有元素的乘积小于`k`
- 此时窗口内维护的连续子数组一定满足乘积小于`k`
  - **如果原窗口是 ABC，现在窗口加入了 X，变成了ABCX, 那么就新增了4个子数组 X, CX, BCX, ABCX**
  - 累加解的个数，解的个数增加了 $(r-l+1)$ 个
- 循环第二步，直到所有元素都添加动窗口中一次

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        // 用一个滑动窗口维护数组内乘积小于k的连续子数组
        int mult = 1; // 窗口内元素的乘积
        int res = 0; // 该数组内乘积小于 k 的连续的子数组的个数

        for(int l = 0, r = 0; r < nums.length; r ++){ // l窗口的左边界, r 窗口的右边界
            mult *= nums[r]; // 不断扩张窗口右边界
            while(l <= r && mult >= k) // 当窗口内元素的乘积大于等于k时,收缩窗口的左边界
                mult /= nums[l ++];
            res += ((r-1)-l+1) + 1; 
        // 如果原窗口是 ABC，现在新窗口加入了 X, ABCX, 那么就新增了4个子数组X, CX, BCX, ABCX
        // res 就增加了 r-l+1
        }
        return res; 
    }
}
```

##### （3）剑指 Offer II 014. 字符串中的变位词:writing_hand: 

**问题**：

给定两个字符串 **s1** 和 **s2**，写一个函数来判断 **s2** 是否包含 **s1** 的某个变位词。

换句话说，**字符串 s1 的排列之一是字符串 s2 的子串** 

**思路**：

- 因为字符串中**全是小写字母**，故可以用长度为**26**的数组模拟哈希表。
- 把字符串 s1 的每个字符存入哈希表，哈希表的第一个元素表示字母，第二个元素表示该字母在整个字符串中出现的次数；
- 遍历字符串 s2，用滑动窗口的思想，窗口大小即字符串 s1 的长度，在哈希表中减去窗口内出现相应字母的次数。如果遍历到某一窗口时，哈希表中的值全为 0，则在字符串 s2 中该窗口内的子串为 s1 的变位词

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int len1 = s1.length(), len2 = s2.length();
        if(len2 < len1) return false; // 特判

        int[] hash = new int[26]; // 记录了s1每个字符出现的次数
        for(int i = 0; i < len1; i ++) hash[s1.charAt(i) - 'a'] ++;
        
        // 滑动窗口维护字符串s1的任意排列
        // 窗口的左边界l, 窗口右边界r
        for(int l = 0, r = 0; r < len2; r ++){
            char ch = s2.charAt(r); // 窗口扩大
            hash[ch - 'a'] -- ;
            // 将窗口左边第一个字符ch及其左边的所有元素都移出窗口, 因为它们无法构成有效解
            while(l <= r && hash[ch - 'a'] < 0) hash[s2.charAt(l ++) - 'a'] ++; // 窗口缩小
            if(r - l + 1 == len1) return true; // 窗口中的所有字符构成一个解
        }
        return false;
    }
}
```

##### （4）剑指 Offer II 015. 字符串中的所有变位词

**问题**：

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **变位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**变位词** 指字母相同，但排列不同的字符串

**思路**：

- 此题与上一题的思路相似，需要**统计出所有的变位词**

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        int slen = s.length(), plen = p.length();
        if(slen < plen) return new ArrayList<Integer>(); // 特判

        int[] hash = new int[26]; // 字符集hash表
        for(int i = 0; i < plen; i ++) hash[p.charAt(i)-'a'] ++ ;

        List<Integer> idxs = new ArrayList<>();
        for(int l = 0, r = 0; r < slen; r ++){
            char ch = s.charAt(r);
            hash[ch - 'a'] -- ; // 窗口扩大

            while(hash[ch - 'a'] < 0) hash[s.charAt(l ++) - 'a'] ++ ; // 窗口搜索
            if(r - l + 1 == plen) idxs.add(l); // 判断
        }
        return idxs;
    }
}
```

##### （5）剑指 Offer II 017. 含有所有字符的最短字符串:writing_hand: 

**问题**：

给定两个字符串 `s` 和 `t` 。返回 `s` 中**包含 `t` 的所有字符**的**最短子字符串**。如果 `s` 中不存在符合条件的子字符串，则返回空字符串 "" 

如果 `s` 中存在多个符合条件的子字符串，返回任意一个。

**思路**：



**注意**：

-  对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量

- `s` 和 `t` 由英文字母组成

```java
class Solution {
    public String minWindow(String s, String t) {
        if(s.length() < t.length()) return ""; // 特判
        // 滑动窗口维护所有包含t的所有字符的子字符串
        // hs[i]维护窗口中字符i出现的次数
        int[] hs = new int[58], ht = new int[58]; // ht[i]是t中字符i出现的次数
        for(int i = 0; i < t.length(); i ++) ht[t.charAt(i) - 'A'] ++; // 将 A-z映射到0-57

        int cnt = 0; //窗口中有效字符个数 
        String res = "";
        for(int l = 0, r = 0; r < s.length(); r ++){
            char ch = s.charAt(r);
            hs[ch - 'A'] ++ ; // 窗口扩大
            if(hs[ch - 'A'] <= ht[ch - 'A']) cnt ++ ; // 表示ch是t中的字符,且满足t中的出现次数
            // 如果向窗口中加入字符ch后,窗口左端是字符ch且窗口中ch的个数超出了t中的出现次数, 则ch移除窗口
            // 直至窗口左端是字符出现的次数满足t中的出现次数
            while(l <= r && hs[s.charAt(l)-'A'] > ht[s.charAt(l)-'A'])
                hs[s.charAt(l ++) - 'A'] --; // 窗口缩小
   
            if(cnt == t.length() && (res.isEmpty() || res.length() > (r - l + 1))) 
                res = s.substring(l, r + 1); // 取包含 t 的所有字符的最短子字符串
        }
        return res;
    }
}
```



##### （6）剑指 Offer II 057. 值和下标之差都在给定的范围内:writing_hand: 

**问题**：

给你一个整数数组 `nums` 和两个整数 `k` 和 `t` 。请你判断是否存在 两个不同下标 `i` 和 `j`，使得 $abs(nums[i] - nums[j]) <= t$ ，同时又满足 $abs(i - j) <= k$ 。如果存在则返回 `true`，不存在返回 `false`

**思路**：

对于序列中每一个元素 $x$ 左侧的至多 $k$ 个元素，如果这 $k$ 个元素中存在一个元素落在区间 $[x - t, x + t]$ 中，我们就找到了一对符合条件的元素。注意到对于两个相邻的元素，它们各自的左侧的 $k$ 个元素中有 $k - 1$ 个是重合的。于是我们可以使用滑动窗口的思路，维护一个大小为 $k$ 的**滑动窗口**，每次遍历到元素 $x$ 时，**滑动窗口中包含元素 $x$ 前面的最多 $k$ 个元素**，我们**检查窗口中是否存在元素落在区间 $[x - t, x + t]$ 中**即可。

如果使用**队列维护滑动窗口**内的元素，由于元素是无序的，我们只能对于每个元素都遍历一次队列来检查是否有元素符合条件。如果数组的长度为 $n$，则使用队列的时间复杂度为 $O(nk)$，**会超出时间限制**。

因此我们希望能够**找到一个数据结构维护滑动窗口**内的元素，该数据结构需要满足以下操作：

- 支持添加和删除指定元素的操作，否则我们无法维护滑动窗口；

- 内部元素**有序**，**支持二分查找**的操作，这样我们可以快速判断滑动窗口中是否存在元素满足条件，具体而言，对于元素 $x$，当我们希望判断滑动窗口中是否存在某个数 $y$ 落在区间 $[x - t, x + t]$ 中，只需要判断滑动窗口中所有大于等于 $x - t$ 的元素中的最小元素是否小于等于 $x + t$ 即可。


我们可以使用**有序集合**来支持这些操作。

实现方面，我们在有序集合中查找大于等于 $x - t$ 的最小的元素 $y$，如果 $y$ 存在，且 $y \leq x + t$y，我们就找到了一对符合条件的元素。完成检查后，我们将 $x$ 插入到有序集合中，如果有序集合中元素数量超过了 $k$，我们将有序集合中最早被插入的元素删除即可[2]。

**注意**

- 如果当前有序集合中存在相同元素，那么此时程序将直接返回 `true`。因此本题中的有序集合无需处理相同元素的情况。
- 为防止整型 `int` 溢出，我们既可以使用长整型 `long`，也可以对查找区间 $[x - t, x + t]$ 进行限制，使其落在 `int` 范围内。

```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        // 元素大小为x, 左边存在k个数, 如果这个k个数中至少存在一个元素落在[x-t, x+t], 那么就找到一个解
        // 对于两个相邻的元素，它们各自的左侧的 k 个元素中有 k - 1 个是重合的
        // 于是我们可以使用滑动窗口的思路，维护一个大小为k的滑动窗口，每次遍历到元素x时，
        // 滑动窗口中包含元素x前面的最多k个元素，我们检查窗口中是否存在元素落在区间 [x - t, x + t]中即可
        if(k == 0) return false; // 特判
        TreeSet<Long> set = new TreeSet<>(); // 用treeset维护窗口中的k个元素

        for(int l = 0, r = 0; r < nums.length - 1; r ++){
            set.add((long)nums[r]); // 窗口扩大
            // 当窗口中的元数个数大于k个数, 将最早加入的元素删除
            while(r - l + 1 > k && set.size() > k) set.remove((long)nums[l ++]); // 窗口缩小

            Long temp = set.ceiling((long)nums[r + 1] - t); // nums[r + 1] 即为x
            if(temp != null && temp.longValue() <= (long)nums[r + 1] + t) //窗口中有数落在[x-t,x+t]
                return true;
        }
        return false;
    }
}
```

##### （7）剑指 Offer II 041. 滑动窗口的平均值

**问题**：

给定一个整数数据流和一个窗口大小，根据该滑动窗口的大小，计算滑动窗口里所有数字的**平均值**。

实现 `MovingAverage` 类：

- `MovingAverage(int size)` 用窗口大小 `size` 初始化对象。
- `double next(int val)` 成员函数 `next` 每次调用的时候都会往滑动窗口增加一个整数，请计算并返回数据流中最后 size 个值的移动平均值，即滑动窗口里所有数字的平均值。

```java
class MovingAverage {
    
    final int N = 10010;
    int[] q = new int[N]; // 用队列来维护一个滑动窗口
    int hh = 0, tt = -1; // 队列的队头指针和队尾指针
    int size; // 窗口的大小
    int sum = 0; // 窗口中所有元素之和

    public MovingAverage(int size) {this.size =size;}
    
    public double next(int val) {
        if(hh <= tt && tt - hh + 1 >= size) sum -= q[hh ++]; // 窗口满了, 缩小窗口
        q[++ tt] = val; // 扩大窗口
        sum += val;
        return sum / (double)(tt - hh + 1); // 窗口的大小为tt - hh + 1
    }
}

```



### 9、哈希表

##### （1）剑指 Offer II 033. 变位词组

**问题**：

给定一个字符串数组 `strs` ，将 **变位词** 组合在一起。 可以按任意顺序返回结果列表。

**注意：**若两个字符串中每个字符出现的次数都相同，则称它们互为变位词

**思路**：

遍历字符串数组，然后将每个遍历到的字符串排序，插入 `map`，最后遍历 `map`，返回结果即可

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> hash = new HashMap<>();
        for(String str : strs){
            char[] chs = str.toCharArray();
            Arrays.sort(chs);
            String s = new String(chs);
            if(hash.containsKey(s)) hash.get(s).add(str);
            else{
                ArrayList<String> arr = new ArrayList<>();
                arr.add(str);
                hash.put(s, arr);
            }
        }
        
        return new ArrayList<>(hash.values());
    }
}
```

##### （2）剑指 Offer II 032. 有效的变位词

**问题**：

给定两个字符串 `s` 和 `t` ，编写一个函数来判断它们是不是一组变位词（字母异位词）。

**注意**：

- 若 `s` 和 `t` 中每个字符出现的**次数都相同**且**字符顺序不完全相同**，则称 `s` 和 `t` 互为变位词（字母异位词）

- `s` and `t` 仅包含小写字母

**思路**：

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length() || s.equals(t)) return false; // 特判
        int[] hash = new int[26];
        for(char ch : t.toCharArray()) hash[ch - 'a'] ++;
        for(char ch : s.toCharArray())
            if(-- hash[ch - 'a'] < 0) return false;
        return true;
    }
}
```

##### （3）剑指 Offer II 066. 单词之和

**问题**：

实现一个 `MapSum` 类，支持两个方法，`insert` 和 `sum`：

- `MapSum()` 初始化 `MapSum` 对象
- `void insert(String key, int val)` 插入 `key-val` 键值对，字符串表示键 `key` ，整数表示值 `val` 。如果键 `key` 已经存在，那么原来的键值对将被替代成新的键值对。
- `int sum(string prefix)` 返回所有以该前缀 `prefix` 开头的键 `key` 的值的总和。

**思路**：**哈希表**

- 哈希表存储`key-val` 键值对
- 枚举所以的键，统计以`prefix` 开头的键对应的值的总和

```java
class MapSum {

    Map<String, Integer> hash = new HashMap<>(); // 哈希表

    public MapSum() {}
    
    public void insert(String key, int val) {
        hash.put(key, val);
    }
    
    public int sum(String prefix) { // 返回所有以该前缀 prefix 开头的键 key 的值的总和
        Set<String> keyset = hash.keySet();
        int res = 0;
        for(String s : keyset)
            if(s.startsWith(prefix)) 
                res += hash.get(s); //s.startsWith(prefix) 判断s是否包含前缀prefix
        return res;
    }
}
```

##### （4）剑指 Offer II 064. 神奇的字典

**问题**：

设计一个使用单词列表进行初始化的数据结构，单词列表中的单词 **互不相同** 。如果给出一个单词，请判定能否只将这个单词中**一个**字母换成另一个字母，使得所形成的新单词存在于已构建的神奇字典中。

实现 `MagicDictionary` 类：

- `MagicDictionary()` 初始化对象
- `void buildDict(String[] dictionary)` 使用字符串数组 `dictionary` 设定该数据结构，`dictionary` 中的字符串互不相同
- `bool search(String searchWord)` 给定一个字符串 `searchWord` ，判定能否只将字符串中 **一个** 字母换成另一个字母，使得所形成的新字符串能够与字典中的任一字符串匹配。如果可以，返回 `true` ；否则，返回 `false` 。

**思路**：**哈希表**

- 哈希表的`key`存储的是单词的长度
- 哈希表的`value`是一个数组，数组内存储了长度为`key`的单词

- `public boolean search(String searchWord)`，根据`searchWord`单词的长度从哈希表中查找，是否存在单词与`searchWord`只有一个字符不同

```java
class MagicDictionary {
    // key是字符串的长度, value是长度为key的字符串集合
    Map<Integer, List<String>> hash = new HashMap<>(); 
    
    public void buildDict(String[] dictionary) { // 使用单词列表初始化哈希表
        for(int i = 0; i < dictionary.length; i ++){
            int len = dictionary[i].length();

            List<String> arr;
            if(!hash.containsKey(len)) arr = new ArrayList<>();
            else arr = hash.get(len);
            arr.add(dictionary[i]);
            
            hash.put(len, arr);
        }  
    }
    
    public boolean check(String a, String b){ // 判断两个字符串是否只有一个字符不同
        boolean flag = false; // 标记, true表示有一个字符不同
        for(int i = 0; i < a.length(); i ++)
            if(a.charAt(i) != b.charAt(i)){
                if(flag) return false;
                else flag = true;
            }
        return flag;
    }
    // 快速查询单词列表中是否有单词与searchWord只有一个字符不同
    public boolean search(String searchWord) {
        int len = searchWord.length();
        if(!hash.containsKey(len)) return false; // 特判

        List<String> arr = hash.get(len);
        for(int i = 0; i < arr.size(); i ++) // 所有相同长度的字符与searchWord比较
            if(check(arr.get(i), searchWord))
                return true;
        return false;
    }
}
```

##### （5）剑指 Offer II 034. 外星语言是否排序

**问题**：

某种外星语也使用英文小写字母，但可能顺序 `order` 不同。字母表的顺序（`order`）是一些小写字母的排列。

给定一组用外星语书写的单词 `words`，以及其字母表的顺序 `order`，只有当给定的单词在这种外星语中按字典序排列时，返回 `true`；否则，返回 `false`。

**示例**：

```python
输入：words = ["hello","leetcode"], order = "hlabcdefgijkmnopqrstuvwxyz"
输出：true
```

**思路**：**哈希表**

```python
class Solution:
    def __init__(self):
        self.mp = {} # 哈希表, 存储每个字符对应的字典序
    
    def check(self, a, b): # 判断在给定字母表的顺序时，a的字典序是否小于b
        for i in range(min(len(a), len(b))):
            if self.mp[a[i]] > self.mp[b[i]]:
                return False
            elif self.mp[a[i]] < self.mp[b[i]]:
                return True
        
        if len(a) <= len(b): 
            return True
        else:
            return False
    
    def isAlienSorted(self, words: List[str], order: str) -> bool:
        self.mp = {order[i] : i for i in range(len(order))} # 初始化哈希表

        i, j = 0, 1
        while j < len(words): # 枚举每一个单词, 判断其是否在在这种外星语中按字典序排列
            if self.check(words[i], words[j]):
                i, j = i + 1, j + 1
            else:
                return False
        return True

```



### 10、双指针

##### （1）1. 两数之和:writing_hand: 

**问题**：

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出和为目标值 `target`  的那两个整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。你可以按任意顺序返回答案。

**思路**：

**解法一**：

- 将所有元素**从小到大排序**
- **双指针**算法，设置两个指针，一个指针`i`指向数组头，另一个指针`j`指向数组尾
- 当`nums[i]+nums[j]<target`时，说明`nums[i]`小了，此时需要右移指针`i`，增大`nums[i]`
- 当 `nums[i]+nums[j]>target`时，说明`nums[j]`大了，此时需要左移指针`j`，减小`nums[j]`
- 直到当 `nums[i]+nums[j]==target`时，此时找到了第一组解
- 可以继续循环上述操作，找到其他的解

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Queue<Integer>> mp = new HashMap<>(); // 构建映射表, 存储每个数存现的下标位置
        for(int i = 0; i < nums.length; i ++) {
            if(mp.containsKey(nums[i])) mp.get(nums[i]).offer(i);
            else {
                Queue<Integer> q = new ArrayDeque<>();
                q.offer(i);
                mp.put(nums[i], q);
            }
        }

        Arrays.sort(nums); // 所有数从小到大排序
        for(int i = 0, j = nums.length - 1; i < j;){ // 双指针算法
            if(nums[i] + nums[j] == target){
                int[] res = {mp.get(nums[i]).poll(), mp.get(nums[j]).poll()};
                return res;
            }
            if(nums[i] + nums[j] < target) i ++;
            else if(nums[i] + nums[j] > target) j --;
        }
        return new int[0];
    }
}
```

**解法二**：

**哈希表**

```java
public int[] twoSum(int[] nums, int target) {
	HashMap<Integer,Integer> map = new HashMap<>(); // key + nums[value] == target
		for(int i = 0; i < nums.length; i++){
			if(map.containsKey(nums[i])){
				return new int[]{map.get(nums[i]), i};
		}
		map.put(target - nums[i], i);
	}
	return null;
}
```



##### （2）剑指 Offer II 007. 数组中和为 0 的三个数:writing_hand: 

**问题**：

给定一个包含`n`个整数的数组`nums`，判断`nums`中是否存在三个元素 `a`，`b`，`c`，使得 `a + b + c = 0`？ 请找出所有和为 0 且**不重复**的三元组

**思路**：

固定一个数，将其转换为**两个数之和**的问题

- 将所有数从小到大排序
- 枚举所有的`c`(即`a[k]`)，将`a+b+c=0`转换为`a+b=-c`
- 从`k`的右边位置，使用双指针算法枚举`a`和`b`
- `a`或`b`一定出现在`k`右边的位置; 如果`a`或`b`出现在`k`左边的位置并且满足`a+b+c=0`,则其一定被考虑过

```java
class Solution {
    public List<List<Integer>> get(int[] a) {
        Arrays.sort(a); // 从小到大排序
        List<List<Integer>> res = new ArrayList<>(); // 存储结果的集合
        
        // a+b+c=0枚举所有的c,即a[k]; a或b一定出现在k右边地位置, a或b出现在k左边的位置如果满足一定被考虑过
        for(int k = 0; k < a.length - 2; k ++){ 
            if(k != 0 && a[k] == a[k - 1]) continue; // a[k]在之前已经考虑过了, 就不在考虑
            
            for(int i = k + 1, j = a.length - 1; i < j;){ // 双指针算法,找出k右边所有满足a+b+c=0的解
                if(a[i] + a[j] == -a[k]){
                    Integer[] arr = new Integer[]{a[i],a[j],a[k]};
                    res.add(Arrays.asList(arr));
                    do i ++ ; while(i < j && a[i] == a[i - 1]); // a[i]在之前已经考虑过了, 就不在考虑
                    do j -- ; while(i < j && a[j] == a[j + 1]); // a[j]在之前已经考虑过了, 就不在考虑
                }
                    
                if(a[i] + a[j] < -a[k]) i ++;
                else if(a[i] + a[j] > -a[k]) j --;
            }
        }
        return res;
    }    
    
    public List<List<Integer>> threeSum(int[] nums) {
        return get(nums);
    }
}
```

##### （3）剑指 Offer II 018. 有效的回文

**问题**：

给定一个字符串 `s` ，验证 `s` 是否是 **回文串** ，只考虑字母和数字字符，可以忽略字母的大小写。

本题中，将空字符串定义为有效的 **回文串** 。

**思路**：

- 设置两个指针，一个指向字符串开始，一个指向字符串结尾，两个指针同步向中间靠拢，直到相遇
- 当遇到非字母或者非数字时，则跳过
- 比较两个指针所指的字符是否相同，若不相同，则不是回文串
- 如果两个指针所指的字符都相同，则是回文串

```java
class Solution {
    public boolean isPalindrome(String s) {
        s = s.toLowerCase(); // 将所有字符转换为小写
        for(int i = 0, j = s.length() - 1; i < j; i ++, j --){
            char a = s.charAt(i), b = s.charAt(j);
            while(i < j && (a < '0' || (a > '9' && a < 'A') || (a > 'Z' && a < 'a') || a > 'z'))//跳过其他字符
                a = s.charAt(++ i);
            while(i < j && (b < '0' || (b > '9' && b < 'A') || (b > 'Z' && b < 'a') || b > 'z'))//跳过其他字符 
                b = s.charAt(-- j);
            if(a != b) return false;
        }
        return true;
    }
}
```

##### （4）剑指 Offer II 019. 最多删除一个字符得到回文:writing_hand: 

**问题**：

给定一个非空字符串 `s`，请判断如果 **最多** 从字符串中删除一个字符能否得到一个回文字符串。

**注意**：

- `1 <= s.length <= 105`
- `s` 由小写英文字母组成

**思路**：**双指针**和**一次递归**

- 遇到一次不匹配的时候，跳过一下，左边跳过或者右边跳过
- 左边跳过或者右边跳过，有一个满足回文就是回文

```java
class Solution {
    // 判断s[l]~s[r]是否是回文字符串, flag为true表示已经删过一个字符, 不可再删
    public boolean dfs(String s, int l, int r, boolean flag){ // 双指针+一次递归
        while(l < r){
            if(s.charAt(l) != s.charAt(r)) {
                // s[l] != s[r] 删除左边的字符或者右边的字符
                if(!flag) return dfs(s, l + 1, r, true) || dfs(s, l, r - 1, true);  
                else return false; 
            }
            l ++; r --;
        }
        return true;
    }

    public boolean validPalindrome(String s) {
        return dfs(s, 0, s.length() - 1, false);
    }
}
```



### 11、枚举

##### （1）剑指 Offer II 079. 所有子集

**问题**：

给定一个整数数组 `nums` ，数组中的元素**互不相同** 。返回该数组所有可能的子集（幂集）。

解集**不能**包含重复的子集。你可以按**任意顺序**返回解集

**注意**：

- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有元素 **互不相同**

**思路**：**递归实现指数级枚举**

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    int st[]; // 记住每个位置当前的状态, 0表示还没有考虑, 1表示选它, 2表示不选它
    int a[]; // 整数数组
    int n;
    
    public void dfs(int u){
        if(u >= n){ // 找到一个子集
            List<Integer> arr = new ArrayList<>();
            for(int i = 0; i < n; i ++)
                if(st[i] == 1) arr.add(a[i]);
            res.add(arr);
            return ;
        }
        // 每个结点u都有2个分支
        st[u] = 2; dfs(u + 1); st[u] = 0; // 第一个分支, 不选u
        st[u] = 1; dfs(u + 1); st[u] = 0; // 第二个分支, 选u
    }

    public List<List<Integer>> subsets(int[] nums) {
        n = nums.length;
        st = new int[n];
        a = nums;

        dfs(0);
        return res;
    }
}
```

##### （2）剑指 Offer II 083. 没有重复元素集合的全排列

**问题**：给定一个不含重复数字的整数数组 `nums` ，返回其 **所有可能的全排列** 。可以 **按任意顺序** 返回答案

**注意**：

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有整数 **互不相同**

**思想**：**递归实现排列型枚举**

```java
class Solution {

    List<List<Integer>> res = new ArrayList<>();
    int[] a;
    int n;
    List<Integer> t = new ArrayList<>();

    public void dfs(int u, int state){ // 枚举每一个位置u, state表示数组a中每个数使用或未使用的状态
        if(u >= n){ // 找到一个解
            res.add(new ArrayList<>(t));
            return ;
        }

        for(int i = 0; i < n; i ++ ) // 每个位置有n中选择
            if((state >> i & 1) == 0){ // 找到第一个没有使用过的数
                t.add(a[i]); 
                dfs(u + 1, state | 1 << i);
                t.remove(t.size() - 1); // 恢复现场
            }
    }

    public List<List<Integer>> permute(int[] nums) {
        a = nums;
        n = a.length;
        dfs(0, 0);
        return res;
    }
}
```

##### （3）剑指 Offer II 084. 含有重复元素集合的全排列 

**问题**：给定一个可**包含重复数字**的整数集合 `nums` ，**按任意顺序** 返回它所有不重复的全排列

**注意**：

- `1 <= nums.length <= 8`
- `-10 <= nums[i] <= 10`

**思路**：**递归实现排列型枚举**，用`Set`集合去除重复元素

```java
class Solution {

    Set<List<Integer>> res = new HashSet<>(); // 用 set 去重
    List<Integer> t = new ArrayList<>();
    int[] a;
    int n;

    public void dfs(int u, int state){
        if(u >= n){
            res.add(new ArrayList<>(t));
            return ;
        }

        for(int i = 0; i < n; i ++)
            if((state >> i & 1) == 0){
                t.add(a[i]);
                dfs(u + 1, state | 1 << i);
                t.remove(t.size() - 1);
            }
    }

    public List<List<Integer>> permuteUnique(int[] nums) {
        a = nums;
        n = a.length;
        dfs(0, 0);
        return new ArrayList<>(res);
    }
}
```

##### （4）剑指 Offer II 080. 含有 k 个元素的组合

**问题**：给定两个整数 `n` 和 `k`，返回 `1 ... n` 中所有可能的 `k` 个数的组合

**思路**：**递归实现组合型枚举**

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> way;
    int n, m;
    
    public void dfs(int u, int start){
        if(u + n - start + 1 < m) return ; // 剪枝，已选的数和剩余可选的数的个数小于m
        if(u == m){
            res.add(new ArrayList<>(way)); // 找到一个解
            return ;
        }

        for(int i = start; i <= n; i ++){
            way.add(i);
            dfs(u + 1, i + 1);
            way.remove(way.size() - 1); // 恢复现场
        }
    }

    public List<List<Integer>> combine(int n, int k) {
        way = new ArrayList<>();
        this.n = n;
        this.m = k;

        dfs(0,1);
        return res;
    }
}
```



### 12、位运算

##### （1）136. 只出现一次的数字

**问题**：给定一个**非空**整数数组，除了某个元素只出现**一次**以外，其余每个元素均出现**两次**。找出那个只出现了一次的元素。

**变形的问题**：一个数组存放若干整数，**一个数**出现**奇数**次，**其余数**均出现**偶数**次，找出这个出现奇数次的数？

**思路**：数组中**所有元素**做**异或**运算，异或的结果即为只出现一次的元素

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0; // 记录按位异或的结果
        for(int val : nums) res ^= val;
        return res;
    }
}
```

##### （2）剑指 Offer 56 - I. 数组中数字出现的次数:writing_hand: 

**问题**：一个整型数组 `nums` 里除两个数字之外，其**他数字**都出现了**两次**。请写程序找出这**两个**只**出现一次**的**数字**。要求时间复杂度是$O(n)$，空间复杂度是$O(1)$。

**思路**：此题充分考察了**异或运算的性质**

- 将数组中的所有数异或，异或的结果等价于**两个**只**出现一次**的**数字(A,B)**相异或
- 用`lowbit` 运算求出上述异或结果的最后一位1以及之后的所有0
- 用上述的1进行分组，也就是做到将A和B区分开，求出两个数中的其中一个
- 利用异或运算的**自反性**，求出另一个数

[题解](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/comments/) 

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int x = 0; // A^B^C^C^D^D^...^Z^Z = x x表示只出现了一次的数的异或结果
        for(int val : nums) x ^= val; // 运用了(a^b) == (b^a), (a^b)^c == a^(b^c), x^x=0, x^0=x
        int t = x & -x; // t = lowbit(x), t表示x的最后一位1以及之后的所有0
        
        int res = 0; // 表示A或B其中的一个数, 只出现了一次的一个数
        for(int val : nums)
            if((val & t) != 0) res ^= val;
        return new int[]{res, x ^ res};
    }
}
```



##### （3）剑指 Offer II 004. 只出现一次的数字 :writing_hand: 

**问题**：给你一个整数数组 `nums` ，除某个元素仅出现 **一次** 外，其余每个元素都恰出现 **三次 。**请你找出并返回那个只出现了一次的元素。

**思路**：将整数的各个数位上的加起来，然后对`3`取余，若结果为`0`，则待求数字在该位上是`0`；若结果为`1`，则待求数字在该位上是`1`

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0;
        for(int i = 0; i < 32; i ++){
            int t = 0; 
            for(int num : nums) t += num >> i & 1; // 统计nums数组中所有数字二进制表示第i位之和
            res |= t % 3 << i; // t mod 3 剩下的就是出现一次的数字二进制表示的第i位,然后按位放到返回值中
        }
        return res;
    }
}
```

##### （4）面试题 17.04. 消失的数字

**问题**：数组`nums`包含从`0`到`n`的所有整数，但其中**缺了一个**。请编写代码找出那个缺失的整数。你有办法在$O(n)$时间内完成吗？

**变形的问题**：数组`nums`包含从`0`到`n`的所有整数，**只有唯一的一个元素值重复**，**其它均只出现一次**。请编写代码找出那个重复的整数。你有办法在$O(n)$时间内完成吗？

**思路**：**异或运算的自反性**

```java
class Solution {
    public int missingNumber(int[] nums) {
        int a = 0, b = 0;
        for(int val : nums) a ^= val; // 数组中所有元素的异或结果
        for(int val = 0; val <= nums.length; val ++) b ^= val; // 0~n所有元素的异或结果
        return a ^ b; // 异或运算的自反性
    }
}
```

##### （5）剑指 Offer II 003. 前 n 个数字二进制中 1 的个数

**问题**：给定一个非负整数 `n` ，请计算 `0` 到 `n` 之间的每个数字的二进制表示中 1 的个数，并输出一个数组。

**思路**：使用`lowbit(x)`运算 或者 `x >> k & i`

```java
class Solution {
    public int[] countBits(int n) {
        int[] arr = new int[n + 1];
        for(int val = 0; val <= n; val ++){
            int res = 0, t = val;
            while(t != 0){
                res ++ ;
                t ^= t & -t; // 将最后一位1变成0
            }
            arr[val] = res;
        }
        return arr;
    }
}
```

##### （6）剑指 Offer II 067. 最大的异或:writing_hand: 

**问题**：给定一个整数数组 `nums` ，返回 `nums[i] XOR nums[j]` 的最大运算结果，其中 `0 ≤ i ≤ j < n` 

**思路**：**位运算**和**字典树**

假设在数组中选择了元素 $a_i$ 和 $a_{j}(i \neq j)$，使得它们达到最大的按位异或运算结果 $x$：

$$
x=a_{i} \oplus a_{j}
$$
其中 $\oplus$ 表示按位异或运算。要想求出 $x$，一种简单的方法是使用**二重循环枚举** $i$ 和 $j$，但这样做的时间复杂度为 $O(n^2)$，**会超出时间限制**。因此，需要寻求时间复杂度更低的做法。

根据按位**异或运算的性质**，$x=a_{i} \oplus a_{j}$ 等价于 $a_j = x \oplus a_i $ 。可以根据这一变换，**设计一种「从高位到低位依次确定 $x$ 二进制表示的每一位」的方法**，以此得到 $x$ 的值。该方法的精髓在于：

- 由于数组中的元素都在 $[0, 2^{31})$ 的范围内，那么可以将每一个数表示为一个长度为 **31 位**的二进制数（如果不满 $31$ 位，在最高位之前补上若干个前导 $0$ 即可）
- 这 31 个二进制位从低位到高位依次编号为 $0,1,⋯,30$。从最高位第 30 个二进制位开始，依次确定 $x$ 的每一位是 $0$ 还是 $1$
- 由于需要找出最大的 $x$，因此在枚举每一位时，先判断 $x$ 的这一位是否能取到 $1$。如果能，我们取这一位为 $1$，否则我们取这一位为 $0$。

「判断 $x$ 的某一位是否能取到 $1$」这一步骤并不容易。



可以将**数组中的元素**看成**长度为 31 的字符串**，字符串中只包含 **0** 和 **1**。如果将字符串放入**字典树**中，那么在字典树中查询一个字符串的过程，恰好就是从高位开始确定每一个二进制位的过程。

根据 $x = a_i \oplus a_j$ ，我们枚举 $a_i$ ，并将 $a_0, a_1, \cdots, a_{i-1}$ 作为 $a_j$ 放入字典树中，希望找出使得 $x$ 达到最大值的 $a_j$ 。如何求出 $x$ 呢？我们可以从字典树的根节点开始进行遍历，遍历的「参照对象」为 $a_i$。在遍历的过程中，我们根据 $a_i$的第 $x$ 个二进制位是 $0$ 还是 $1$，确定我们应当走向哪个子节点以继续遍历。假设我们当前遍历到了第 $k$ 个二进制位：

- 如果 $a_i$ 的第 $k$ 个二进制位为 $0$，那么我们应当往表示 $1$ 的子节点走，这样 $0 \oplus 1 = 1$，可以使得 $x$ 的第 $k$ 个二进制位为 1。如果不存在表示 $1$ 的子节点，那么我们只能往表示 $0$ 的子节点走，$x$ 的第 $k$ 个二进制位为 $0$；

- 如果 $a_i$ 的第 $k$ 个二进制位为 $1$，那么我们应当往表示 $0$ 的子节点走，这样 $1 \oplus 0 = 1$，可以使得 $x$ 的第 $k$ 个二进制位为 $1$。如果不存在表示 $0$ 的子节点，那么我们只能往表示 1 的子节点走，$x$ 的第 $k$ 个二进制位为 $0$。


当遍历完所有的 $31$ 个二进制位后，我们也就得到了 $a_i$

可以通过异或运算得到的最大 $x$。这样一来，如果我们枚举了所有的 $a_i$ ，也就得到了最终的答案。

```java
class Solution {
    int[][] son;
    int idx, n;

    public void insert(int val){ // val可以看成长度为32的字符串, 将val的二进制表示,从高位到低位插入字典树
        int p = 0;
        for(int i = 30; i >= 0; i --){ // 0 <= nums[i] <= 2^31 - 1, nums[i]最多31位, 从高位向低位存储
            int u = val >> i & 1;
            if(son[p][u] == 0) son[p][u] = ++ idx;
            p = son[p][u];
        }
    }

    public int query(int val){ // 返回nums数组中与val异或最大的数, res 的每一位能取1，则尽可能取1
        int p = 0, res = 0;
        for(int i = 30; i >= 0; i --){
            int u = val >> i & 1;
            if(son[p][u ^ 1] != 0) { p = son[p][u ^ 1]; res |= (u ^ 1) << i; }
            else if(son[p][u] != 0) { p = son[p][u]; res |= u << i; }
            else return -1;
        }
        return res;
    }

    public int findMaximumXOR(int[] nums) {
        int n = nums.length;
        // 此处的Trie树是一颗典型的二叉树, 32*N是树中的结点数, 第二维表示每个结点有两个孩子,左孩子0,右孩子1
        son = new int[31 * n + 1][2]; 

        for(int val : nums) insert(val);
        
        int res = 0;
        for(int i = 0; i < nums.length; i ++){
            int t = query(nums[i]);
            if(t != -1) res = Math.max(res, t ^ nums[i]);
        }
        return res;
    }
}
```

##### （7）剑指 Offer II 005. 单词长度的最大乘积:writing_hand: 

**问题**：给定一个字符串数组 `words`，请计算当两个字符串 `words[i]` 和 `words[j]` 不包含相同字符时，它们**长度的乘积的最大值**。假设字符串中只包含英语的**小写字母**。如果没有不包含相同字符的一对字符串，返回 `0`。

此题与上题相似，但是解法完全不同

**思路**：暴力算法的时间复杂度是 $O(n^3)$，采用二进制数**按位与**来**优化**两个字符串字符的比较，时间复杂度降为 $O(n^2)$

- 用一个`26`位的**二进制数表示每一个字符串**，二进制数的第 $k$(从0开始，从右往左数) 为1表示，该字符串包含 `'a' + k` 这个字符
- 枚举所有的二进制数对，当两个二进制数按位与的结果为 0，则表示这个两个字符串不包含相同的字符，记录其长度的乘积
- 求出所有长度乘积的最大值

```java
class Solution {
    public int maxProduct(String[] words) {
        // 使用二进制位运算可以将暴力算法O(n^3)的时间复杂度优化到O(n^2)
        int[] a = new int[words.length]; // a[i]的二进制表示中第k位为1, words[i]中存在'a'+ k
        for(int i = 0; i < words.length; i ++)
            for(int j = 0; j < words[i].length(); j ++)
                a[i] |= 1 << (words[i].charAt(j) - 'a');

        int res = 0;
        for(int i = 0; i < a.length; i ++)
            for(int j = 0; j < i; j ++)
                if((a[i] & a[j]) == 0) // 字符串 words[i] 和 words[j] 不包含相同字符
                    res = Math.max(res, words[i].length() * words[j].length());
        return res;
    }
}
```

##### （8）剑指 Offer II 001. 整数除法:writing_hand: 

**问题**：给定两个整数 `a` 和 `b` ，求它们的除法的商 `a/b` ，要求不得使用乘号 `'*'`、除号 `'/'` 以及求余符号 `'%'` 。

- 整数除法的结果应当截去（`truncate`）其小数部分，例如：`truncate(8.345) = 8` 以及 `truncate(-2.7335) = -2`
- 假设我们的环境只能存储 32 位有符号整数，其数值范围是 `[−231, 231−1]`。本题中，如果除法结果溢出，则返回 `231 − 1`

**思路**：64 / 3 = ？

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202212043790.png" alt="image-20220221204327690" style="zoom:67%;" />

```java
class Solution {
    public int divide(int a, int b) {
        // 特判, 当 a = Integer.MIN_VALUE 或 b = Integer.MIN_VALUE时, 防止a或b转正数时溢出
        int res = 0;
        if(a == Integer.MIN_VALUE){
            if(b == 1) return Integer.MIN_VALUE;
            if(b == -1) return Integer.MAX_VALUE; // 防止结果溢出
            if(a == b) return 1;
            a -= -Math.abs(b);  // 否则，a先减去一个b，防止a转正数溢出
            res += 1;
        }
        if(b == Integer.MIN_VALUE) return 0;

        int symbol = (a > 0) ^ (b > 0) ? -1 : 1; // 判断符号 同号为false取1, 异号为true取-1
        a = Math.abs(a); b = Math.abs(b); // 转为正数

        for(int i = 31; i >= 0; i --){
            if((a >> i) >= b) {  // a/(2^i) >= b，即 a >= b*(2^i)，这里不直接对 b 操作，防止溢出
                res += 1 << i;
                a -= b << i;
            }
        }

        return symbol == -1 ? -res : res; // 加上符号
    }
}
```



### 13、堆（优先队列）

##### （1）剑指 Offer II 076. 数组中的第 k 大的数字

**问题**：

给定整数**数组** `nums` 和整数 `k`，请返回数组中**第 `k` 个最大的元素**。

请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

**思路**：**大根堆**

```java
class Solution {
    // 大根堆
    PriorityQueue<Integer> heap = new PriorityQueue<>((i1,i2)->Integer.compare(i2,i1));

    public int findKthLargest(int[] nums, int k) {
        for(int val : nums) heap.offer(val);
        for(int i = 1; i < k; i ++) heap.poll(); // 前k-1个最大元素弹出堆
        return heap.peek(); 
    }
}
```

##### （2）剑指 Offer II 059. 数据流的第 K 大数值

**问题**：

设计一个找到**数据流**中第 `k` 大元素的类（class）。注意是排序后的**第 `k` 大元素**，不是第 `k` 个不同的元素。

请实现 `KthLargest` 类：

- `KthLargest(int k, int[] nums)` 使用整数 `k` 和整数流 `nums` 初始化对象。
- `int add(int val)` 将 `val` 插入数据流 `nums` 后，返回当前数据流中第 `k` 大的元素。

**注意**：题目数据保证，在查找第 `k` 大元素时，数组中至少有 `k` 个元素

**思路**：此问题属于 `topK` 问题，是一类典型的优先队列问题

```java
class KthLargest {
    // 使得 sh.peek() >= bh.peek()
    Queue<Integer> bh = new PriorityQueue<>((i1,i2)->i2-i1); // 大根堆, 维护其他的剩余的元素
    Queue<Integer> sh = new PriorityQueue<>(); // 小根堆, 维护前k的最大元素,堆顶元素就是第k的大元素
    int k;

    public void insert(int val){
        if(sh.size() < k) sh.offer(val); // sh维护k个最大元素
        else {
            sh.offer(val); //  // 使得 sh.peek() >= bh.peek()
            bh.offer(sh.poll());
        }
    }

    public KthLargest(int k, int[] nums) {
        this.k = k;
        for(int val : nums) insert(val);
    }
    
    public int add(int val) {
        insert(val);
        return sh.peek();
    }
}
```

##### （3）剑指 Offer II 060. 出现频率最高的 k 个数字

**问题**：给定一个整数数组 `nums` 和一个整数 `k` ，请返回其中出现频率前 `k` 高的元素。可以按 **任意顺序** 返回答案。

**思路**：用**大根堆**维护出现次数的 `k` 个数

```java
class Pair{
    int num, count;
    Pair(int num) {this.num = num; this.count = 0;}
}

class Solution {
    Map<Integer, Pair> hash = new HashMap<>();
    Queue<Pair> heap = new PriorityQueue<>((p1, p2) -> p2.count - p1.count);

    public int[] topKFrequent(int[] nums, int k) {
        for(int val : nums){
           Pair p = hash.getOrDefault(val, new Pair(val));
           p.count ++ ;
           hash.put(val, p);
        }

        Set<Integer> keySet = hash.keySet();
        for(Integer key : keySet)
            heap.offer(hash.get(key));

        int[] res = new int[k];
        int idx = 0;
        while(idx < k) res[idx ++] = heap.poll().num;

        return res;
    }
}
```

### 14、并查集

##### （1）剑指 Offer II 118. 多余的边

**问题**：

树可以看成是一个**连通**且**无环**的**无向图**。

给定往一棵 `n` 个节点 (节点值 `1～n`) 的树中添加一条边后的图。添加的边的两个顶点包含在 `1` 到 `n` 中间，且这条附加的边不属于树中已存在的边。图的信息记录于长度为 `n` 的二维数组 `edges` ，$edges[i] = [a_i, b_i]$ 表示图中在 $a_i$ 和 $b_i$ 之间存在一条边。

请**找出一条可以删去的边**，删除后可使得剩余部分是一个有着 `n` 个节点的树。如果有多个答案，则返回数组 `edges` 中最后出现的边。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202171853802.png" alt="img" width="300px" />

```
输入：edges = [[1,2],[2,3],[3,4],[1,4],[1,5]]
输出：[1,4]
```

**注意**：数据保证无自环，无重边

- `ai != bi`
- `edges` 中无重复元素
- 给定的图是连通的

**思路**：**并查集**合并集合

- 假设所有的结点都不互相连通

- 从前往后依次遍历所有的边，合并此边的两个点
- 直到第一次遇到边的两个节点已经连通，则此边即为可以删去的边

```java
class Solution {
    int n;
    int[] p; // p[i]存储i的父结点

    public int find(int x){ // 求x的祖宗结点, 路径压缩
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public int[] findRedundantConnection(int[][] edges) {
        n = edges.length;
        p = new int[n + 10];

        for(int i = 1; i <= n; i ++) p[i] = i; // 初始化 p 数组

        int[] res = new int[2];
        for(int i = 0; i < n; i ++){
            int a = edges[i][0], b = edges[i][1];
            int x = find(a), y = find(b); // x, y 分别是 a, b 的祖宗结点
            
            if(x != y) p[x] = y; // a, b不是一个集合, 则将a和b合并
            else{ // 找到一组解
                res[0] = a; res[1] = b;
                break;
            }
        }
        return res;
    }   
}
```

##### （2）剑指 Offer II 116. 省份数量

**问题**：

有 `n` 个城市，其中一些彼此相连，另一些没有相连。如果城市 `a` 与城市 `b` 直接相连，且城市 `b` 与城市 `c` 直接相连，那么城市 `a` 与城市 `c` 间接相连。

省份 是一组直接或间接相连的城市，组内不含其他没有相连的城市。

给你一个 `n x n` 的矩阵 `isConnected` ，其中 `isConnected[i][j] = 1` 表示第 `i` 个城市和第 `j` 个城市直接相连，而 `isConnected[i][j] = 0` 表示二者不直接相连。

返回矩阵中 **省份 的数量**

**注意**：此图中含有自环和重边

- `isConnected[i][i] == 1`
- `isConnected[i][j] == isConnected[j][i]`

**思路**：给定**非连通图**的**邻接矩阵**，使用**并查集**求图中**连通块的数量**

- 假设所有的结点都不互相连通，其个数为 `res`
- 从前往后依次遍历所有的边，如果边的两个点不连通，则合并此边的两个点，即 `res--`
- 最后`res`即为连通块的数量

```java
class Solution {
    int n, res;
    int[] p; // p[i]存储i的父结点
 
    public int find(int x){ // 求x的祖宗结点, 路径压缩
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public int findCircleNum(int[][] isConnected) {
        res = n = isConnected.length;
        p = new int[n + 10];

        for(int i = 0; i < n; i ++) p[i] = i; // 初始化 p 数组

        for(int i = 0; i < n; i ++)
            for(int j = 0; j < i; j ++) // 只遍历一边,去除自环和重边
                if(isConnected[i][j] == 1){
                    int x = find(i), y = find(j);
                    if(x != y){ // 合并i, j
                        p[x] = y;
                        res --; // 计算连通块的个数
                    }
                }

        return res;   
    }
}
```

### 15、模拟

##### （1）剑指 Offer II 002. 二进制加法

**问题**：

给定两个 01 字符串 `a` 和 `b` ，请计算它们的和，并以二进制字符串的形式输出。

输入为 **非空** 字符串且只包含数字 `1` 和 `0`。

**思路**：

```java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder res = new StringBuilder();
        StringBuilder x = new StringBuilder(a), y = new StringBuilder(b);
        x.reverse(); y.reverse();

        int t = 0; // 进位
        for(int i = 0; i < x.length() || i < y.length(); i ++){
            if(i < x.length()) t += x.charAt(i) - '0';
            if(i < y.length()) t += y.charAt(i) - '0';

            res.append(t % 2);
            t /= 2;
        }

        if(t == 1) res.append(t); // 还有一位进位
        return res.reverse().toString();
    }
}
```

### 16、字典树

##### （1）剑指 Offer II 062. 实现前缀树

**问题**：Trie（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如**自动补完**和**拼写检查**

```java
class Trie {

    final int N = 40010; // insert、search 和 startsWith 调用次数总计不超过 30000 次
    int[][] son = new int[N][26]; //多叉树, 字符串仅有仅由小写英文字母组成
    int[] cnt = new int[N]; // 记录每个单词出现的次数, 其标记作用
    int idx = 0; // 下标是0既是更结点也是空结点

    public Trie() {}
    
    public void insert(String word) { // 向前缀树中插入字符串 word
        char[] str = word.toCharArray();
        int p = 0; // 表示根结点，从根结点开始创建
        for(int i = 0; i < str.length; i ++){
            int u = str[i] - 'a'; // 将a-z映射到下标0-25
            if(son[p][u] == 0) son[p][u] = ++ idx; // p不存在u这个孩子，则创建这个结点
            p = son[p][u]; // p 指向孩子结点
        }
        cnt[p] ++ ; // 该单词的数量加一
    }
    
    public boolean search(String word) { // 如果字符串 word 在前缀树中，返回 true；否则，返回 false 
        char[] str = word.toCharArray();
        int p = 0; // 表示根结点，从根结点开始查询
        for(int i = 0; i < str.length; i ++){
            int u = str[i] - 'a'; // 将a-z映射到下标0-25
            if(son[p][u] == 0) return false;
            p = son[p][u];
        }
        if(cnt[p] == 0) return false;
        return true;
    }
    
    public boolean startsWith(String prefix) { // 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false
        char[] str = prefix.toCharArray();
        int p = 0;
        for(int i = 0; i < str.length; i ++){
            int u = str[i] - 'a';
            if(son[p][u] == 0) return false;
            p = son[p][u];
        }
        return true;
    }
}
```

##### （2）剑指 Offer II 063. 替换单词

**问题**：

在英语中，有一个叫做 `词根(root)` 的概念，它可以跟着其他一些词组成另一个较长的单词——我们称这个词为 `继承词(successor)`。例如，词根`an`，跟随着单词 `other`(其他)，可以形成新的单词 `another`(另一个)。

现在，给定一个由许多词根组成的词典和一个句子，需要将句子中的所有`继承词`用`词根`替换掉。如果`继承词`有许多可以形成它的`词根`，则用最短的词根替换它。

需要输出替换之后的句子。

**注意**：

- `1 <= dictionary.length <= 1000`
- `1 <= dictionary[i].length <= 100`
- `dictionary[i]` **仅由小写字母组成**

- `sentence` 仅由小写字母和空格组成

**思路**：**前缀树**

- 利用前缀和组织所有的词根
- `insert(char[] strs)`操作负责向前缀树中添加词根
- `query(char[] strs)` 从前缀树中查找与`strs`相匹配的**最短**的词根，并返回词根；如果没有找到，则返回`strs`本身

```java
class Solution {

    final int N = 100010;
    int[][] son = new int[N][26]; // 前缀树
    int[] cnt = new int[N];
    int idx = 0; // cnt 标记一个词根, idx表示当前用到第几个结点

    public void insert(char[] strs){ // 将一个词根插入前缀树中
        int p = 0; // 根结点
        for(int i = 0; i < strs.length; i ++){
            int u = strs[i] - 'a'; // 将a-z 映射到 0-25
            if(son[p][u] == 0) son[p][u] = ++ idx;
            p = son[p][u];
        }
        cnt[p] ++ ;
    }

    public String query(char[] strs){ // 查询一个继承词，返回其最短的词根
        int len = 0;
        
        int p = 0; // 根结点
        for(int i = 0; i < strs.length; i ++){
            int u = strs[i] - 'a';
            if(son[p][u] == 0) return new String(strs); // 没有找到相应的词根, 则返回原继承词
            p = son[p][u];
            
            len ++;
            if(cnt[p] > 0) break;
        }
        return new String(strs, 0, len); // 返回最短的词根
    }

    public String replaceWords(List<String> dictionary, String sentence) {
        for(int i = 0; i < dictionary.size(); i ++) // 根据词典构建前缀树
            insert(dictionary.get(i).toCharArray());

        String[] word = sentence.split(" ");
        for(int i = 0; i < word.length; i ++)
            word[i] = query(word[i].toCharArray()); // 将句子中的所有继承词用词根替换掉

        StringBuilder res = new StringBuilder(word[0]); // 字符串拼接
        for(int i = 1; i < word.length; i ++)
            res.append(" ").append(word[i]);
        return res.toString();
    }
}
```

##### （3）剑指 Offer II 065. 最短的单词编码

**问题**：

单词数组 `words` 的 **有效编码** 由任意助记字符串 `s` 和下标数组 `indices` 组成，且满足：

- `words.length == indices.length`
- 助记字符串 `s` 以 `'#'` 字符结尾
- 对于每个下标 `indices[i]` ，`s` 的一个从 `indices[i]` 开始、到下一个 `'#'` 字符结束（但不包括 `'#'`）的 子字符串 恰好与 `words[i]` 相等

给定一个单词数组 `words` ，返回成功对 `words` 进行编码的最小助记字符串 `s` 的长度 。

**示例**：

```java
输入：words = ["time", "me", "bell"]
输出：10
解释：一组有效编码为 s = "time#bell#" 和 indices = [0, 2, 5] 。
```

**思路**：**前缀树**

- 将每个**单词逆序遍历**并存储在前缀树中
- 前缀树存储的是**最长的前缀**，即从根结点到叶子结点的路径中，**只有叶子结点有标记信息**，其余结点的标记信息均被消除
- 遍历前缀树(多叉树)，求出前缀树维护的**所有前缀的字符个数**，在**加上**所维护的**前缀数量**，即为最小助记字符串 `s` 的长度 

```java
class Solution {

    final int N = 14010;
    int[][] son = new int[N][26];
    int[] cnt = new int[N]; // cnt[i]表示以i结尾的单词的长度, 同时起标记作用
    int idx = 0;

    public void insert(char[] strs){ // 插入字符串strs,并沿途消除标记; 如果strs是子串,不用添加标记
        int p = 0;
        for(int i = strs.length - 1; i >= 0; i --){
            int u = strs[i] - 'a';
            if(son[p][u] == 0) son[p][u] = ++ idx;
            p = son[p][u];

            if(cnt[p] > 0) cnt[p] = 0; // 遍历的过程中, 消除之前添加的标记
        }
        
        for(int i = 0; i < 26; i ++)
            if(son[p][i] != 0) return ; // 如果strs是子串,不用添加标记
        cnt[p] = strs.length; // 如果strs不是子串, 添加新的标记
    }

    public int query(int u){ //多叉树遍历, 求对 words 进行编码的最小助记字符串的长度
        if(cnt[u] > 0) return cnt[u] + 1; // 字符串长度+结尾符#长度1

        int res = 0;
        for(int i = 0; i < 26; i ++) // 遍历u的所有子节点
            if(son[u][i] != 0) res += query(son[u][i]);
        return res;
    }

    public int minimumLengthEncoding(String[] words) {
        for(int i = 0; i < words.length; i ++)
            insert(words[i].toCharArray());
        return query(0); // 字典树的根节点编号为 0 
    }
}
```



### 17、单调栈

##### （1）剑指 Offer II 038. 每日温度

**问题**：请根据每日 `气温` 列表 `temperatures` ，重新生成一个列表，要求其对应位置的输出为：**要想观测到更高的气温，至少需要等待的天数**。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

**思路**：**单调递减栈**

- 单调递减栈**存储的是下标**
- 从右往左遍历，求最近且比当前元素大的数与当前元素位置的差
- 如果不存在，该位置用 `0` 来代替

```java
class Solution {

    int[] stk; // 单调递减栈, 栈顶元素为从右往左遍历第一个比当前元素大的元素
    int tt; // 栈顶指针

    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        stk = new int[n + 1];
        tt = 0;

        int[] res = new int[n];
        for(int i = n - 1; i >= 0; i --){
            // 栈顶元素出栈, 维护栈的单减性
            while(tt != 0 &&  temperatures[stk[tt]] <= temperatures[i]) tt --; 
            if(tt != 0) res[i] = stk[tt] - i; // 求最近且比当前元素大的数与当前元素位置的差
            else res[i] = 0; // 如果不存在，该位置用 0 来代替
            stk[++ tt] =  i;
        }
        return res;
    }
}
```

##### （2）剑指 Offer II 039. 直方图最大矩形面积:writing_hand: 

**（经典的单调栈问题）**

**问题**：给定非负整数数组 `heights` ，数组中的数字用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 `1` 。求在该柱状图中，能够勾勒出来的矩形的最大面积。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202262302053.jpeg" alt="img" width="360px" />

**思路**：**单调递增栈**

- 单调递增栈**存储是下标**

- 维护单调递增栈，**找每个元素左边和右边第一个比它小的边界**，或者说记录 「以第 `i` 根柱子为最矮柱子时，所能延伸的最大面积」

**例如**：

- 对于任意一个位置，想要找到以该位置为高，可以向左和向右找到的可以扩张的最大宽度
- 如图，`index = 2`时，`height = h[2] = 4`，向左找到第一个低于`h[2]`的位置，`left = 0`，向右找到第一个低于`h[2]`的位置，`right = 4`，所以，`width = right - left - 1 = 3`
- 综上，以`h[2]`为高的最大的矩形的面积是`height * width = 12`
- 同理，找到以每个位置为高可以得到最大矩形，进行比较，取最大值

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202262306275.png" alt="image-20220226230629171" width="350px" />

```java
class Solution {
    int[] stk; // 单调递增栈
    int tt = 0; // 栈顶指针

    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        stk = new int[n + 3];

        int[] h = new int[n + 2]; 
        h[0] = 0; h[n + 1] = 0; // 在heights最左边和最右边分别增加两个元素0作为边界
        for(int i = 0; i < n; i ++) h[i + 1] = heights[i];

        int res = 0;
        for(int i = 0; i < h.length; i ++){
            while(tt != 0 && h[stk[tt]] > h[i]) { // 栈顶元素比当前元素大时
                int t = stk[tt --]; // 维护栈的单增性, 栈顶元素出栈
                // 此时, 栈顶元素和h[i]分别为h[t]左边最近和右边最近比h[t]小的元素
                res = Math.max(res, h[t] * (i - stk[tt] - 1)); // 求h[t]所能延伸的最大面积
            }
            stk[++ tt] = i;
        }
        return res;
    }
}
```

##### （3）2016. 增量元素之间的最大差值

**问题**：

给你一个下标从 **0** 开始的整数数组 `nums` ，该数组的大小为 `n` ，请你计算 `nums[j] - nums[i]` 能求得的 **最大差值**，其中 `0 <= i < j < n` 且 `nums[i] < nums[j]` 。

返回 **最大差值** 。如果不存在满足要求的 `i` 和 `j` ，返回 `-1` 。

**思路**：**单调递增栈**

- 单调递增栈**存储的是下标**
- 求出对于每个`j`，`nums[j] - nums[i]`的最大差值 (满足`nums[i] < nums[j]`,`0 <= i < j < n` )
- 单调递增栈的**栈顶元素**维护的是`j`，那么**栈底元素**就是满足条件的`i` 
- 求出所有差值的最大值

```java
class Solution {

    int[] stk; // 单调递增栈
    int tt = 0; // 栈顶指针

    public int maximumDifference(int[] nums) {
        int n = nums.length;
        stk = new int[n + 2];

        int[] a = new int[n + 1]; 
        a[n] = 0; // 为nums数组增加右边界
        for(int i = 0; i < n; i ++) a[i] = nums[i];

        int res = -1;
        for(int i = 0; i < a.length; i ++){
            while(tt != 0 && a[stk[tt]] > a[i]){ // 当栈顶元素大于当前元素
                int t = stk[tt --]; // 维护栈的单增性, 栈顶元素出栈
                if(tt != 0 && a[t] > a[stk[1]]) // 栈不为空且栈底元素小于栈顶元素
                    res = Math.max(res, a[t] - a[stk[1]]); 
                    // nums[t] - nums[stk[1]]即为 nums[t] - nums[k]的最大差值(0 <= k < t < n)
            }
            stk[++ tt] = i;
        }
        return res;
    }
}
```

### 18、栈



### 19、队列

##### （1）剑指 Offer II 042. 最近请求次数

**问题**：

写一个 `RecentCounter` 类来计算特定时间范围内最近的请求。

请实现 `RecentCounter` 类：

- `RecentCounter()` 初始化计数器，请求数为 0 。
- `int ping(int t)` 在时间 $t$ 添加一个新请求，其中 $t$ 表示以毫秒为单位的某个时间，并返回过去 $3000$ 毫秒内发生的所有请求数（包括新请求）。确切地说，返回在 $[t-3000, t]$ 内发生的请求数。

**保证** 每次对 `ping` 的调用都使用比之前更大的 `t` 值。

**思路**：

- 用一个队列维护在$[t-3000, t]$内发生的请求，队列的大小即为**在 $[t-3000, t]$ 内发生的请求数**
- 每次向队列中添加一个新的请求(从队尾入队)，同时将不在$[t-3000, t]$内发生的所有请求从队列剔除(从队头出队)

```java
class RecentCounter {
    Queue<Integer> q = new ArrayDeque<>(); // 队列, 维护过去3000 毫秒内发生的所有请求

    public RecentCounter() {}

    public int ping(int t) {
        q.offer(t);
        while(q.peek() < t - 3000) q.poll(); // 将不在[t-3000, t]内发生的所有请求数从队列剔除
        return q.size(); // 队列的大小就是在 [t-3000, t] 内发生的请求数
    }
}
```



### 20、拓扑排序

##### （1）剑指 Offer II 113. 课程顺序

**问题**：

现在总共有 `numCourses` 门课需要选，记为 `0` 到 `numCourses-1`。

给定一个数组 `prerequisites` ，它的每一个元素 `prerequisites[i]` 表示两门课程之间的先修顺序。 例如 `prerequisites[i] = [ai, bi]` 表示想要学习课程 `ai` ，需要先完成课程 `bi` 。

请根据给出的总课程数  `numCourses` 和表示先修顺序的 `prerequisites` 得出一个可行的修课序列。

可能会有多个正确的顺序，只要任意返回一种就可以了。如果不可能完成所有课程，返回一个空数组。

**注意**：**无重边和自环**

- `ai != bi`
- `prerequisites` 中不存在重复元素

**思路**：**拓扑排序**求**拓扑序列**

```java
class Solution {

    int n;
    int[] h, e, ne; // 邻接表,h表示头指针数组
    int idx;
    int[] d, q; // d[i]表示编号为i的结点的入度; q 表示队列

    public void add(int a, int b){ // 增加一条边a->b
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++;
    }

    public boolean topsort(){ // 拓扑排序
        int hh = 0, tt = -1; // 队列初始化
        for(int i = 0; i < n; i ++) // 所有入度为0的结点入队列
            if(d[i] == 0) q[++ tt] = i;

        while(hh <= tt){
            int t = q[hh ++]; // 队头元素出队列
            for(int i = h[t]; i != -1; i = ne[i]){ // 枚举所有t的出边
                int j = e[i];
                d[j] --; // 删除i->j的边
                if(d[j] == 0) q[++ tt] = j; // j之前的所有结点都放好了
            }
        }
        return tt == n - 1 ? true : false; // tt==n-1说明有拓扑序列;否则存在环,无图拓扑序列
    }

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        n = numCourses; // 初始化数据结构
        h = new int[n]; e = new int[n * n]; ne = new int[n * n];
        idx = 0;
        d = new int[n]; q = new int[n];

        Arrays.fill(h, -1); // 初始化头指针数组

        for(int i = 0; i < prerequisites.length; i ++){
            int a = prerequisites[i][1], b = prerequisites[i][0];
            add(a, b); // 增加一条a->b的边
            d[b] ++ ; // b的入队加一
        }

        if(topsort()) return q;
        else return new int[0];
    }
}
```

##### （2）剑指 Offer II 115. 重建序列:writing_hand: 

**问题**：

请判断原始的序列 `org` 是否可以从序列集 `seqs` 中唯一地 **重建** 。

序列 `org` 是 $1$ 到 $n$ 整数的排列，其中 $1 ≤ n ≤ 10^4$。

**重建** 是指在序列集 `seqs` 中构建最短的公共超序列，即 `seqs` 中的任意序列都是该最短序列的子序列。

**注意**：**要避免内存溢出**

- `1 <= n <= 10^4`
- `org` 是数字 `1` 到 `n` 的一个排列
- `1 <= segs[i].length <= 105`
- `seqs[i][j]` 是 `32` 位有符号整数

**思路**：

- 图中**有重边和自环**，所以建立邻接表时要**除去重边和自环**
  - `Set<Integer>[] g  = new Set[n]; // 邻接表，使用Set集合是为了去重`
- 拓扑排序，每次查找入度为 $0$ 的顶点，如果同时有两个入度为 $0$ 的顶点，则没有唯一解。
- 需要注意`org`和`seqs`中的元素可能不一致，不一致则没有解

可重建的条件（用于特判）

- 序列集`seqs`中所有出现过的元素，都存在于`org`中。
- 序列集`seqs`包含了所有`org`中的元素。
- 重建的顺序要唯一，使用拓扑排序可求出重建的可能性，要满足两个条件：
  - 拓扑排序能遍历所有的元素
  - 拓扑排序的顺序是唯一的

```java
class Solution {

    int n;
    Set<Integer>[] g; // 邻接表
    int[] d, q; // d[i] 表示结点编号为i的入度; q是队列

    public boolean topsort(int[] org){
        int hh = 0, tt = -1; // 队列的头/尾指针
        for(int i = 0; i < n; i ++) // 找到所有入度为0的结点
            if(d[i] == 0) q[++ tt] = i;
        if(tt - hh > 0) return false; // 入度为0的结点大于1个，不存在唯一解

        while(hh <= tt){
            int t = q[hh ++];
            for(Integer i : g[t]){ // 遍历t的所有出边
                d[i] --;
                if(d[i] == 0) q[++ tt] = i; // j之前所有的结点都放好了
                if(tt - hh > 0) return false; // 入度为0的结点大于1个，不存在唯一解
            }
        }
        if(tt == n - 1){ // 检测结果序列是否跟目标序列完全一致
            for(int i = 0; i < org.length; i ++)
                if(q[i + 1] != org[i]) return false;
            return true;
        }
        else return false;
    }

    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        n = org.length + 1; // 添加一个特殊的结点编号为0
        g = new Set[n];
        d = new int[n]; q = new int[n];

        for(int i = 0; i < n; i ++) g[i] = new HashSet<>();

        if(seqs.size() == 0) return false; // 特判

        for(int i = 0; i < seqs.size(); i ++){ // 建图,构建邻接表
            List<Integer> arr = seqs.get(i);
            if(arr.size() > org.length) return false; // 特判,seqs中的任意序列都是最短序列的子序列
            // 每个序列串转化为多个边 如：3-4-6-1 转化为：3-4, 4-6, 6-1
            for(int j = 0, a = 0; j < arr.size(); j ++){ 
                int b = arr.get(j);
                if(b < 1 || b > n - 1) return false; // 特判, seqs得到的数字不能超过闭区间[1, n]
                if(a != b && !g[a].contains(b)) { g[a].add(b); d[b] ++ ; } // 去除重边和自环
                a = b;
            }
        }

        return topsort(org);
    }
}
```

### 21、迭代器

##### （1）剑指 Offer II 055. 二叉搜索树迭代器:writing_hand: 

**问题**：

实现一个二叉搜索树迭代器类`BSTIterator` ，表示一个**按中序遍历**二叉搜索树（BST）的迭代器：

- `BSTIterator(TreeNode root)` 初始化 `BSTIterator` 类的一个对象。BST 的根节点 `root` 会作为构造函数的一部分给出。指针应初始化为一个不存在于 BST 中的数字，且该数字小于 BST 中的任何元素。
- `boolean hasNext()` 如果向指针右侧遍历存在数字，则返回 `true` ；否则返回 `false` 。
- `int next()`将指针向右移动，然后返回指针处的数字。

注意，指针初始化为一个不存在于 BST 中的数字，所以对 `next()` 的首次调用将返回 BST 中的最小元素

可以假设 `next()` 调用总是有效的，也就是说，当调用 `next()` 时，BST 的中序遍历中至少存在一个下一个数字。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202270037490.png" alt="img" swidth="300px" />

**思路**：**用栈实现非递归的中序遍历**

- 模拟用栈实现非递归的中序遍历，相当于把中序遍历分开进行了。
- 一开始初始化将所有左孩子放到栈中。每次`next`就取出栈顶的节点并返回当前的值，但当前指针要指向右孩子，再把右子树的所有左孩子节点放到栈中即可。

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
class BSTIterator {

    Deque<TreeNode> stk = new ArrayDeque<TreeNode>(); // 栈, 保存临时状态
    TreeNode cur; // 迭代器指向的是二叉树的当前结点

    public BSTIterator(TreeNode root) {
        cur = root;
    }
    
    public int next() {
        while(cur != null){ // 遍历cur的左子树
            stk.push(cur);
            cur = cur.left;
        }
        cur = stk.pop();
        int res = cur.val; // 当前cur的根结点
        cur = cur.right; // 当前cur的右子树
        return res;
    }
    
    public boolean hasNext() { // 判断是否迭代完成
        return cur != null || !stk.isEmpty();
    }
}
```



### :mag_right: 参考文献 

[1] [《剑指offer 2 面试题51》 书中算法C++实现](https://leetcode-cn.com/problems/jC7MId/solution/jian-zhi-offer-2-mian-shi-ti-51-shu-zhon-aumb/) 

[2] [值和下标之差都在给定的范围内](https://leetcode-cn.com/problems/7WqeDu/solution/zhi-he-xia-biao-zhi-chai-du-zai-gei-ding-94ei/) 

[3] [动画模拟 一文秒杀七道题](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/solution/de-liao-wo-ba-qian-zhui-he-ba-de-gan-gan-ngkp/) 

[4] [最大的异或](https://leetcode-cn.com/problems/ms70jA/solution/zui-da-de-yi-huo-by-leetcode-solution-hr7m/) 

[5] https://leetcode.cn/

[6] https://www.acwing.com/

### :closed_lock_with_key: License

本文内容不是将网上的资料随意拼凑而来，除了少部分引用书上和技术文档的原文（这部分内容都在末尾的参考文献中加了出处），其余都是作者的原创。在您引用本文内容或者对内容进行修改演绎时，请署名并以相同方式共享，谢谢。

转载文章请在开头明显处标明该页面地址，公众号等其它转载请联系 [1246550576@qq.com](mailto:1246550576@qq.com)

### 📝排版申明

笔记内容按照一定标准进行排版，以保证内容的可读性。

不使用 `![]()` 这种方式来引用图片，而是用 `<img>` 标签。为了能够控制图片以合适的大小显示，使用 `<div align="center"> <img src=""/> </div>` 达到居中的效果。

排版美化工具：[Typora-Purple](https://theme.typora.io/theme/Purple/)

