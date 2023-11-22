# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>
> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第四部分之树



<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208181220550.png" alt="image.png" width="600px" />

### 1、前序遍历

#### （1）589. N 叉树的前序遍历

**问题**：

给定一个 n 叉树的根节点  `root` ，返回 其节点值的 **前序遍历** 。

n 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 `null` 分隔（请参见示例）。

<img src="https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png" alt="img" width="400px" />

**思路**：先遍历根结点，再从左往右遍历孩子结点

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
    
    List<Integer> res = new ArrayList<>();

    public void dfs(Node u) {
        if(u == null) return ;
        res.add(u.val); // 先遍历根结点

        if(u.children != null && u.children.size() != 0) // 在遍历孩子结点
            for(Node node : u.children)
                dfs(node);
    }
    
    public List<Integer> preorder(Node root) {
        dfs(root);
        return res;
    }
}
```

#### （2）144. 二叉树的前序遍历

给你二叉树的根节点 `root` ，返回它节点值的 **前序** 遍历

```java
class Solution {
    List<Integer> preorder = new ArrayList<>();

    public void dfs(TreeNode u) {
        preorder.add(u.val);
        if(u.left != null) dfs(u.left);
        if(u.right != null) dfs(u.right);
    }

    public List<Integer> preorderTraversal(TreeNode root) {
        if(root == null) return new ArrayList<>();
        dfs(root);
        return preorder;
    }
}
```

### 2、中序遍历

#### （1）285. 二叉搜索树中的中序后继

**问题**：

给定一棵二叉搜索树和其中的一个节点 `p` ，找到该节点在树中的中序后继。如果节点没有中序后继，请返回 `null` 。节点 `p` 的后继是值比 `p.val` 大的节点中键值最小的节点。

**思路**：**中序遍历二叉树**，当前继结点等于目标结点时, 当前结点为要找的结点

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:

    def __init__(self):
        self.pre, self.cur = None, None # 前继结点和当前结点
        self.target, self.res = None, None

    def dfs(self, u):

        if u.left:
            self.dfs(u.left)
        
        self.cur = u
        if self.pre and self.pre.val == self.target.val: # 当前继结点等于目标结点时, 当前结点为要找的结点
            self.res = self.cur
        self.pre = self.cur

        if u.right:
            self.dfs(u.right)

    def inorderSuccessor(self, root: 'TreeNode', p: 'TreeNode') -> 'TreeNode':
        if not root: # 特判
            return None
        self.target = p
        self.dfs(root) # 中序遍历
        return self.res
```

##### 

### 3、后序遍历

#### （1）590. N 叉树的后序遍历

**问题**：

给定一个 n 叉树的根节点 `root` ，返回 *其节点值的 **后序遍历*** 。

n 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 `null` 分隔（请参见示例）。

**思路**：向从左往右遍历孩子结点，在遍历根结点

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
    
    List<Integer> res = new ArrayList<>();
    
    public void dfs(Node u) {
        if(u.children == null && u.children.size() == 0) { // 叶子结点, 递归结束
            res.add(u.val);
            return ;
        }
        
        for(Node child : u.children) // 向从左往右遍历孩子结点
            if(child != null)
                dfs(child);
        res.add(u.val); // 在遍历根结点
    }

    public List<Integer> postorder(Node root) {
        if(root == null) return res; // 特判
        dfs(root);
        return res;
    }
}
```

#### （2）145. 二叉树的后序遍历

给你一棵二叉树的根节点 `root` ，返回其节点值的 **后序遍历** 。

```java
class Solution {
    List<Integer> postorder = new ArrayList<>();

    public void dfs(TreeNode u) {
        if(u.left != null) dfs(u.left);
        if(u.right != null) dfs(u.right);
        postorder.add(u.val);
    }
    

    public List<Integer> postorderTraversal(TreeNode root) {
        if(root == null) return new ArrayList<>();
        dfs(root);
        return postorder;
    }
}
```

### 4、层序遍历

#### （1）1302. 层数最深叶子节点的和

**问题**

给你一棵二叉树的根节点 `root` ，请你返回 **层数最深的叶子节点的和** 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208171532302.png" alt="img" width="300px" />

**层序遍历**

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

    public int bfs(TreeNode root) {
        final int N = 10010;
        TreeNode[] q = new TreeNode[N]; // 定义队列
        int hh = 0, tt = 0; // hh队头指针, tt 队尾指针
        q[0] = root;

        Deque<Integer> stk = new LinkedList<>();

        while(hh <= tt) { // 层序遍历
            int head = hh, tail = tt;
            int sum = 0; // 计算每一层结点的和
            while(hh <= tail) { // 遍历二叉树的每一层
                TreeNode t = q[hh ++ ];
                sum += t.val;
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
            stk.push(sum);
        }

        return stk.peek();
    }

    public int deepestLeavesSum(TreeNode root) {
        return bfs(root);
    }
}
```

#### （2）637. 二叉树的层平均值

**问题**

给定一个非空二叉树的根节点 `root` , 以数组的形式返回每一层节点的平均值。与实际答案相差 `10-5` 以内的答案可以被接受。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208171626999.jpeg" alt="img" width="200px" />

```java
class Solution {

    public List<Double> bfs(TreeNode root) {
        final int N = 10010;
        TreeNode[] q = new TreeNode[N]; // 队列
        int hh = 0, tt = 0; // 队头指针和队尾指针
        q[0] = root;

        List<Double> res = new ArrayList<>(); // 存储每一层结点的平均值

        while(hh <= tt) { // 层序遍历
            int head = hh, tail = tt;
            long sum = 0, cnt = 0;
            while(hh <= tail) { // 遍历每一层层
                TreeNode t = q[hh ++ ];
                sum += t.val; cnt ++;
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
            res.add((double)sum / cnt); // 求当前层的平均值
        }

        return res;
    }

    public List<Double> averageOfLevels(TreeNode root) {
        return bfs(root);
    }
}
```

#### （3）102. 二叉树的层序遍历

**问题**

给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208171631045.jpeg" alt="img" width="200" />

```
输出：[[3],[9,20],[15,7]]
```

```java
class Solution {

    public List<List<Integer>> bfs(TreeNode root) {
        final int N = 2010;
        TreeNode[] q = new TreeNode[N]; // 队列
        int hh = 0, tt = 0; // 队头指针和队尾指针
        q[0] = root;

        List<List<Integer>> res = new ArrayList<>();

        while(hh <= tt) { // 层序遍历
            int head = hh, tail = tt;
            List<Integer> layer = new ArrayList<>();
            while(hh <= tail) { // 遍历每一层
                TreeNode t = q[hh ++];
                layer.add(t.val);
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
            res.add(layer);
        }

        return res;
    }

    public List<List<Integer>> levelOrder(TreeNode root) {
        if(root == null) return new ArrayList<>();
        return bfs(root);
    }
}
```

#### （4）107. 二叉树的层序遍历 II

**问题**

给你二叉树的根节点 `root` ，返回其节点值 **自底向上的层序遍历** 。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208171630043.jpeg" alt="img" width="200px" />

```
输出：[[15,7],[9,20],[3]]
```

```java
class Solution {

    public List<List<Integer>> bfs(TreeNode root) {
        final int N = 2010;
        TreeNode[] q = new TreeNode[N];
        int hh = 0, tt = 0;
        q[0] = root;

        Deque<List<Integer>> stk = new LinkedList<>();

        while(hh <= tt) {
            int head = hh, tail = tt;
            List<Integer> layer = new ArrayList<>();
            while(hh <= tail) {
                TreeNode t = q[hh ++];
                layer.add(t.val);
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right; 
            }
            stk.push(layer);
        }

        return new ArrayList<>(stk);
    }

    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if(root == null) return new ArrayList<>();
        return bfs(root);
    }
}
```

#### （5）429. N 叉树的层序遍历

给定一个 `N` 叉树，返回其节点值的*层序遍历*。（即从左到右，逐层遍历）。

树的序列化输入是用层序遍历，每组子节点都由 `null` 值分隔（参见示例）。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208181210105.png" alt="img" width="400px" />

```java
class Solution {
    public List<List<Integer>> bfs(Node root) {
        final int N = 10010;
        Node[] q = new Node[N]; // 队列
        int hh = 0, tt = 0;
        q[0] = root;

        List<List<Integer>> res = new ArrayList<>();

        while(hh <= tt) { // 层序遍历
            int head = hh, tail = tt;
            List<Integer> layer = new ArrayList<>();
            while(hh <= tail) { // 遍历每一层
                Node t = q[hh ++];
                layer.add(t.val);

                if(t.children != null) //遍历孩子结点
                    for(Node child : t.children) 
                        if(child != null) q[++ tt] = child;
            }

            res.add(layer);
        }

        return res;
    }

    public List<List<Integer>> levelOrder(Node root) {
        if(root == null) return new ArrayList<>();
        return bfs(root);
    }
}
```

#### （6）[T 秒后青蛙的位置](https://leetcode.cn/problems/frog-position-after-t-seconds/)

**问题**：

给你一棵由 `n` 个顶点组成的无向树，顶点编号从 `1` 到 `n`。青蛙从 **顶点 1** 开始起跳。规则如下：

- 在一秒内，青蛙从它所在的当前顶点跳到另一个 **未访问** 过的顶点（如果它们直接相连）。
- 青蛙**无法跳回已经访问过的顶点**。
- 如果青蛙可以跳到多个不同顶点，那么**它跳到其中任意一个顶点上的机率都相同**。
- 如果青蛙不能跳到任何未访问过的顶点上，那么**它每次跳跃都会停留在原地**。

无向树的边用数组 `edges` 描述，其中 `edges[i] = [ai, bi]` 意味着存在一条直接连通 `ai` 和 `bi` 两个顶点的边。返回青蛙在 *`t`* 秒后位于目标顶点 *`target`* 上的概率。与实际答案相差不超过 `10-5` 的结果将被视为正确答案。

例子：

<img src="https://assets.leetcode.com/uploads/2021/12/21/frog1.jpg" alt="img" style="zoom:80%;" />

```
输入：n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 2, target = 4
输出：0.16666666666666666 
解释：上图显示了青蛙的跳跃路径。青蛙从顶点 1 起跳，第 1 秒 有 1/3 的概率跳到顶点 2 ，然后第 2 秒 有 1/2 的概率跳到顶点 4，因此青蛙在 2 秒后位于顶点 4 的概率是 1/3 * 1/2 = 1/6 = 0.16666666666666666 。 
```

**数据范围**：

- `1 <= n <= 100`
- `edges.length == n - 1`
- `edges[i].length == 2`
- `1 <= ai, bi <= n`
- `1 <= t <= 50`
- `1 <= target <= n`

**思路**：树的层序遍历，且记录每一层。

```java
class Solution {

    public double bfs(boolean[][] g, int n, int t, int target) {
        // 初始化队列
        int hh = 0, tt = 0; // 队头, 队尾
        int[] q = new int[n + 10]; 

        boolean[] st = new boolean[n + 10]; // 判重数组 
        double[] p = new double[n + 10]; // 概率数组
        boolean[] leaf = new boolean[n + 10]; // 存储每个节点是否是叶子节点
        int[] l = new int[n + 10]; // 存储每个节点的所在的层数, 1节点在0层

        q[0] = 1; // 起点从1开始
        st[1] = true; p[1] = 1; l[1] = 0;

        int layer = 0; // 遍历的层数
        int head = hh, tail = tt;
        while(hh <= tt) { // 队列不为空

            head = hh; tail = tt;
            layer += 1;

            while(hh <= tail) { // 遍历每一层的节点
                int tx = q[hh ++]; // 队头元素出队

                int count = 0; // tx的子节点的个数
                for(int i = 1; i < g[tx].length; i ++ )
                    if(g[tx][i] && !st[i]) count ++ ;

                if(count == 0) leaf[tx] = true; // tx是叶子节点

                for(int i = 1; i < g[tx].length; i ++ ) { // 遍历临接点
                    if(!g[tx][i]) continue; // 可达
                    if(st[i]) continue; // 不是父节点

                    st[i] = true;
                    p[i] = p[tx] * (1.0 / count);
                    q[++ tt] = i; 
                    l[i] = layer;
                    // 当前层数是t层，且当前节点i是targat
                    if(layer == t && target == i) return p[target];
                }
            }
        }
        // target是叶子结点 并且 t大于等于target所在的层数
        if(t >= l[target] && leaf[target]) return p[target];

        return 0; // 其他情况返回概率为0
    }

    public double frogPosition(int n, int[][] edges, int t, int target) {
        boolean[][] g = new boolean[n + 10][n + 10];
        for(int i = 0; i < edges.length; i ++ ) {
            int a = edges[i][0], b = edges[i][1];
            g[a][b] = g[b][a] = true;
        }

        return bfs(g, n, t, target);
    }
}
```



### 5、二叉树的构建与修改

#### （1）6018. 根据描述创建二叉树

**问题**：

给你一个二维整数数组 `descriptions` ，其中 `descriptions[i] = [parenti, childi, isLefti]` 表示 `parenti` 是 `childi` 在 **二叉树** 中的 **父节点**，二叉树中各节点的值 **互不相同** 。此外：

- 如果 `isLefti == 1` ，那么 `childi` 就是 `parenti` 的左子节点。
- 如果 `isLefti == 0` ，那么 `childi` 就是 `parenti` 的右子节点。

请你根据 `descriptions` 的描述来构造二叉树并返回其 **根节点** 。

测试用例会保证可以构造出 **有效** 的二叉树。

**思路**：

- 求根结点
- 利用前序遍历构建二叉树

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
    
    public TreeNode build(int u, Map<Integer, int[]> map){
        
        TreeNode root = new TreeNode(u);
        if(!map.containsKey(u)) return root; // 叶子结点
        
        int[] child = map.get(u);
        if(child[0] != 0) root.left = build(child[0], map); // 构建左子树
        if(child[1] != 0) root.right = build(child[1], map); // 构建右子树
        return root;
    }    
    
    public TreeNode createBinaryTree(int[][] descriptions) {
        
        Map<Integer, Integer> hash = new HashMap<>();
        Map<Integer, int[]> map = new HashMap<>(); // value是key的左右孩子
        
        for(int i = 0; i < descriptions.length; i ++){
            int p = descriptions[i][0], son = descriptions[i][1], st = descriptions[i][2];
            hash.put(p, hash.getOrDefault(p, p));
            hash.put(son, p);
            
            int[] child = map.getOrDefault(p, new int[]{0, 0});
            child[1 - st] = son;
            map.put(p, child);
        }
        
        int root = -1;
        Set<Integer> keySet = hash.keySet();
        for(Integer x : keySet){ // 求根结点, 没有父节点的节点为根节点
            if(x.equals(hash.get(x))){
                root = x;
                break;
            }
        }
        return build(root, map);
    }
}
```

#### （2）889. 根据前序和后序遍历构造二叉树:writing_hand:  

**问题**

给定两个整数数组，`preorder` 和 `postorder` ，其中 `preorder` 是一个具有 **无重复** 值的二叉树的前序遍，`postorder` 是同一棵树的后序遍历，重构并返回二叉树。

如果存在多个答案，您可以返回其中 **任何** 一个。

注意，`preorder` 中所有值都 **不同**，`postorder` 中所有值都 **不同**

**思路**

- 首先`preorder`的第一个节点和`postorder`的最后一个节点一定是根节点`root`
- 可以暴力枚举所有的(前序遍历的)左子树，左子树长度确定，即右子树的长度确定。后序遍对应的左右子树长度也确定
- 递归求解左右子树的所有方案数，得到一个合法的中序序列
- 根据前序序列和中序序列重建二叉树

**代码**

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

    int[] pre, post; // 前序序列 后序序列
    List<Integer> in = new ArrayList<>(); // 中序序列
    Map<Integer, Integer> pos = new HashMap<>(); // pos记录结点key在中序序列对应的位置是value

    // // 根据前序序列和后序序列求出一个合法的中序序列
    public int dfs(int l1, int r1, int l2, int r2, List<Integer> inorder) {
        if(l1 > r1) return 1; // 空树是一种解决方案
        if(pre[l1] != post[r2]) return 0; // 不是一种合法的方案

        int cnt = 0;
        for(int i = l1; i <= r1; i ++ ) { // 枚举所有左右子树的边界
            List<Integer> lt = new ArrayList<>(), rt = new ArrayList<>();
            int lcnt = dfs(l1 + 1, i, l2, l2 + i - l1 - 1, lt);
            int rcnt = dfs(i + 1, r1, l2 + i - l1 - 1 + 1, r2 - 1, rt);  

            if(lcnt != 0 && rcnt != 0) { // 找到一种合法方案
                inorder.clear(); // 清空前一种合法方案
                for(Integer x : lt) inorder.add(x);
                inorder.add(pre[l1]);
                for(Integer x : rt) inorder.add(x);

                cnt += lcnt * rcnt;
                if(cnt > 1) break;
            }
        }
        return cnt;
    }

    public TreeNode build(int il, int ir, int pl, int pr) { // 根据中序序列和前序序列构建二叉树
        TreeNode root = new TreeNode(pre[pl]); // 根结点
        int k = pos.get(pre[pl]); // 根结点在中序序列的位置

        if(il < k) root.left = build(il, k - 1, pl + 1, pl + 1 + k - 1 - il); // 左子树
        if(k < ir) root.right = build(k + 1, ir, pl + 1 + k - 1 - il + 1, pr); // 右子树

        return root;
    }

    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        int n = preorder.length;
        pre = preorder;
        post = postorder;

        int cnt = dfs(0, n - 1, 0, n - 1, in); // 根据前序序列和后序序列求出一个合法的中序序列

        for(int i = 0; i < in.size(); i ++) pos.put(in.get(i), i);

        return build(0, n - 1, 0, n - 1);
    }
}
```

#### （3）654. 最大二叉树

**问题**

给定一个不重复的整数数组 `nums` 。 **最大二叉树** 可以用下面的算法从 `nums` 递归地构建:

- 创建一个根节点，其值为 `nums` 中的最大值。

- 递归地在最大值 **左边** 的 **子数组前缀上** 构建左子树。

- 递归地在最大值 **右边** 的 **子数组后缀上** 构建右子树。  

  返回 *`nums` 构建的* **最大二叉树** 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208200851746.jpeg" alt="img" width="200px" />

```
nums = [3,2,1,6,0,5]
```

**思路**

采用**分治**的思路

用递归函数 $build(l,r,a) $表示对数组 $a$ 中从 $a[l]$ 到 $a[r]$ 的元素构建一棵树。我们首先找到这一区间中的**最大值**，记为 $a$ 中 $a[k]$，这样就确定了根节点的值。随后我们就可以进行递归：

- 左子树为 $build(l, k - 1, a)$；

- 右子树为 $build(k + 1,r,a)$。


```java
class Solution {
    Map<Integer, Integer> hash = new HashMap<>();

    public TreeNode build(int l, int r, int[] a) {
        int x = -0x3f3f3f3f;
        for(int i = l; i <= r; i ++) x = Math.max(x, a[i]); // 寻找区间[l,r]中的最大值

        int k = hash.get(x); // 最大值对应的下标
        TreeNode root = new TreeNode(x);

        if(l < k) root.left = build(l, k - 1, a); // 构建左子树
        if(k < r) root.right = build(k + 1, r, a); // 构建右子树

        return root;
    }

    public TreeNode constructMaximumBinaryTree(int[] nums) {
        int n = nums.length;
        for(int i = 0; i < n; i ++) hash.put(nums[i], i);

        return build(0, n - 1, nums);
    }
}
```

#### （4）[1110. 删点成林](https://leetcode.cn/problems/delete-nodes-and-return-forest/)

**问题**：

给出二叉树的根节点 `root`，树上每个节点都有一个不同的值。

如果节点值在 `to_delete` 中出现，我们就把该节点从树上删去，最后得到一个森林（一些不相交的树构成的集合）。

返回森林中的每棵树。你可以按任意顺序组织答案。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/07/05/screen-shot-2019-07-01-at-53836-pm.png" alt="img" style="zoom:50%;" />

```
输入：root = [1,2,3,4,5,6,7], to_delete = [3,5]
输出：[[1,2,null,4],[6],[7]]
```

**数据范围**：

- 树中的节点数最大为 `1000`。
- 每个节点都有一个介于 `1` 到 `1000` 之间的值，且各不相同。
- `to_delete.length <= 1000`
- `to_delete` 包含一些从 `1` 到 `1000`、各不相同的值。

**思路**：

此题考查二叉树的删除操作。我们用一个列表`List<TreeNode> roots`来维护森林中的每一个二叉树的根节点。

我们可以采用**DFS遍历**二叉树中的每一个节点。当一个节点被删除时，需要将其左右子树(子树不为空)分别加入到列表`roots`中，同时‘’通知‘’其父节点表示自己被删除了。

需要注意的是根节点`root`是否被删除需要进行特判。

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

    List<TreeNode> roots = new ArrayList<>();

    public boolean dfs(TreeNode u, boolean[] to_delete) {

        if(u.left != null && dfs(u.left, to_delete)) u.left = null; 
        if(u.right != null && dfs(u.right, to_delete)) u.right = null;

        if(to_delete[u.val]) {
            if (u.left != null) {
                roots.add(u.left);
                u.left = null;
            }
            if (u.right != null) {
                roots.add(u.right);
                u.right = null;
            }
            return true;
        }
        return false;
    }

    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
        boolean[] st = new boolean[1001];
        for(int i = 0; i < to_delete.length; i ++)
            st[to_delete[i]] = true;

        if(!st[root.val]) roots.add(root);
        dfs(root, st);

        return roots;
    }
}
```





### 6、二叉搜索树的构建与修改

#### （1）1008. 前序遍历构造二叉搜索树

**问题**

给定一个整数数组，它表示BST(即 **二叉搜索树** )的 **先序遍历** ，构造树并返回其根。

**保证** 对于给定的测试用例，总是有可能找到具有给定需求的二叉搜索树。

**二叉搜索树** 是一棵二叉树，其中每个节点， `Node.left` 的任何后代的值 **严格小于** `Node.val` , `Node.right` 的任何后代的值 **严格大于** `Node.val`。

二叉树的 **前序遍历** 首先显示节点的值，然后遍历`Node.left`，最后遍历`Node.right`。

- `1 <= preorder.length <= 100`
- `1 <= preorder[i] <= 10^8`
- `preorder` 中的值 **互不相同**

**思路** 

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

    Map<Integer, Integer> pos = new HashMap<>(); // pos记录结点key在中序序列对应的位置是value
    int[] inorder, preorder; // 中序序列, 前序序列

    public TreeNode build(int il, int ir, int pl, int pr) { // 前序序列和中序序列构建二叉搜索树
        TreeNode root = new TreeNode(preorder[pl]); // 根结点
        int k = pos.get(preorder[pl]); // 根结点在中序序列中的位置

        if(il < k) root.left = build(il, k - 1, pl + 1, pl + 1 + k - 1 - il); // 左子树
        if(k < ir) root.right = build(k + 1, ir, pl + 1 + k - 1 - il + 1, pr); // 右子树

        return root;
    }

    public TreeNode bstFromPreorder(int[] preorder) {
        this.preorder = preorder;
        int n = preorder.length;
        inorder = Arrays.copyOfRange(preorder, 0, n);
        Arrays.sort(inorder);

        for(int i = 0; i < n; i ++ ) pos.put(inorder[i], i);

        return build(0, n - 1, 0, n - 1);
    }
}
```

#### （2）538. 把二叉搜索树转换为累加树

**问题**

给出二叉 **搜索** 树的根节点，该树的节点值各不相同，请你将其转换为**累加树**（Greater Sum Tree），使每个节点 `node` 的新值等于原树中大于或等于 `node.val` 的值之和。树中的所有值 **互不相同** 。

提醒一下，二叉搜索树满足下列约束条件：

- 节点的左子树仅包含键 **小于** 节点键的节点。
- 节点的右子树仅包含键 **大于** 节点键的节点。
- 左右子树也必须是二叉搜索树。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208191313765.png" alt="img" width="500px" />

**思路**

BST 的中序遍历（**左根右**）就是从小到大,那么反过来（**右根左**）就是从大到小，然后**累加**

```java
class Solution {
    int sum = 0;
    
    public void rebuild(TreeNode u) {
        if(u.right != null) rebuild(u.right); // 右子树
        u.val = u.val + sum;
        sum = u.val;
        if(u.left != null) rebuild(u.left); // 左子树
    }

    public TreeNode convertBST(TreeNode root) {
        if(root == null) return root;

        rebuild(root);
        return root;
    }
}
```



### 7、二叉树的属性

#### （1）222. 完全二叉树的节点个数

**问题**

给你一棵 **完全二叉树** 的根节点 `root` ，求出该树的节点个数。

[完全二叉树](https://baike.baidu.com/item/完全二叉树/7773232?fr=aladdin) 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 `h` 层，则该层包含 `1~ 2h` 个节点。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208191152007.jpeg" alt="img" width="270px" />

**思路一**：层序遍历求结点数 $O(n)$

```java
class Solution {

    public int bfs(TreeNode root) {
        final int N = 50010;
        TreeNode[] q = new TreeNode[N];
        int hh = 0, tt = 0;

        q[0] = root;

        while(hh <= tt) {
            TreeNode t = q[hh ++];
            if(t.left != null) q[++ tt] = t.left;
            if(t.right != null) q[++ tt] = t.right;
        } 

        return tt + 1;
    }

    public int countNodes(TreeNode root) {
        if(root == null) return 0;
        return bfs(root);
    }
}
```

**思路二**：根据**完全二叉树的性质**，求结点数

```java
class Solution {
    public int dfs(TreeNode root, int k) {
        if(root == null) return 0;
        if(root.left == null && root.right == null) return k;

        int a = dfs(root.left, 2 * k);
        int b = dfs(root.right, 2 * k + 1);
        return Math.max(a, b);
    }

    public int countNodes(TreeNode root) {
        return dfs(root, 1);
    }
}
```

#### （2）366. 寻找二叉树的叶子节点

**问题**：

给你一棵二叉树，请按以下要求的顺序收集它的全部节点：

- 依次从左到右，每次收集并删除所有的叶子节点
- 重复如上过程直到整棵树为空

**思路**：**DFS**

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

    // u是当前结点, p是u的父结点, st为0表示u是p的左孩子, st为1表示u是p的右孩子, arr存储此次删除的结点
    public void dfs(TreeNode u, int st, TreeNode p, List<Integer> del){
        if(u.left == null && u.right == null) { // u是叶子结点, 则将u删除
            del.add(u.val);
            if(st == 0) p.left = null;
            if(st == 1) p.right = null;
            return ;
        } 

        if(u.left != null) dfs(u.left, 0, u, del); // 先遍历左子树
        if(u.right != null) dfs(u.right, 1, u, del); // 在遍历右子树
    }

    public List<List<Integer>> findLeaves(TreeNode root) {
        TreeNode p = new TreeNode();
        p.left = root; // root是p的左孩子

        List<List<Integer>> res = new ArrayList<>();
        while(p.left != null){ // 重复如下过程直到整棵树为空
            List<Integer> del = new ArrayList<>(); // 依次从左到右，每次收集并删除所有的叶子节点
            dfs(root, 0, p, del);
            res.add(del);
        }
        return res;
    }
}
```

#### （3）树的直径

问题：

给你这棵「无向树」，请你测算并返回它的「直径」：这棵树上最长简单路径的 **边数**。

我们用一个由所有「边」组成的数组 `edges` 来表示一棵无向树，其中 `edges[i] = [u, v]` 表示节点 `u` 和 `v` 之间的双向边。

树上的节点都已经用 `{0, 1, ..., edges.length}` 中的数做了标记，每个节点上的标记都是独一无二的。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203102310886.png" alt="img" width="320px" />

**思路**：

- 将无向树转换为一个**无向图**
- 用邻接表建图，并维护每个结点的入度和出度
- 求每一个叶子到达其他叶子结点的最大路径
- 求所有最大路径的最大值

```java
class Solution {

    int n;
    boolean[] st; // 判重数组, st[i]为true表示i被搜索过
    int[] in, out; // in[i]的入度, out[i]是i的出度
    int[] h, e, ne; // 邻接表
    int idx = 0;
    int res = 0;

    public void add(int a, int b){ // 向邻接表添加一条a->b的边
        in[b] ++; out[a] ++ ;
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++;
    }

    public void dfs(int u,  int k, boolean start){ // start为true表示u是起点
        if(!start && in[u] == 1 && out[u] == 1){ // 从一个叶子结点到另一个叶子结点找到一条路径
            res = Math.max(res, k);
            return ;
        }

        for(int i = h[u]; i != -1; i = ne[i]){
            int j = e[i];
            if(!st[j]){
                st[j] = true;
                dfs(j, k + 1, false);
                st[j] = false;
            }
        }
    }

    public int treeDiameter(int[][] edges) {
        this.n = edges.length + 1;
        h = new int[n + 10]; e = new int[2*(n + 10)]; ne = new int[2*(n + 10)];
        st = new boolean[n + 10];
        in = new int[n + 10]; out = new int[n + 10];

        Arrays.fill(h, -1); 
        
        for(int i = 0; i < edges.length; i ++){ // 建图
            int a = edges[i][0], b = edges[i][1];
            add(a, b); add(b, a);
        }

        for(int i = 0; i < n; i ++){ // 从每一个叶子结点开始dfs搜索
            st[i] = true;
            if(in[i] == 1 && out[i] == 1) // 叶子结点的入度和出度都为1
                dfs(i, 0, true);
            st[i] = false;
        }

        return res;    
    }
}
```

#### （4）1522. N 叉树的直径

**问题**：

给定一棵 N 叉树的根节点 `root` ，计算这棵树的直径长度。

N 叉树的直径指的是树中任意两个节点间路径中 **最长** 路径的长度。**这条路径可能经过根节点，也可能不经过根节点**。

（N 叉树的输入序列以层序遍历的形式给出，每组子节点用 null 分隔）

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203102314360.png" alt="img" width="240" />

**思路**：递归求解

- 经过某个结点的最长路径，等于从该结点到叶子结点的最长路径和次长路径之和
- N 叉树的直径，即对于每一个结点，求经过其的最长路径，所有最长路径取最大值即为 N 叉树的直径
- 该结点到叶子结点的最长路径，等于其孩子结点到叶子结点的最长路径长度+ 1

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    
    public Node() {
        children = new ArrayList<Node>();
    }
    
    public Node(int _val) {
        val = _val;
        children = new ArrayList<Node>();
    }
    
    public Node(int _val,ArrayList<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
    int res = 0;

    public int dfs(Node u){  // 返回值为以u为根节点的子树, 从u到叶子结点的最大路径
        if(u.children == null || u.children.size() == 0) // 叶子结点
            return 0;

        int first = 0, sec = 0; // 以u为根结点的子树, 从u到叶子结点的最大路径和次大路径
        for(Node child : u.children){
            int t = dfs(child);
            int k = t + 1;
            if(k > first) { // 根据k更新最大路径和次大路径
                sec = first;
                first = Math.max(first, k);
            }
            else if(k > sec) sec = Math.max(sec, k);
        }
        res = Math.max(res, first + sec); // 求最大直径
        return first;
    }
 
    public int diameter(Node root) {
        dfs(root); 
        return res;       
    }
}
```

#### （5）543. 二叉树的直径

**问题**：

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。**这条路径可能穿过也可能不穿过根结点。** 

**注意：**两结点之间的路径长度是以它们之间边的数目表示。

思路：

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

    int res = 0;

    public int dfs(TreeNode u){
        if(u.left == null && u.right == null) return 0; // 叶子结点
    
        int l = 0, r = 0; // l表示左孩子到根结点的最长路径, r表示右孩子到根结点的最长路径
        if(u.left != null) l = dfs(u.left) + 1;
        if(u.right != null) r = dfs(u.right) + 1;
        res = Math.max(res, l + r); // 经过u的最长路径
        return Math.max(l, r); // u到叶子结点的最长路径
    }

    public int diameterOfBinaryTree(TreeNode root) {
        dfs(root);
        return res;
    }
}
```

#### （6）742. 二叉树最近的叶节点

**问题**：

给定一个 **每个结点的值互不相同** 的二叉树，和一个目标整数值 `k`，返回树中与目标值 `k` **最近的叶结点** 。 

**与叶结点最近** 表示在二叉树中到达该叶节点需要行进的边数与到达其它叶结点相比最少。而且，当一个结点没有孩子结点时称其为叶结点。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203082128881.jpeg" alt="img" width="300px" />

**思路**：

- **将二叉树转换为图**（注意：建图时，从根结点到 k 的路径要逆向）
- 利用 **BFS 搜索**距离k最近的叶子结点，即 BFS搜索到的第一个叶子结点即为答案

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

    final int N = 1010;
    int[] h = new int[N], e = new int[N], ne = new int[N]; // 邻接表
    int idx = 0, start = 0; // start是 k 对应的结点
    int[] p = new int[N]; // p[i] 表示i的父结点, p[i]==0表示 i 没有父结点
    boolean[] leaf = new boolean[N]; // leaf[i]为true表示i是叶子结点

    public void add(int a, int b){ // 向邻接表中增加一条边a->b
        e[idx] = b; ne[idx] = h[a]; h[a] = idx ++ ;
    }

    public void dfs(TreeNode u){ // 前序遍历二叉树, 记录p[i]，同时查找k对应的结点
        if(u.left == null && u.right == null) { // 标记叶子结点
            leaf[u.val] = true;
            return ;
        }
        if(u.left != null) {
            p[u.left.val] = u.val;
            dfs(u.left);
        }
        if(u.right != null) {
            p[u.right.val] = u.val;
            dfs(u.right);
        }
    } 

    public int bfs() { // 从k开始宽搜, 搜到的第一个叶子结点，即为距离k最近的叶子结点
        Queue<Integer> q = new ArrayDeque<>(); // 队列
        boolean[] st = new boolean[N]; // 判重数组

        q.offer(start); // 队头元素入队列
        st[start] = true;
        
        int res = 0;
        while(!q.isEmpty()){
            int t = q.poll(); // 队头元素出队列
            if(leaf[t]) {
                res = t;
                break;
            }
            for(int i = h[t]; i != -1; i = ne[i]){ // 搜索t的所有出边
                int j = e[i];
                if(st[j]) continue;
                q.offer(j);
                st[j] = true;
            }
        }
        return res;
    }

    public int findClosestLeaf(TreeNode root, int k) {
        Arrays.fill(h, -1); // 初始化头指针数组
        Arrays.fill(p, -1);
        start = k;

        dfs(root);
        for(int i = start, t = 0; i != root.val; i = t) { // 将root-> ... ->start 的边方向添加到图中
            add(i, p[i]);
            t = p[i];
            p[i] = -1; // 清除此边
        }
        for(int i = 0; i < p.length; i ++ ) // 将其余的边添加到图中
            if(p[i] != -1) 
                add(p[i], i);

        return bfs();
    }
}
```



### 8、二叉搜索树的属性

#### （1）[1373. 二叉搜索子树的最大键值和](https://leetcode.cn/problems/maximum-sum-bst-in-binary-tree/)

**问题**：

给你一棵以 `root` 为根的 **二叉树** ，请你返回 **任意** 二叉搜索子树的最大键值和。

二叉搜索树的定义如下：

- 任意节点的左子树中的键值都 **小于** 此节点的键值。
- 任意节点的右子树中的键值都 **大于** 此节点的键值。
- 任意节点的左子树和右子树都是二叉搜索树。

例子1：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/03/07/sample_1_1709.png)

```
输入：root = [1,4,3,2,4,2,5,null,null,null,null,null,null,4,6]
输出：20
解释：键值为 3 的子树是和最大的二叉搜索树。
```

例子2：

```
输入：root = [-4,-2,-5]
输出：0
解释：所有节点键值都为负数，和最大的二叉搜索树为空。
```

**数据范围**：

- 每棵树有 `1` 到 `40000` 个节点。
- 每个节点的键值在 `[-4 * 10^4 , 4 * 10^4]` 之间。

**思路**：

- 构造一个类型，用于每一个节点的信息，包括以该节点为根的二叉树的键值和，是否是BST，左子树最小的键值（如果左子树不存在就等于val）和右子树最大建值（如果右子树不存在就等于val）。
- 遍历该二叉树的每一个节点，统计节点信息 。
- 找出最大键值和。

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

class Info {
    int sum; // 二叉树的键值和
    Boolean isBST; // 是否是BST
    int minLeft; // 左子树最小的键值；如果左子树不存在就等于val
    int maxRight; // 右子树最大建值；如果右子树不存在就等于val 

    Info(int sum, Boolean isBST, int minLeft, int maxRight) {
        this.sum = sum;
        this.isBST = isBST;
        this.minLeft = minLeft;
        this.maxRight = maxRight;
    }
}

class Solution {
    
    HashMap<TreeNode,Info> res = new HashMap<>();
    
    public void dfs(TreeNode u) {
        if (u.left == null && u.right == null ) { // 单个节点一定是BST
            res.put(u, new Info(u.val, true, u.val, u.val));
            return;
        }

        if (u.left != null) dfs(u.left);
        if (u.right != null) dfs(u.right);

        // 左子树或者右子树不是BST，则以u为根的二叉树也不是BST
        if ( (u.left != null && !res.get(u.left).isBST ) || (u.right != null && !res.get(u.right).isBST )) {
            res.put(u, new Info(-0x3fffffff, false, -0x3fffffff, -0x3fffffff));
            return;
        }

        // 以u为根的二叉树是BST, 满足BST的条件
        // 左子树中的键值都 小于 此节点的键值
        // 右子树中的键值都 大于 此节点的键值
        if ( (u.left == null || (u.left.val < u.val && res.get(u.left).maxRight < u.val)) && 
         (u.right == null || (u.right.val > u.val) && res.get(u.right).minLeft > u.val ) ) {
            
            int sum = u.val + (u.left != null ? res.get(u.left).sum : 0) +
             (u.right != null ? res.get(u.right).sum : 0);
            
            int minLeft = u.left != null ? res.get(u.left).minLeft : u.val;
            int maxRight = u.right != null ? res.get(u.right).maxRight : u.val;
            res.put(u, new Info(sum, true, minLeft, maxRight));
        } else {
            res.put(u, new Info(-0x3fffffff, false, -0x3fffffff, -0x3fffffff));
        }
    }

    public int maxSumBST(TreeNode root) {
        dfs(root);

        int sum = -0x3fffffff;
        Boolean exist = false;
        Set<TreeNode> keySet = res.keySet();
        for (TreeNode u : keySet ) {
            if (res.get(u).isBST && res.get(u).sum > sum) {
                sum = res.get(u).sum;
                exist = true;
            }
        }
        
        if (!exist) return 0;
        return sum <= 0 ? 0 : sum;
    }
}
```

#### （2）510. 二叉搜索树中的中序后继 II:writing_hand: 

**问题**：

给定一棵二叉搜索树和其中的一个节点 `node` ，找到该节点在树中的中序后继。如果节点没有中序后继，请返回 `null` 。

一个节点 `node` 的中序后继是键值比 `node.val` 大所有的节点中键值最小的那个。

**思路**：

- 如果有右结点, 则中序后继为右子树最靠左的结点
- 否则，中序后继就是**第一个比他大的祖宗结点**

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
        self.parent = None
"""

class Solution:
    def inorderSuccessor(self, node: 'Node') -> 'Optional[Node]':
        if node.right: # 如果有右结点, 则中序后继为右子树最靠左的结点
            cur = node.right
            while cur.left:
                cur = cur.left
            return cur
        
        if node.parent: # 否则,就是第一个比他大的祖宗结点
            p = node
            while p.parent:
                p = p.parent
                if p.val > node.val:
                    return p

        return None
```

##### 

### 9、二叉树的公共祖先问题

#### （1）[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)（树上倍增算法）

**问题**：

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例子：

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出：3
解释：节点 5 和节点 1 的最近公共祖先是节点 3 。
```

**数据范围**：

- 树中节点数目在范围 `[2, 10^5]` 内。
- `-10^9 <= Node.val <= 10^9`
- 所有 `Node.val` `互不相同` 。
- `p != q`
- `p` 和 `q` 均存在于给定的二叉树中。

**思路**：倍增算法求解LCA模板题

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
    Map<TreeNode, Integer> a = new HashMap<>(); // 离散化
    Map<Integer, TreeNode> ra = new HashMap<>(); // 反哈希
    int[] d = new int[100010]; // d[i]表示i节点的深度
    int[][] f = new int[100010][22]; // f[i][j]存储i节点的2^j倍父亲是哪个节点, f[i][0]是其父节点
    int n = 0;

    public void dfs(TreeNode u, int k, int p){ // k当前所在层数, p是u的父节点
        a.put(u, n ++); ra.put(a.get(u), u);
        d[a.get(u)] = k;
        f[a.get(u)][0] = p;
        if(u.left != null) dfs(u.left, k + 1, a.get(u));
        if(u.right != null) dfs(u.right, k + 1, a.get(u));
    }

    public TreeNode lca(TreeNode p, TreeNode q) { // 5 = (101)2
        int x = a.get(p), y = a.get(q);
        if(d[x] < d[y]) { // 默认d[x] >= d[y]
            int temp = x; x = y; y = temp;
        }
        for(int i = 21; i >= 0; i -- ) //先让 x 跳到 y 的同层 
            if(d[f[x][i]] >= d[y])
                x = f[x][i];

        if(x == y) return ra.get(x); // 如果重合，就已经结束了

        for(int i = 21; i >= 0; i --) //不重合，x 和 y 一起往上跳
            if(f[x][i] != f[y][i]) { //只要父亲不同，就跳，父亲相同，就停了 
                x = f[x][i];
                y = f[y][i];
            }
        return ra.get(f[x][0]);
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        dfs(root, 1, 0); // 初始化d数组和f数组, 让根的层数为1，定义f[r][0]作为边界, f[0][0] = 0

        for(int j = 1; j < 22; j ++ ) // 第一层循环是步数，从小到大；
            for(int i = 0; i < n; i ++ ) // 第二层循环是点数，
                f[i][j] = f[f[i][j - 1]][j - 1]; // 将2^j 分成两个 2^(j-1)
        return lca(p, q);
    }
}
```

#### （2）[235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)



#### （3）[1123. 最深叶节点的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-deepest-leaves/)（爬山法）

**问题**：

给你一个有根节点 `root` 的二叉树，返回它 *最深的叶节点的最近公共祖先* 。

回想一下：

- **叶节点** 是二叉树中没有子节点的节点
- 树的根节点的 **深度** 为 `0`，如果某一节点的深度为 `d`，那它的子节点的深度就是 `d+1`
- 如果我们假定 `A` 是一组节点 `S` 的 **最近公共祖先**，`S` 中的每个节点都在以 `A` 为根节点的子树中，且 `A` 的深度达到此条件下可能的最大值。

例子：

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/01/sketch1.png" alt="img" style="zoom: 50%;" />

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4]
输出：[2,7,4]
解释：我们返回值为 2 的节点，在图中用黄色标记。
在图中用蓝色标记的是树的最深的节点。
注意，节点 6、0 和 8 也是叶节点，但是它们的深度是 2 ，而节点 7 和 4 的深度是 3 。
```

**数据范围**：

- 树中的节点数将在 `[1, 1000]` 的范围内。
- `0 <= Node.val <= 1000`
- 每个节点的值都是 **独一无二** 的。

**思路**：爬山法

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

    Map<TreeNode, Integer> map = new HashMap<>(); // 哈希表，离散化
    Map<Integer, TreeNode> remap = new HashMap<>(); // 反哈希
    int[] d = new int[1010]; // d[i]表示结点i的深度
    int[] p = new int[1010]; // p[i]是结点i的父节点
    int n = 0, maxd = Integer.MIN_VALUE; // n是结点数量, maxd是树最大的深度

    public void dfs(TreeNode u, int k, int parent) {
        map.put(u, n ++);
        remap.put(map.get(u), u);
        d[map.get(u)] = k;
        p[map.get(u)] = parent;

        maxd = Math.max(maxd, k);
        
        if(u.left != null) dfs(u.left, k + 1, map.get(u));
        if(u.right != null) dfs(u.right, k + 1, map.get(u));
    }

    public TreeNode lcaDeepestLeaves(TreeNode root) {
        dfs(root, 0, 0); // 初始化map, remap, d, p

        List<Integer> leafs = new ArrayList<>(); // 计算深度最大的叶子节点
        for(int i = 0; i < n; i ++ )
            if(d[i] == maxd) leafs.add(i);

        if(leafs.size() == 1) return remap.get(leafs.get(0));
        
        int a = leafs.get(0); // 爬山法求所有深度最大的叶子节点的最近公共祖先
        for(int i = 1; i < leafs.size(); i ++ ) {
            int b = leafs.get(i);
            while(a != b) {
                if(d[a] > d[b]) a = p[a];
                else b = p[b]; 
            }
        }

        return remap.get(a);
    }
}
```

#### （4）[1483. 树节点的第 K 个祖先](https://leetcode.cn/problems/kth-ancestor-of-a-tree-node/) （树上倍增算法）

**问题**：

给你一棵树，树上有 `n` 个节点，按从 `0` 到 `n-1` 编号。树以父节点数组的形式给出，其中 `parent[i]` 是节点 `i` 的父节点。树的根节点是编号为 `0` 的节点。

树节点的第 *`k`* 个祖先节点是从该节点到根节点路径上的第 `k` 个节点。

实现 `TreeAncestor` 类：

- `TreeAncestor（int n， int[] parent）` 对树和父数组中的节点数初始化对象。
- `getKthAncestor(int node, int k)` 返回节点 `node` 的第 `k` 个祖先节点。如果不存在这样的祖先节点，返回 `-1` 。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/06/14/1528_ex1.png" alt="img" style="zoom:80%;" />

```
输入：
["TreeAncestor","getKthAncestor","getKthAncestor","getKthAncestor"]
[[7,[-1,0,0,1,1,2,2]],[3,1],[5,2],[6,3]]

输出：
[null,1,0,-1]

解释：
TreeAncestor treeAncestor = new TreeAncestor(7, [-1, 0, 0, 1, 1, 2, 2]);

treeAncestor.getKthAncestor(3, 1);  // 返回 1 ，它是 3 的父节点
treeAncestor.getKthAncestor(5, 2);  // 返回 0 ，它是 5 的祖父节点
treeAncestor.getKthAncestor(6, 3);  // 返回 -1 因为不存在满足要求的祖先节点
```

**数据范围**：

- `1 <= k <= n <= 5 * 10^4`
- `parent[0] == -1` 表示编号为 `0` 的节点是根节点。
- 对于所有的 `0 < i < n` ，`0 <= parent[i] < n` 总成立
- `0 <= node < n`
- 至多查询 `5 * 10^4` 次

**思路**：

- 采用树上倍增算法**创建稀疏表**
- 针对每一个查询：给定节点 `node`，找出它的第 k 个祖先。依据稀疏表向上查找其第k个祖先。

```java
class TreeAncestor {
    int[][] f; // f[i][j]存储i节点的2^j倍父亲是哪个节点, f[i][0]是其父节点
    int[] lg;

    public TreeAncestor(int n, int[] parent) {
        this.f = new int[n + 10][22];
        this.lg = new int[n + 10];

        // 结点编号从0~n-1 => 1~n, 根结点的父节点编号为0
        for(int i = 1; i <= n; i ++ ) f[i][0] = parent[i - 1] + 1; // 初始化f数组
        for(int i = 2; i < lg.length; i ++ ) lg[i] = lg[i >> 1] + 1;

        for(int j = 1; j < 22; j ++ )
            for(int i = 1; i <= n; i ++ )
                f[i][j] = f[f[i][j - 1]][j - 1];
    }

    public int getKthAncestor(int node, int k) {
        int x = node + 1; // 结点编号从1开始
        while(k != 0) { // 例如 k = 7 = (111)2, 从x一直往上跳，直到跳到第k个祖先节点
            int t = lg[k];
            x = f[x][t];
            k = k - (int)Math.pow(2, t);
        }
        return x == 0 ? -1 : x - 1; // x = 0 表示不存在这样的祖先节点，返回 -1 
      
    }
}
```



### 10、特殊的树

#### （1）1382. 将二叉搜索树变平衡:writing_hand:  

**问题**

给你一棵二叉搜索树，请你返回一棵 **平衡后** 的二叉搜索树，新生成的树应该与原来的树有着相同的节点值。如果有多种构造方法，请你返回任意一种。

如果一棵二叉搜索树中，每个节点的两棵子树高度差不超过 `1` ，我们就称这棵二叉搜索树是 **平衡的** 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208181213198.jpeg" alt="img" width="370px" />

**思路一**：根据结点值，通过**左旋**和**右旋**，重新构建 **AVL 树**

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

    Map<TreeNode, Integer> h = new HashMap<>(); // 存储结点对应的高
    List<Integer> inorder = new ArrayList<>(); // 二叉树的中序序列

    public void update(TreeNode u) { // 更新以u为根结点的子树的高度
        h.put(u, Math.max(h.getOrDefault(u.left, 0), h.getOrDefault(u.right, 0)) + 1);
    }

    public TreeNode R(TreeNode u) { // 右旋
        TreeNode p = u.left;
        u.left = p.right; p.right = u;
        update(u); update(p);
        u = p;
        return u;
    }

    public TreeNode L(TreeNode u) { // 左旋
        TreeNode p = u.right;
        u.right = p.left; p.left = u;
        update(u); update(p);
        u = p;
        return u;
    }

    public int get_balance(TreeNode u) {
        return h.getOrDefault(u.left, 0) - h.getOrDefault(u.right, 0);
    }

    public TreeNode insert(TreeNode u, int w) {
        if(u == null) u = new TreeNode(w); // 结点不存在, 则新建一个结点
        else if(w < u.val) { // 满足二叉搜索树的性质，插入权值比根结点的权值小，向u的左子树插入
            u.left = insert(u.left, w);
            if(get_balance(u) == 2) { // u的左子树比右子树高2
                if(get_balance(u.left) == 1) u = R(u);
                else {
                    u.left = L(u.left);
                    u = R(u);
                }
            }
        }
        else { // 满足二叉搜索树的性质，插入权值大于等于根结点的权值，向u的右子树插入
            u.right = insert(u.right, w);
            if(get_balance(u) == -2) {
                if(get_balance(u.right) == -1) u = L(u);
                else {
                    u.right = R(u.right);
                    u = L(u);
                }
            }
        }
        update(u); // 更新高度(回溯)
        return u;
    }

    public void dfs(TreeNode u) { // 中序遍历
        if(u.left != null) dfs(u.left);
        inorder.add(u.val);
        if(u.right != null) dfs(u.right);
    }

    public TreeNode balanceBST(TreeNode root) {
        if(root == null) return root; // 特判
        
        dfs(root); // 求中序序列

        TreeNode rt = null; // 构建平衡二叉树
        for(Integer w : inorder) 
            rt = insert(rt, w);

        return rt;
    }
}
```

**思路二**：中序遍历二叉搜索树得到**有序数组**，根据有序数组**二分**重建**AVL树**

```java
class Solution {

    List<Integer> inorder = new ArrayList<>(); // 二叉树的中序序列

    public void dfs(TreeNode u) { // 中序遍历
        if(u.left != null) dfs(u.left);
        inorder.add(u.val);
        if(u.right != null) dfs(u.right);
    }

    public TreeNode build(List<Integer> arr, int l, int r) { // 有序数组构建平衡二叉树
        if(l > r) return null;

        int mid = l + r >> 1;
        TreeNode u = new TreeNode(arr.get(mid));
        u.left = build(arr, l, mid - 1);
        u.right = build(arr, mid + 1, r);

        return u;
    }

    public TreeNode balanceBST(TreeNode root) {
        if(root == null) return root;
        dfs(root);

        return build(inorder, 0, inorder.size() - 1);
    }
}
```

### 11、树的DFS

#### （1）2049. 统计最高分的节点数目

**问题**：

给你一棵根节点为 `0` 的 **二叉树** ，它总共有 `n` 个节点，节点编号为 `0` 到 `n - 1` 。同时给你一个下标从 **0** 开始的整数数组 `parents` 表示这棵树，其中 `parents[i]` 是节点 `i` 的父节点。由于节点 `0` 是根，所以 `parents[0] == -1` 。

一个子树的 大小 为这个子树内节点的数目。每个节点都有一个与之关联的 分数 。求出某个节点分数的方法是，将这个节点和与它相连的边全部 删除 ，剩余部分是若干个 非空 子树，这个节点的 分数 为所有这些子树 大小的乘积 。

请你返回有 **最高得分** 节点的 **数目** 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203112228894.png" alt="example-1" width="480px" />

```
输入：parents = [-1,2,0,2,0]
输出：3
```

**思路**： $O(n)$ 

- 在一棵树中，当把一个节点和与它相连的所有边删除，剩余部分最多为三棵非空子树，即原节点的左子树（如果有），右子树（如果有），以及把以这个节点为根结点的子树移除所形成的子树（除根结点外均有）。
- 而这个节点的分数为这些子树的节点个数的乘积。
- 我们可以先用 `parents` 数组统计出每个节点的子节点，然后使用深度优先搜索来计算以每个节点为根结点的子树的大小，同时计算每个节点的大小，作为深度优先搜索的返回值，最后统计最大分数出现的次数。
- 在实现上，统计最大分数出现的次数可以放到深度优先搜索中完成，从而节省一部分空间。

```java
class Solution {

    Map<Integer, int[]> hash = new HashMap<>(); // hash.get(i)存储i的左右孩子结点
    Map<Long, Integer> map = new HashMap<>(); // map.get(i) 表示得分i出现的次数
    long score = 0L; // 最高得分
    int res = 0; // 最高得分节点的数目
    int n;

    public int dfs(int x){ // 返回以x的根结点的子树的结点数
        if(!hash.containsKey(x)) { // x是叶子结点
            map.put((long)(n - 1), map.getOrDefault((long)(n - 1), 0) + 1); // n - 1 是x的得分
            if(n - 1 >= score) { // 更新最高分以及其出现的次数
                score = n - 1;
                res = map.get((long)(n - 1));
            }
            return 1;
        } 
    
        int[] kids = hash.get(x); // x的孩子结点
        int lcnt = 0, rcnt = 0; // 左子树的结点数和右子树的结点数
        if(kids[0] != -1) lcnt = dfs(kids[0]); // 求左子树
        if(kids[1] != -1) rcnt = dfs(kids[1]);  // 求右子树

        long k = 1;
        int cnt = 1 + rcnt + lcnt; // 以x的根结点的子树的结点数
        if(n - cnt > 0) k *= n - cnt; // 计算结点 u 的得分
        if(lcnt != 0) k *= lcnt;
        if(rcnt != 0) k *= rcnt;
        map.put(k, map.getOrDefault(k, 0) + 1);
        if(k >= score) { // 更新最高分以及其出现的次数
            score = k;
            res = map.get(k);
        }
        return cnt;
    }

    public int countHighestScoreNodes(int[] parents) {
        n = parents.length;
        for(int i = 1; i < n; i ++){
            int[] kids = hash.getOrDefault(parents[i], new int[]{-1, -1});
            if(kids[0] == -1) kids[0] = i;
            else kids[1] = i;
            hash.put(parents[i], kids);
        }

        dfs(0); // 从根结点开始构建树
        return res;
    }
}
```

#### （2）[1080. 根到叶路径上的不足节点](https://leetcode.cn/problems/insufficient-nodes-in-root-to-leaf-paths/)（树的DFS遍历）

**问题**：

给你二叉树的根节点 `root` 和一个整数 `limit` ，请你同时删除树中所有 **不足节点** ，并返回最终二叉树的根节点。

假如通过节点 `node` 的每种可能的 “根-叶” 路径上值的总和全都小于给定的 `limit`，则该节点被称之为 **不足节点** ，需要被删除。

**叶子节点**，就是没有子节点的节点。

例子：

<img src="https://assets.leetcode.com/uploads/2019/06/05/insufficient-11.png" alt="img" style="zoom:67%;" />

```
输入：root = [1,2,3,4,-99,-99,7,8,9,-99,-99,12,13,-99,14], limit = 1
输出：[1,2,3,4,null,null,7,8,9,null,14]
```

**数据范围**：

- 树中节点数目在范围 `[1, 5000]` 内
- `-10^5 <= Node.val <= 10^5`
- `-10^9 <= limit <= 10^9`

**思路**：此题考查的是树的DFS遍历，依据指定条件删除树中的节点。

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
    public boolean dfs(TreeNode u, int limit, int total) {
        if(u.left == null && u.right == null) { // 叶子结点
            if(total + u.val < limit) return true; // 删除此节点
            return false; // 不删除
        }

        if(u.left != null && dfs(u.left, limit, total + u.val)) u.left = null; // 删除左节点
        if(u.right != null && dfs(u.right, limit, total + u.val)) u.right = null; // 删除右节点
        // 每种可能的 “根-叶” 路径上值的总和全都小于给定的 limit
        if(u.left == null && u.right == null) return true; // 左右节点都删除了, 当前节点也要删除
        return false;
    }

    public TreeNode sufficientSubset(TreeNode root, int limit) {
        if(dfs(root, limit, 0)) root = null;
        return root;
    }
}
```



#### （3）[LCP 67. 装饰树](https://leetcode.cn/problems/KnLfVT/) （树的DFS遍历）

**问题**：

力扣嘉年华上的 DIY 手工展位准备了一棵缩小版的 **二叉** 装饰树 `root` 和灯饰，你需要将灯饰逐一插入装饰树中，要求如下：

- 完成装饰的二叉树根结点与 `root` 的根结点值相同
- 若一个节点拥有父节点，则在该节点和他的父节点之间插入一个灯饰（即插入一个值为 `-1` 的节点）。具体地：
  - 在一个 父节点 x 与其左子节点 y 之间添加 -1 节点，节点 -1、节点 y 为各自父节点的左子节点，
  - 在一个 父节点 x 与其右子节点 y 之间添加 -1 节点，节点 -1、节点 y 为各自父节点的右子节点，

现给定二叉树的根节点 `root` ，请返回完成装饰后的树的根节点。

例子：

```
输入：
root = [7,5,6]

输出：[7,-1,-1,5,null,null,6]

解释：如下图所示，
```

<img src="https://pic.leetcode-cn.com/1663575757-yRLGaq-image.png" alt="image.png" style="zoom:50%;" />

**数据范围**：

- $0 <= root.Val <= 1000$ 
- $root$ 节点数量范围为 $[1, 10^5]$

**思路**：此题考查树的DFS遍历，依据指定条件向树中增加树中的节点

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

    public void decorate(TreeNode p, TreeNode kid, boolean l) {
        TreeNode t = new TreeNode(-1);
        if(l) {
            t.left = kid;
            p.left = t;
        } else {
            t.right = kid;
            p.right = t;
        }
    }

    public void dfs(TreeNode u) {
        if(u.left != null) {
            dfs(u.left);
            decorate(u, u.left, true);
        }
        if(u.right != null) {
            dfs(u.right);
            decorate(u, u.right, false);
        }
    }

    public TreeNode expandBinaryTree(TreeNode root) {
        dfs(root);
        return root;
    }
}
```

#### （4）[979. 在二叉树中分配硬币](https://leetcode.cn/problems/distribute-coins-in-binary-tree/)

**问题**

给定一个有 `N` 个结点的二叉树的根结点 `root`，树中的每个结点上都对应有 `node.val` 枚硬币，并且总共有 `N` 枚硬币。

在一次移动中，我们可以选择两个相邻的结点，然后将一枚硬币从其中一个结点移动到另一个结点。(移动可以是从父结点到子结点，或者从子结点移动到父结点。)。

返回使每个结点上只有一枚硬币所需的移动次数。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/01/19/tree4.png" alt="img" style="zoom: 50%;" />

```
输入：[1,0,0,null,3]
输出：4
```

**数据范围**：

- `1<= N <= 100`
- `0 <= node.val <= N`

**思路**：

此题具有**节点个数等于所有节点的权值之和**的特点，可以采用dfs**在回溯时**计算移动次数。

在回溯时，设当前节点的权值是 $w$。如果想要当前节点的权值要等于$1$，就需要**减去** $w - 1$。

如果 $w - 1>0$，表示将 $w - 1$ 个硬币从当前节点移动到父节点，每次移动一个硬币，需要移动 $w-1$次。

如果 $w - 1<0$，表示将 $w - 1$ 个硬币从父节点移动到当前节点，每次移动一个硬币，需要移动 $abs(w-1)$次。

父节点的权值需要**加上** $w - 1$。

```java
class Solution {
    int cnt = 0;
    public int dfs(TreeNode u) {
        int res = u.val;
        if(u.left != null) res += dfs(u.left);
        if(u.right != null) res += dfs(u.right);

        if(1 != res) cnt += Math.abs(res - 1);
        return res - 1;
    }

    public int distributeCoins(TreeNode root) {
        dfs(root);
        return cnt;
    }
}
```

#### （5）1214. 查找两棵二叉搜索树之和

**问题**：

给出**两棵**二叉搜索树的根节点 `root1` 和 `root2` ，请你从两棵树中**各找出一个节点**，使得这两个节点的值之和等于目标值 `Target`。

如果可以找到返回 `True`，否则返回 `False`。

**解法一**：

**思路**：将问题转化为**两数之和**

- **中序遍历**求两个二叉树的中序序列
- 二叉搜索树的中序序列是有序序列，使用**二路归并**，得到一个有序序列
- 使用**双指针**算法求“两数之和”
- 用**二分查找**判断两个数是否分别位于两颗数中

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

    List<Integer> inl = new ArrayList<>();
    List<Integer> inr = new ArrayList<>();
    List<Integer> seq = new ArrayList<>();

    public void dfs(TreeNode u, List<Integer> inorder){ // 中序遍历二叉树, 求中序序列
        if(u.left != null) dfs(u.left, inorder);
        inorder.add(u.val);
        if(u.right != null) dfs(u.right, inorder);
    }

    public void merge(){ // 二路归并两个有序序列
        int i = 0, j = 0;
        while(i < inl.size() && j < inr.size()){
            int a = inl.get(i), b = inr.get(j);
            if(a < b) { seq.add(a); i ++; }
            else { seq.add(b); j ++; }
        }
        while(i < inl.size()) seq.add(inl.get(i ++));
        while(j < inr.size()) seq.add(inr.get(j ++));
    }

    public boolean bsearch(List<Integer> a, int x){ // 在有序序列a中二分查找 x
        int l = 0, r = a.size() - 1;
        while(l < r){
            int mid = l + r + 1>> 1;
            if(a.get(mid) > x) r = mid - 1;
            else l = mid;
        }
        if(l == r && a.get(l) == x) return true;
        else return false;
    }

    public boolean twoSumBSTs(TreeNode root1, TreeNode root2, int target) {
        dfs(root1, inl);
        dfs(root2, inr);
        merge();

        for(int i = 0, j = seq.size() - 1; i < j;){ // 双指针求有序序列中的两数之和
            int x = seq.get(i), y = seq.get(j);
            if(x + y < target) i ++ ;
            else if (x + y > target) j -- ;
            else {
                if((bsearch(inl, x) && bsearch(inr, y)) ||(bsearch(inr, x) && bsearch(inl, y)))
                    return true;
                else {
                    i ++;
                }
            } 
        }
        return false;
    }
}
```

**解法二**：:writing_hand: 

- **深度优先遍历**

```java
class Solution {

    public boolean dfs(TreeNode u, TreeNode v, int k){
        if(u == null || v == null) return false; // 无解
        if(u.val + v.val == k) return true; // 找到一个
        if(u.val + v.val > k) // 两数之和大于k，则缩小u或缩小v，递归所示其左子树(二叉搜索树的左子树比根结点小)
            return dfs(u.left, v, k) || dfs(u, v.left, k);
        else // 两数之和小于k，则增大u或增大v，递归所示其右子树(二叉搜索树的右子树比根结点大)
            return dfs(u.right, v, k) || dfs(u, v.right, k);
    }

    public boolean twoSumBSTs(TreeNode root1, TreeNode root2, int target) {
        return dfs(root1, root2, target);
    }
}
```

#### （6）1120. 子树的最大平均值

**问题**：

给你一棵二叉树的根节点 `root`，找出这棵树的每一棵子树的平均值中的最大值。

子树是树中的任意节点和它的所有后代构成的集合。

树的平均值是树中节点值的总和除以节点数。

**思路**：

- **递归**搜索所有子树，求每一颗子树的结点值之和以及结点数
- 取所有子树的平均值的最大值

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

    double avg = 0.0;

    public int[] dfs(TreeNode u){ // 前序遍历, 求每一个子树的结点值之和, 结点数
        int[] res = new int[]{u.val, 1}; // res[0],res[1]分别表示以u为根结点子树结点值之和, 结点数
        if(u.left != null) { // 求u的左子树
            int[] t = dfs(u.left);
            res[0] += t[0]; res[1] += t[1];
        }
        if(u.right != null) { // 求u的右子树
            int[] t = dfs(u.right);
            res[0] += t[0]; res[1] += t[1];            
        }
        avg = Math.max(avg, res[0] / (double) res[1]); // 求所有子树的平均值中的最大值
        return res;
    }

    public double maximumAverageSubtree(TreeNode root) {
        dfs(root);
        return avg;
    }
}
```

##### 



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
