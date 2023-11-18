# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>
> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第四部分

### 1、树

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208181220550.png" alt="image.png" width="600px" />

#### （I）前序遍历

###### （1）589. N 叉树的前序遍历

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

###### （2）144. 二叉树的前序遍历

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

#### （II）中序遍历

###### （1）285. 二叉搜索树中的中序后继

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

#### （III）后序遍历

###### （1）590. N 叉树的后序遍历

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

###### （2）145. 二叉树的后序遍历

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

#### （IV）层序遍历

###### （1）1302. 层数最深叶子节点的和

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

###### （2）637. 二叉树的层平均值

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

###### （3）102. 二叉树的层序遍历

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

###### （4）107. 二叉树的层序遍历 II

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

###### （5）429. N 叉树的层序遍历

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

###### （6）[T 秒后青蛙的位置](https://leetcode.cn/problems/frog-position-after-t-seconds/)

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



#### （V）二叉树的构建与修改

###### （1）6018. 根据描述创建二叉树

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

###### （2）889. 根据前序和后序遍历构造二叉树:writing_hand:  

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

###### （3）654. 最大二叉树

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

###### （4）[1110. 删点成林](https://leetcode.cn/problems/delete-nodes-and-return-forest/)

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





#### （VI）二叉搜索树的构建与修改

###### （1）1008. 前序遍历构造二叉搜索树

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

###### （2）538. 把二叉搜索树转换为累加树

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



#### （VII）二叉树的属性

###### （1）222. 完全二叉树的节点个数

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

###### （2）366. 寻找二叉树的叶子节点

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

###### （3）树的直径

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

###### （4）1522. N 叉树的直径

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

###### （5）543. 二叉树的直径

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

###### （6）742. 二叉树最近的叶节点

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



#### （VIII）二叉搜索树的属性

###### （1）[1373. 二叉搜索子树的最大键值和](https://leetcode.cn/problems/maximum-sum-bst-in-binary-tree/)

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

###### （2）510. 二叉搜索树中的中序后继 II:writing_hand: 

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

#### （IX）二叉树的公共祖先问题

###### （1）[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)（树上倍增算法）

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

###### （2）[235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)



###### （3）[1123. 最深叶节点的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-deepest-leaves/)（爬山法）

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

###### （4）[1483. 树节点的第 K 个祖先](https://leetcode.cn/problems/kth-ancestor-of-a-tree-node/) （树上倍增算法）

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



#### （X）特殊的树

###### （1）1382. 将二叉搜索树变平衡:writing_hand:  

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

#### （XI）树的DFS

###### （1）2049. 统计最高分的节点数目

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

###### （2）[1080. 根到叶路径上的不足节点](https://leetcode.cn/problems/insufficient-nodes-in-root-to-leaf-paths/)（树的DFS遍历）

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



###### （3）[LCP 67. 装饰树](https://leetcode.cn/problems/KnLfVT/) （树的DFS遍历）

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

###### （4）[979. 在二叉树中分配硬币](https://leetcode.cn/problems/distribute-coins-in-binary-tree/)

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

###### （5）1214. 查找两棵二叉搜索树之和

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

###### （6）1120. 子树的最大平均值

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

### 2、Tire 树

#### （1）14. 最长公共前缀

**问题**

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

**思路** **前缀树**

```java
class Solution {

    final int N = 40010;
    int[][] son = new int[N][26]; // 前缀树
    int[] cnt = new int[N];
    int idx = 0;

    List<Integer> arr = new ArrayList<>(); // 公共前缀对应的ASCII数组

    // 向前缀树中插入一个字符串
    public void insert(char[] str) {
        int p = 0;
        for(int i = 0; i < str.length; i ++ ) {
            int u = str[i] - 'a'; // 映射成ASCII
            if(son[p][u] == 0) son[p][u] = ++ idx; // 创建一个结点
            p = son[p][u];
        }
        cnt[p] ++ ;
    }

    // 编号为u的结点对应的字符为ch, 前缀树维护的字符串的个数为sum
    public void dfs(int u, int ch, int sum) { // 在前缀树中查找所有字符串的公共前缀
        if(u != 0) arr.add(ch);
        
        if(cnt[u] != 0 && cnt[u] < sum) return ; // 情况1：从根结点到u的路径是一个字符串
        
        int kids = 0;
        for(int i = 0; i < 26; i ++ )
            if(son[u][i] != 0) kids ++ ;
        if(kids == 0 || kids > 1 ) return ; // 情况2

        for(int i = 0; i < 26; i ++ ) // 继续查找公共前缀的下一个字符
            if(son[u][i] != 0) dfs(son[u][i], i, sum);
    }

    public String longestCommonPrefix(String[] strs) {
        for(int i = 0; i < strs.length; i ++ ) {
            if("".equals(strs[i])) return ""; // 特判
            insert(strs[i].toCharArray());
        }

        dfs(0, -1, strs.length);
        String prefix = "";
        for(int i = 0; i < arr.size(); i ++ ) { // ASCII数组转换成字符串
            char ch = (char)(arr.get(i) + 'a');
            prefix = prefix + ch;
        }
        return prefix;
    }
}
```

### 3、线段树

#### （1）LCP 09. 最小跳跃次数:writing_hand: 

**问题**：

为了给刷题的同学一些奖励，力扣团队引入了一个弹簧游戏机。游戏机由 `N` 个特殊弹簧排成一排，编号为 `0` 到 `N-1`。

初始有一个小球在编号 `0` 的弹簧处。若小球在编号为 `i` 的弹簧处，通过按动弹簧，可以选择把小球向右弹射 `jump[i]` 的距离，或者向左弹射到**任意左侧弹簧的位置**。

也就是说，在编号为 `i` 弹簧处按动弹簧，小球可以弹向 `0` 到 `i-1` 中任意弹簧或者 `i+jump[i]` 的弹簧（若 `i+jump[i]>=N` ，则表示小球弹出了机器）。小球位于编号 0 处的弹簧时不能再向左弹。

为了获得奖励，你需要将小球弹出机器。请求出**最少**需要按动多少次弹簧，可以将小球从编号 `0` 弹簧弹出整个机器，即向右越过编号 `N-1` 的弹簧。

**简化后的问题**：给定一个数组 `jump`，长度为 `N`，在第 `i` 个位置可以选择跳到 `0..i-1` 和 `i + jump[i]`，问从 `0` 跳过 `n-1` 的最小跳跃次数是多少？

**数据范围**：

- `1 <= jump.length <= 10^6`
- `1 <= jump[i] <= 10000`

**思路**：

- 考虑第 `i` 个位置，可以**直接跳**到 `i+jump[i]`, 也可以**跳两步**（即先跳到$i+jump[i]$，再往回跳）跳到所有的 `i+1 .. i+jump[i]-1`。
- 每一个元素`f[i]`表示从`1`位置跳到`i`位置的最少跳数。
- 向右跳：`f[i+jump[i]] = min(f[i + jump[i]], f[i] + 1)` 。
- 向左跳：`f[j] = min(f[j], f[i] + 2), i < j < i+jump[i]` 。

用**线段树**来实现区间更新，单点查询。线段树某个节点的区间维护的是数组$f$的区间最小值。

```java
class Node{ // 线段树的结点, 维护一段区间的信息, jump 和 minj初始化都为0x3f3f3f3f
    int l,r; // 区间的左右边界
    int jump; // 表示从1位置跳到[l,r]中的任意一个位置的最少的跳数
    int minj; // 懒标记
    Node(int l, int r){ this.l = l; this.r = r; this.minj = 0x3f3f3f3f;}
    Node(int l, int r, int jump){ this.l = l; this.r = r; this.jump = jump;this.minj = 0x3f3f3f3f;}
}

class Solution {
    final int INF = 0x3f3f3f3f;
    Node[] tr; // 线段树
    int[] f; // 权值序列，权值序列的每一个元素f[i]表示从1位置跳到该位置的最少跳数
    int n; // 权值序列的实际元素个数

    public void pushUp(int u){
        tr[u].jump = Math.min(tr[u << 1].jump, tr[u << 1|1].jump);
    }

    public void pushDown(int u){ // 下传标记
        if(tr[u].l == tr[u].r) {tr[u].minj = INF; return ;} // 叶子结点，清流标记
        tr[u << 1].jump = Math.min(tr[u << 1].jump, tr[u].minj);
        tr[u << 1|1].jump = Math.min(tr[u << 1|1].jump, tr[u].minj);
        tr[u << 1].minj = Math.min(tr[u << 1].minj, tr[u].minj);
        tr[u << 1|1].minj = Math.min(tr[u << 1|1].minj, tr[u].minj);
        tr[u].minj = INF; // 清空标记
    }

    public void build(int u, int l, int r){
        if(l == r) tr[u] = new Node(l,r,f[l]);
        else{
            tr[u] = new Node(l,r);
            int mid = l + r >> 1;
            build(u << 1, l, mid);
            build(u << 1|1, mid + 1, r);
            pushUp(u);
        }
    }

    public int query(int u, int x){ // 单点查询
        if(tr[u].l == tr[u].r) return tr[u].jump;
        else{
            pushDown(u); // 递归查询前, 先下传标记
            int mid = tr[u].l + tr[u].r >> 1;
            if(x <= mid) return query(u << 1, x);
            else return query(u << 1|1, x);
        }
    }

    public void modify(int u, int l, int r, int v){ // 区间更新
        if(tr[u].l >= l && tr[u].r <= r) {
            tr[u].jump = Math.min(tr[u].jump, v);
            tr[u].minj = Math.min(tr[u].minj, v);
        }else{
            pushDown(u);
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, v);
            if(r > mid) modify(u << 1|1, l, r, v);
            // pushUp(u); // 单点查询时此处无需更新父结点
        }
    }

    public int minJump(int[] jump) {
        n = jump.length;
        tr = new Node[4*(n+1)];
        f = new int[n + 1];
        Arrays.fill(f, INF); // 初始化为无穷大表示无法到达
        build(1, 1, n);
        modify(1, 1, 1, 0); // 从1位置到达1位置最小跳数为0

        int res = INF;
        for(int i = 0; i < n; i ++){
            int cnt = query(1, i + 1); // 从0到达i位置最少跳多少次
            if(i + jump[i] >= n) res = Math.min(res, cnt + 1); // 再跳一次就跳出机器
            else{ 
                modify(1, i+jump[i]+1,i+jump[i]+1, cnt + 1);
                if(i+1+1 <= i+jump[i]-1+1) modify(1, i+1+1, i+jump[i]-1+1, cnt + 2);
            }
        }
        return res;
    }
}
```

#### （2）[LCP 52. 二叉搜索树染色](https://leetcode.cn/problems/QO5KpG/)

**问题**：

欢迎各位勇者来到力扣城，本次试炼主题为「二叉搜索树染色」。

每位勇士面前设有一个**二叉搜索树**的模型，模型的根节点为 `root`，树上的各个节点值均不重复。初始时，所有节点均为蓝色。现在按顺序对这棵二叉树进行若干次操作， `ops[i] = [type, x, y]` 表示第 `i` 次操作为：

- `type` 等于 0 时，将节点值范围在 `[x, y]` 的节点均染蓝
- `type` 等于 1 时，将节点值范围在 `[x, y]` 的节点均染红

请返回完成所有染色后，该二叉树中红色节点的数量。

例子：

```
输入：root = [4,2,7,1,null,5,null,null,null,null,6]
ops = [[0,2,2],[1,1,5],[0,4,5],[1,5,7]]

输出：5

解释：
第 0 次操作，将值为 2 的节点染蓝；
第 1 次操作，将值为 1、2、4、5 的节点染红；
第 2 次操作，将值为 4、5 的节点染蓝；
第 3 次操作，将值为 5、6、7 的节点染红；
因此，最终值为 1、2、5、6、7 的节点为红色节点，返回数量 5
```

<img src="https://pic.leetcode-cn.com/1649833763-BljEbP-image.png" alt="image.png" style="zoom:50%;" />



**注意：**

- 题目保证对于每个操作的 `x`、`y` 值定出现在二叉搜索树节点中

数据范围：

- `1 <= 二叉树节点数量 <= 10^5`
- `1 <= ops.length <= 10^5`
- `ops[i].length == 3`
- `ops[i][0]` 仅为 `0` or `1`
- `0 <= ops[i][1] <= ops[i][2] <= 10^9`
- `0 <= 节点值 <= 10^9`

**思路**：区间更新和区间查询

二叉搜索树的节点值不同，其数据范围是`0 <= 节点值 <= 10^9`，节点个数是`1 <= 二叉树节点数量 <= 10^5`，首先需要对节点值进行离散化处理。将所有节点值从小到大排序后，离散化到1~n之间。

线段树某个节点的区间维护的是该区间内的红色节点的数量。

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

class Node { // 定义线段树
    int l, r; // 维护区间的左右边界 
    int sum; // 维护区间中红色节点的数量
    int tadd = -1; // 懒标签, 0-表示将区间中所有节点染成蓝色，1表示染成红色, -1表示懒标记清空
    Node(int ll, int rr) {l = ll; r = rr;}
    Node(int ll, int rr, int s) {l = ll; r = rr; sum = s;}
}

class Solution {

    List<Integer> arr = new ArrayList<>();
    Map<Integer, Integer> map = new HashMap<>();
    
    int n;
    int[] w; // 权值序列, w[i]=0表示节点i是蓝色
    Node[] tr; // 线段树是一颗完全二叉树，用一个一维数组来存储

    public void dfs(TreeNode u) {
        if(u.left != null) dfs(u.left);
        if(u.right != null) dfs(u.right);
        arr.add(u.val);
    }

    public void pushup(int u) { // 用u的左右孩子更新u的信息
        tr[u].sum = tr[u << 1].sum + tr[u << 1 | 1].sum;
    }

    public void pushdown(int u) { // 懒标记下传，只下传一层。依据懒标记的信息，更新u的左右子节点
        if(tr[u].tadd == -1) return; // u的懒标签是清空状态
        if(tr[u].l == tr[u].r) {tr[u].tadd = -1; return;} // 如果u是叶子节点，它没有子节点，直接清空懒标记
        tr[u << 1].sum = (tr[u << 1].r - tr[u << 1].l + 1) * tr[u].tadd; // 左子树
        tr[u << 1 | 1].sum = (tr[u << 1 | 1].r - tr[u << 1 | 1].l + 1) * tr[u].tadd; // 右子树
        tr[u << 1].tadd = tr[u].tadd; // 更新左子树tadd
        tr[u << 1 | 1].tadd = tr[u].tadd; // 更新右子树的tadd
        tr[u].tadd = -1; // 清空自己懒标记
    }

    public void build(int u, int l, int r) { // 用权值序列w的区间[l,r]构建以u为根节点的子树
        if(l == r) tr[u] = new Node(l, r, w[l]); // 如果[l,r]是单个元素，对应的节点是叶子节点，则直接构建
        else {
            tr[u] = new Node(l, r);
            int mid = l + r >> 1;
            build(u << 1, l, mid); // 构建u的左子树
            build(u << 1 | 1, mid + 1, r); // 构建u的右子树
            pushup(u);
        }
    }

    // 在更新之前，先将之前的懒标记下传pushdown(u) ->递归更新modify(u) -> pushup(u)
    public void modify(int u, int l, int r, int v) { // 将区间[l,r]之间的节点都染成v, 0是蓝色, 1是红色
        if(tr[u].l >= l && tr[u].r <= r) { // 如果u表示的区间包含在修改区间[l,r]中
            tr[u].sum = (tr[u].r - tr[u].l + 1) * v;
            tr[u].tadd = v; // 更新懒标记
        } else {
            pushdown(u); // 懒标记下传
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, v);
            if(r > mid) modify(u << 1 | 1, l, r, v);
            pushup(u);
        }
    }

    public int query(int u, int l, int r) {
        if(tr[u].l >= l && tr[u].r <= r) return tr[u].sum; // u节点表示的区间包含在查询区间[l,r]
        pushdown(u);
        int res = 0;
        int mid = tr[u].l + tr[u].r >> 1;
        if(l <= mid) res += query(u << 1, l, r);
        if(r > mid) res += query(u << 1 | 1, l, r);
        return res;
    }

    public int getNumber(TreeNode root, int[][] ops) {
        // 离散化
        dfs(root);
        Collections.sort(arr);
        for(int i = 0; i < arr.size(); i ++ ) map.put(arr.get(i), i + 1); // 将所有节点值离散化到1~n

        n = arr.size();
        w = new int[n + 1]; // 所有借点初始状态都是蓝色0, 下标从1开始
        tr = new Node[4 * n];

        // 构建线段树
        build(1, 1, n); // 完全二叉树的根节点编号必须从1开始

        // 区间修改
        for(int i = 0; i < ops.length; i ++ ) {
            int v = ops[i][0], l = ops[i][1], r = ops[i][2];
            modify(1, map.get(l), map.get(r), v);
        }

        return query(1, 1, n);
    }
}
```

#### （3）[6468. 统计没有收到请求的服务器数目](https://leetcode.cn/problems/count-zero-request-servers/)

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

- `1 <= n <= 60`
- `1 <= logs.length <= 10^5`
- `1 <= queries.length <= 10^5`
- `logs[i].length == 2`
- **`1 <= logs[i][0] <= n`**
- `1 <= logs[i][1] <= 10^6`
- `1 <= x <= 10^5`
- `x < queries[i] <= 10^6`

**思路**：

题目给定了一组日志记录 $logs$，$logs[i]$表示编号为$logs[i][0]$的服务器在时刻$logs[i][1]$收到了一个请求。服务器的集群数量为 $n, 1<=n<=60$。给定一组查询$queries$，要求查询时间区间 $[queries[i]-x, queries[i]]$内没有收到请求的服务器。

所有问题可以看做一个**单点修改、区间查询**的问题。考虑使用线段树或者树状数组来解决

其中，线段树的节点维护了一段时间区间，以及在该时间区间内收到请求的服务器编号的集合，**该集合包含不重复的元素**。

在线段树的单点修改和区间查询涉及到**集合合并操作**。设单点修改的数量为 $P$，查询的个数为 $K$，涉及到总时间区间长度为 $D$，在**不考虑集合合并操作的时间复杂度下**，总的时间复杂度为 $O(PlogD+KlogD)$。

为了使得集合合并操作复杂度尽可能的小，在服务器规模 $n$ 比较小的情况下，我们可以考虑**采用二进制表示集合**，**位运算实现集合合并和新增元素操作**，这些集合操作复杂度为 $O(1)$。`long`的数据范围为$2^{64}$，因此二进制可以表示的集合大小为 $64$。

但是，在服务器规模 $n$ 比较大的情况下，即使使用大整数实现二进制表示集合，但是其集合操作时间复杂度不是常数复杂度，使得算法总的复杂度较高，此时可以考虑使用其他算法来解决此问题。

```java
class Node {
    int l, r;
    long sum; // 二进制表示集合
    Node(int ll, int rr) {l = ll; r = rr;}
    Node(int ll, int rr, int s) {l = ll; r = rr; sum = s;}
}

class Solution {
    
    int N = 0;
    int[] w;
    Node[] tr;
    
    public void pushup(int u) {
        tr[u].sum = tr[u << 1].sum | tr[u << 1 | 1].sum; // 集合合并
    }
    
    public void build(int u, int l, int r) {
        if(l == r) tr[u] = new Node(l, r, w[l]);
        else {
            tr[u] = new Node(l, r);
            int mid = l + r >> 1;
            build(u << 1, l, mid);
            build(u << 1 | 1, mid + 1, r);
            pushup(u);
        }
    }
    
    public void modify(int u, int x, int v) {
        if(tr[u].l == tr[u].r &&(((tr[u].sum >> v) & 1) == 0)) tr[u].sum += 1 << v; // 如何集合中不包此元素，则向集合中添加元素
        else {
            int mid = tr[u].l + tr[u].r >> 1;
            if(x <= mid) modify(u << 1, x, v);
            else modify(u << 1 | 1, x, v);
            pushup(u);
        }
    }
    
    public long query(int u, int l, int r) {
        if(tr[u].l >= l && tr[u].r <= r) return tr[u].sum;
        int mid = tr[u].l + tr[u].r >> 1;
        long res = 0; // 集合合并
        if(l <= mid) res |= query(u << 1, l, r);
        if(r > mid) res |= query(u << 1 | 1, l, r);
        return res;
    }
    
    public int count(long x) {
        int res = 0;
        while(x != 0) {
            if((x & 1) == 1) res ++;
            x >>= 1;
        }
        return res;
    }
           
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        for(int i = 0; i < logs.length; i ++ ) N = Math.max(N, logs[i][1]);
        for(int i = 0; i < queries.length; i ++) N = Math.max(N, queries[i]);
        
        w = new int[N + 10];
        tr = new Node[4 * (N + 10)];
        
        build(1, 1, w.length - 1);
        
        for(int i = 0;  i < logs.length; i ++ ) {
            int v = logs[i][0], y = logs[i][1];
            modify(1, y, v);
        }

        int[] res = new int[queries.length];
        for(int i = 0; i < queries.length; i ++ ) 
            res[i] = n - count(query(1, queries[i] - x, queries[i]));
        return res;
    }
}
```

#### （4）[2569. 更新数组后处理求和查询](https://leetcode.cn/problems/handling-sum-queries-after-update/) 

### 4、树状数组

#### （I）一维树状数组

###### （1）307. 区域和检索 - 数组可修改

**问题**：

给你一个数组 `nums` ，请你完成两类查询。

- 其中一类查询要求 **更新** 数组 `nums` 下标对应的值
- 另一类查询要求返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** ，其中 `left <= right` 

实现 `NumArray` 类：

- `NumArray(int[] nums)` 用整数数组 `nums` 初始化对象
- `void update(int index, int val)` 将 `nums[index]` 的值 **更新** 为 `val` 
- `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** （即，`nums[left] + nums[left + 1], ..., nums[right]`）

**思路**：树状数组模板题

```java
class NumArray {

    int n;
    int[] a, tr; // a是原数组, tr是树状数组

    public int lowbit(int x) { // 返回x末尾0的个数
        return x & -x;
    }

    public void add(int x, int v) { // 在原数组x的位置加上v， 同时更新树状数组
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) { // 返回原数组[0,x]之间所有元素的和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public NumArray(int[] nums) {
        n = nums.length;
        tr = new int[n + 10];
        a = nums;
        for(int i = 1; i <= n; i ++ ) add(i, nums[i - 1]); // 用nums数组初始化树状数组
    }
    
    public void update(int index, int val) { // 单点更新
        int x = index + 1;
        add(x, val - a[index]);
        a[index] =  val;
    }
    
    public int sumRange(int left, int right) { // 区间求和
        int l = left + 1, r = right + 1;
        return query(r) - query(l - 1);
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(index,val);
 * int param_2 = obj.sumRange(left,right);
 */
```

###### （2）683. K 个关闭的灯泡

**问题**：

`n` 个灯泡排成一行，编号从 `1` 到 `n` 。最初，所有灯泡都关闭。每天 **只打开一个** 灯泡，直到 `n` 天后所有灯泡都打开。

给你一个长度为 `n` 的灯泡数组 `blubs` ，其中 `bulls[i] = x` 意味着在第 `(i+1)` 天，我们会把在位置 `x` 的灯泡打开，其中 `i` **从 0 开始**，`x` **从 1 开始**。

给你一个整数 `k` ，请返回恰好有两个打开的灯泡，且它们中间 **正好** 有 `k` 个 **全部关闭的** 灯泡的 **最小的天数** 。如果不存在这种情况，返回 `-1` 。

**思路**：

对于`bulbs[i] = x`：

- 检查 `x` 前后间隔 `k` 个灯泡的灯泡亮没亮
- `x - 1`和 `x - k - 1`这两个位置的前缀和是否相等，即中间间隔的 `k` 个灯是否都是灭的。同时`x - k - 1` 与 `x - k - 1 - 1` 位置的前缀和应该相差 `1`，即两端的灯应该是亮着
- `x` 和 `x + k` 这两个位置的前缀和是否相等，即中间间隔的 `k` 个灯是否都灭了。同时`x + k + 1` 与 `x` 位置的前缀和应该相差 `1`，即两端的灯应该是亮着

```java
class Solution {

    int n;
    int[] tr; // 树状数组

    public int lowbit(int x) { // lowbit(x)返回x末尾1的个数
        return x & -x;
    }

    public void add(int x, int v) { // 将元素组x的位置的元素加上v
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) { // 查询[1, x]的区间和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public int kEmptySlots(int[] bulbs, int k) {
        n = bulbs.length;
        tr = new int[n + 10];
        for(int i = 0; i < bulbs.length; i ++ ) {
            int x = bulbs[i];
            add(x, 1);

            if(x + k + 1 <= n) { // 恰好有两个打开的灯泡, 其中间[x-k, x-1] 之间的灯泡全部熄灭
                int a = query(x), b = query(x + k), c = query(x + k + 1);
                if(a == b && c - a == 1) return i + 1;
            } 
            if(x - k - 1 >= 1) {  // 恰好有两个打开的灯泡, 其中间[x-k, x-1] 之间的灯泡全部熄灭
                int a = query(x - k - 1 - 1), b = query(x - k - 1), c = query(x - 1);
                if(b - a == 1 && b == c) return i + 1;
            }
        }
        return -1;
    }
}
```

#### （II）离散化——树状数组

###### （1）剑指 Offer 51. 数组中的逆序对:writing_hand: 

**思路**：

- 将`nums`数组的元素进行去除排序，映射到`1~n`
- 原数组`a[k]`存储`nums[i]`出现的次数，树状数组`tr[k]`支持单点修改并维护前缀和
- 从后向前枚举`nums`数组。`nums[i]`对应的原数组为`a[k]`，更新`nums[i]`出现的次数，并求出前缀和，即比`nums[i]`元素之后比`nums[i]`小的元素个数，也即逆序对的个数

```java
class Solution {
    int[] tr; // 树状数组
    int n;
    Map<Integer,Integer> mp = new HashMap<>();

    public int lowbit(int x){ // lowbit(x) = 2^k k是x二进制表示末尾0的个数
        return x & -x;
    }

    public void add(int x, int v){ // 单点修改
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    } 

    public int query(int x){ // 区间查询
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public int reversePairs(int[] nums) {
        n = nums.length;
        tr = new int[n + 1]; // 树状数组下标从1开始
        // 对nums数组元素去重并排序
        Set<Integer> set = new TreeSet<>();
        for(int i = 0; i < n; i ++) set.add(nums[i]); 
        // 对元素进行离散化
        int idx = 1; // 映射到 1~k
        for(Integer i : set) mp.put(i, idx ++);

        int res = 0;
        for(int i = n - 1; i >= 0; i --){ // 从后向前枚举nums数组
            int k = mp.get(nums[i]); // 得到nums[i]的映射值
            add(k, 1); // 原数组a[k]存储了nums[i]元素的个数, 更新原素组, 同时更新树状数组
            res += query(k - 1); // 查询nums[i]后面比它小的元素个数, 即与其构成逆序对的个数
        }
        return res;
    }

}
```

###### （2）315. 计算右侧小于当前元素的个数

**问题**：

给你一个整数数组 `nums` ，按要求返回一个新数组 `counts` 。数组 `counts` 有该性质： `counts[i]` 的值是 `nums[i]` 右侧小于 `nums[i]` 的元素的数量。

**思路**：此题与上一题求数组中逆序对的个数相似

```java
class Solution {
    
    int n; 
    int[] tr; // 树状数组
    
    public int lowbit(int x) {
        return x & -x;
    }

    public void add(int x, int v) {
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) {
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public List<Integer> countSmaller(int[] nums) {
        // 将所有数从小到大排序, 去重, 离散化
        TreeSet<Integer> unique  = new TreeSet<>();
        for(int val : nums) unique.add(val);
        HashMap<Integer, Integer> hash = new HashMap<>();
        int idx = 1;
        for(Integer val : unique) hash.put(val, idx ++ );

        n = unique.size();
        tr = new int[n + 10];
        
        // 从右往左遍历nums[i], 依次统计nums[i] 右侧小于 nums[i] 的元素的数量
        LinkedList<Integer> res = new LinkedList<>();
        for(int i = nums.length - 1; i >= 0; i -- ) {
            int t = hash.get(nums[i]); // t 是 nums[i] 映射后的值
            res.addFirst(query(t - 1)); // 求 nums[i] 右侧小于 nums[i] 的元素的数量
            add(t, 1);
        }
        return res;
    }
}
```



#### （III）前缀和离散化——树状数组

###### （1）327. 区间和的个数:writing_hand: 

**问题**：

给你一个整数数组 `nums` 以及两个整数 `lower` 和 `upper` 。求数组中，值位于范围 `[lower, upper]` （包含 `lower` 和 `upper`）之内的 **区间和的个数** 。

**区间和** `S(i, j)` 表示在 `nums` 中，位置从 `i` 到 `j` 的元素之和，包含 `i` 和 `j` (`i` ≤ `j`)。

**注意**：`-231 <= nums[i] <= 231 - 1` 

**思路**：

设前缀和数组为 $s$ ，则问题等价于求所有的下标对$(i,j)$，满足下列等式的个数：
$$
s[j]−s[i]∈[lower,upper]
$$
对于每个下标 $j$，以 $j$ 为右端点的满足条件的下标对的数量，就等于数组 $[0..j−1]$ 中的所有整数，出现在区间 $[s[j]−upper,s[j]−lower]$ 的次数。

所以可以考虑使用树状数组

具体地：

- 对原数组求前缀和，得到前缀和数组
- 对所有的前缀和、前缀和与`upper`的差、前缀和与`lower`的差进行离散化
  - 从小到大排序 
  - 去重
  - 从下标1开始映射
- 从左往右遍历前缀和数组的每一个元素$s[j]$，先进行在树状数组中区间查询 $[s[j]−upper,s[j]−lower]$ ，然后在单点更新 $s[j]$

```java
class Solution {

    int n; // 树状数组实际元素个数
    long[] s;
    int[] tr; // s 是前缀和数组, tr是树状数组, s和tr下标都是从1开始

    public int lowbit(int x) { // lowbit(x)返回x末尾1的个数
        return x & -x;
    }

    public void add(int x, int v) { // 将元素组x的位置的元素加上v
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) { // 查询[1, x]的区间和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res += tr[i];
        return res;
    }

    public int countRangeSum(int[] nums, int lower, int upper) {
        s = new long[nums.length + 10]; 

        // 求前缀和
        for(int i = 1; i <= nums.length; i ++ ) s[i] = s[i - 1] + nums[i - 1];

        // 将前缀和离散化到 1 ~ k
        TreeSet<Long> ts = new TreeSet<>();
        for(int i = 0; i <= nums.length; i ++ ) { // 从小到大排序并去重, s[0] 也需要加入
            ts.add(s[i]); 
            ts.add(s[i] - lower);
            ts.add(s[i] - upper); // s[i] - upper 和 s[i] - lower 也需要加入进行离散化
        }
        int idx = 1;
        HashMap<Long, Integer> hash = new HashMap<>();
        for(Long val : ts) hash.put(val, idx ++); // 从1开始映射
        
        n = ts.size(); // n为树状数组的实际大小
        tr = new int[n + 10];
        
        int res = 0;
        for(int i = 0; i <= nums.length; i ++) { // 从前往后枚举前缀和数组, s[0]也需要加入
            //  query(r)是和为前缀和为s[i] - lower出现的次数
            int l = hash.get(s[i] - upper), r = hash.get(s[i] - lower);
            res += query(r) - query(l - 1);
            add(hash.get(s[i]), 1);
        }

        return res;
    }
}
```

###### （2）2031. 1 比 0 多的子数组个数

**问题**：

给你一个只包含 `0` 和 `1` 的数组 `nums`，请返回 `1` 的数量 **大于** `0` 的数量的子数组的个数。由于答案可能很大，请返回答案对 `109 + 7` **取余** 的结果。

一个 **子数组** 指的是原数组中连续的一个子序列。

思路：

- 将原数组中的所有的 `0` 都换成 `-1`
- 设其前缀和数组为 $s$， 对于每个下标 $j$，以 $j$ 为右端点的满足条件的子数组的个数(`1` 的数量 **大于** `0` 的数量的子数组的个数)，等价于求 在 $j$ 左边比 $s[j]$ 小的前缀和的数量
- 可以考虑使用树状数组

具体的：

- 对原数组求前缀和，得到前缀和数组
- 对所有的前缀和进行离散化
  - 从小到大排序 
  - 去重
  - 从下标1开始映射
- 从左往右遍历前缀和数组的每一个元素$s[j]$，先进行在树状数组中区间查询 ，然后在单点更新 $s[j]$

```java
class Solution {

    final int MOD = (int)1e9 + 7;
    int n;
    int[] tr; // 树状数组

    public int lowbit(int x) {
        return x & -x;
    }

    public void add(int x, int v) {
        for(int i = x; i <= n; i += lowbit(i)) tr[i] += v;
    }

    public int query(int x) {
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i)) res = (res + tr[i]) % MOD;
        return res;
    }

    public int subarraysWithMoreZerosThanOnes(int[] nums) {
        int[] s = new int[nums.length + 10]; // 前缀和数组

        for(int i = 0; i < nums.length; i ++ ) // 将数组中所有的0替换成-1
            if(nums[i] == 0) nums[i] = -1;
        for(int i = 1; i <= nums.length; i ++ ) s[i] = s[i - 1] + nums[i - 1]; // 求前缀和

        // 所有前缀和和从小到大排序, 去重, 离散化
        TreeSet<Integer> unique = new TreeSet<>();
        for(int i = 0; i <= nums.length; i ++ ) unique.add(s[i]);
        int idx = 1;
        HashMap<Integer, Integer> hash = new HashMap<>();
        for(Integer val : unique) hash.put(val, idx ++ );

        n = unique.size();
        tr = new int[n + 10];
        
        int res = 0;
        for(int i = 0; i <= nums.length; i ++ ) {
            int t = hash.get(s[i]);
            res = (res + query(t - 1)) % MOD;
            add(t, 1);
        }
        return res;
    }
}
```



#### （IV）二维树状数组

###### （1）308. 二维区域和检索 - 可变:writing_hand: 

**问题**：

给你一个二维矩阵 `matrix` ，你需要处理下面两种类型的若干次查询：

- **更新：**更新 `matrix` 中某个单元的值。
- **求和：**计算矩阵 `matrix` 中某一矩形区域元素的 **和** ，该区域由 **左上角** `(row1, col1)` 和 **右下角** `(row2, col2)` 界定。

实现 `NumMatrix` 类：

- `NumMatrix(int[][] matrix)` 用整数矩阵 `matrix` 初始化对象。
- `void update(int row, int col, int val)` 更新 `matrix[row][col]` 的值到 `val` 。
- `int sumRegion(int row1, int col1, int row2, int col2)` 返回矩阵 `matrix` 中指定矩形区域元素的 **和** ，该区域由 **左上角** `(row1, col1)` 和 **右下角** `(row2, col2)` 界定。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203162232909.jpeg" alt="img" width="500px" />

**思路**：与一维前缀和类似，二维前缀和模板题

```java
class NumMatrix {

    int m, n; // 子矩阵 m 行, n 列 
    int[][] a, tr; // a表示原数组, tr表示树状数组

    public int lowbit(int x) { // 返回x的末尾0的个数
        return x & -x;
    }

    public void add(int x, int y, int v) { // 将原数组(x,y)更新为val, 同时更新tr
        for(int i = x; i <= m; i += lowbit(i)) 
            for(int j = y; j <= n; j += lowbit(j))
                tr[i][j] += v;
    }   

    public int query(int x, int y) { // 查询 (1,1) 到 (x, y) 构成的子矩阵的和
        int res = 0;
        for(int i = x; i != 0; i -= lowbit(i))
            for(int j = y; j != 0; j -= lowbit(j))
                res += tr[i][j];
        return res;
    }

    public NumMatrix(int[][] matrix) {
        m = matrix.length; n = matrix[0].length;
        tr = new int[n + 10][n + 10];
        a = new int[n + 10][n + 10];
        for(int i = 0; i < m; i ++ )
            for(int j = 0; j < n; j ++){
                a[i + 1][j + 1] = matrix[i][j];
                add(i + 1, j + 1, matrix[i][j]); // 初始化树状数组
            }
    }
    
    public void update(int row, int col, int val) { // 单点修改
        int x = row + 1, y = col + 1;
        add(x, y, val - a[x][y]);
        a[x][y] = val;
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) { // 区间查询
        int x1 = row1 + 1, y1 = col1 + 1, x2 = row2 + 1, y2 = col2 + 1;
        return query(x2, y2) - query(x2, y1 - 1) - query(x1 - 1, y2) + query(x1 - 1, y1 - 1);
    }
}
```

### 5、扫描线

#### （1）[1094. 拼车](https://leetcode.cn/problems/car-pooling/)



#### （2）[1851. 包含每个查询的最小区间](https://leetcode.cn/problems/minimum-interval-to-include-each-query/)



#### （3）[850. 矩形面积 II](https://leetcode.cn/problems/rectangle-area-ii/)（结合线段树）

**问题**

给你一个轴对齐的二维数组 `rectangles` 。 对于 `rectangle[i] = [x1, y1, x2, y2]`，其中（x1，y1）是矩形 `i` 左下角的坐标， `(xi1, yi1)` 是该矩形 **左下角** 的坐标， `(xi2, yi2)` 是该矩形 **右上角** 的坐标。

计算平面中所有 `rectangles` 所覆盖的 **总面积** 。任何被两个或多个矩形覆盖的区域应只计算 **一次** 。

返回 ***总面积*** 。因为答案可能太大，返回 `10^9 + 7` 的 **模** 。

例子

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/06/rectangle_area_ii_pic.png" alt="img" style="zoom: 33%;" />

```
输入：rectangles = [[0,0,2,2],[1,0,2,3],[1,0,3,1]]
输出：6
解释：如图所示，三个矩形覆盖了总面积为 6 的区域。
从(1,1)到(2,2)，绿色矩形和红色矩形重叠。
从(1,0)到(2,3)，三个矩形都重叠。
```

**数据范围**：

- `1 <= rectangles.length <= 200`
- `rectanges[i].length = 4`
- `0 <= xi1, yi1, xi2, yi2 <= 10^9`

**思路**：离散化+二分+线段树+扫描线，模板题

```java
class Segment {
    int x, y1, y2;
    int k;
    public Segment(int x, int y1, int y2, int k) {
        this.x = x;
        this.y1 = y1;
        this.y2 = y2;
        this.k = k;
    }
}

class Node {
    int l, r;
    int cnt, len;
    public Node(int left, int right) {
        l = left;
        r = right;
    }
}

class Solution {

    final int N = 210, MOD = (int)1e9 + 7;
    Segment[] segs;
    Node[] tr;
    int[] alls;

    public int find(int[] a, int x) {
        int l = 0, r = a.length;
        while(l < r) {
            int mid = l + r >> 1;
            if(a[mid] >= x) r = mid;
            else l = mid + 1;
        }
        return r; // 离散化到0,...
    }

    public int unique(int[] a, int st, int ed) {
        int j = 0; // 不相同元素的个数
        for(int i = st; i <= ed; i ++) 
            if(i == 0 || a[i] != a[i - 1])
                a[j ++] = a[i];
        return st + j;
    }

    public void pushup(int u) { // 用u的孩子信息更新u
        if(tr[u].cnt > 0) tr[u].len = alls[tr[u].r + 1] - alls[tr[u].l]; // 用u自己的信息更新
        else if(tr[u].l == tr[u].r) tr[u].len = 0;
        else tr[u].len = tr[u << 1].len + tr[u << 1 | 1].len; // 用孩子的信息更新u的信息
    }

    public void build(int u, int l, int r) {
        tr[u] = new Node(l, r);
        if(l == r) return ; // 叶子节点返回
        int mid = l + r >> 1;
        build(u << 1, l, mid); // 递归构建左右孩子
        build(u << 1 | 1, mid + 1, r);
    }

    public void modify(int u, int l, int r, int k) {
        if(tr[u].l >= l && tr[u].r <= r) { // 区间[tr[u].l, tr[u].r] 被包含在 [l, r] 中
            tr[u].cnt += k;
            pushup(u);
        } else {
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, k);
            if(r > mid) modify(u << 1 | 1, l, r, k);
            pushup(u);
        }
    }

    public int rectangleArea(int[][] rectangles) {
        int n = rectangles.length;
        segs = new Segment[n * 2 + 10];
        tr = new Node[n * 8 + 10];
        alls = new int[n * 2];

        int m = 0, idx = 0;
        for(int i = 0; i < n; i ++ ) {
            int x1 = rectangles[i][0], y1 = rectangles[i][1];
            int x2 = rectangles[i][2], y2 = rectangles[i][3];
            segs[m ++] = new Segment(x1, y1, y2, 1);
            segs[m ++] = new Segment(x2, y1, y2, -1);
            alls[idx ++] = y1; alls[idx ++] = y2;
        }

        Arrays.sort(segs, 0, m, (a, b) -> a.x - b.x);

        Arrays.sort(alls); //sort
        int ed = unique(alls, 0, alls.length - 1); // unique
        alls = Arrays.copyOfRange(alls, 0, ed); // erase

        build(1, 0, alls.length);

        int res = 0;
        for(int i = 0; i < m; i ++ ) {
            if(i != 0) res = (int)((res + (long)tr[1].len * (segs[i].x - segs[i - 1].x)) % MOD);
            modify(1, find(alls, segs[i].y1), find(alls, segs[i].y2) - 1, segs[i].k);
        }
        return res;
    }
}
```

#### （4）[218. 天际线问题](https://leetcode.cn/problems/the-skyline-problem/) （结合线段树+离散化）

**问题**

城市的 **天际线** 是从远处观看该城市中所有建筑物形成的轮廓的外部轮廓。给你所有建筑物的位置和高度，请返回 *由这些建筑物形成的 **天际线*** 。

每个建筑物的几何信息由数组 `buildings` 表示，其中三元组 `buildings[i] = [lefti, righti, heighti]` 表示：

- `lefti` 是第 `i` 座建筑物左边缘的 `x` 坐标。
- `righti` 是第 `i` 座建筑物右边缘的 `x` 坐标。
- `heighti` 是第 `i` 座建筑物的高度。

你可以假设所有的建筑都是完美的长方形，在高度为 `0` 的绝对平坦的表面上。

**天际线** 应该表示为由 “关键点” 组成的列表，格式 `[[x1,y1],[x2,y2],...]` ，并按 **x 坐标** 进行 **排序** 。**关键点是水平线段的左端点**。列表中最后一个点是最右侧建筑物的终点，`y` 坐标始终为 `0` ，仅用于标记天际线的终点。此外，任何两个相邻建筑物之间的地面都应被视为天际线轮廓的一部分。

**注意：**输出天际线中不得有连续的相同高度的水平线。例如 `[...[2 3], [4 5], [7 5], [11 5], [12 7]...]` 是不正确的答案；三条高度为 5 的线应该在最终输出中合并为一个：`[...[2 3], [4 5], [12 7], ...]`.

例子

<img src="https://assets.leetcode.com/uploads/2020/12/01/merged.jpg" alt="img" style="zoom: 33%;" />

```
输入：buildings = [[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
输出：[[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
解释：
图 A 显示输入的所有建筑物的位置和高度，
图 B 显示由这些建筑物形成的天际线。图 B 中的红点表示输出列表中的关键点。
```

**数据范围：**

- `1 <= buildings.length <= 10^4`
- `0 <= lefti < righti <= 2^31 - 1`
- `1 <= heighti <= 2^31 - 1`
- `buildings` 按 `lefti` 非递减排序

**思路**：**离散化+二分+线段树+扫描线**，模板题

但是，需要注意一下两种类型用例

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-07-18%2021.30.01%E7%9A%84%E5%89%AF%E6%9C%AC.png" alt="截屏2023-07-18 21.30.01的副本" style="zoom: 20%;" />

左边的用例的特点，两个高度相同的矩形相邻且不重叠；右边的用例的特点，两个高度不相同的矩形相邻且不重叠。

```java
class Segment {
    int x, y1, y2;
    int k;
    public Segment(int x, int y1, int y2, int k) {
        this.x = x;
        this.y1 = y1;
        this.y2 = y2;
        this.k = k;
    }
}

class Node {
    int l, r;
    int cnt, len;
    public Node(int l, int r) {
        this.l = l; this.r = r;
    }
}

class Solution {
    final int N = 10010;
    Segment[] segs;
    Node[] tr;
    int[] alls;

    public int unique(int[] a, int st, int ed) {
        int j = 0;
        for(int i = st; i <= ed; i ++)
            if(i == 0 || a[i] != a[i - 1])
                a[j ++] = a[i];
        return st + j;
    }

    public int find(int[] a, int x) {
        int l = 0, r = a.length - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(a[mid] >= x) r = mid;
            else l = mid + 1;
        }
        return r; // 映射到0,....
    }

    public void pushup(int u) {
        if(tr[u].cnt > 0) tr[u].len = alls[tr[u].r + 1] - alls[tr[u].l];
        else if(tr[u].l == tr[u].r) tr[u].len = 0;
        else tr[u].len = tr[u << 1].len + tr[u << 1 | 1].len; 
    }

    public void build(int u, int l, int r) {
        tr[u] = new Node(l, r);
        if(l == r) return ;
        int mid = l + r >> 1;
        build(u << 1, l, mid);
        build(u << 1 | 1, mid + 1, r);
    }

    public void modify(int u, int l, int r, int k) {
        if(tr[u].l >= l && tr[u].r <= r) {
            tr[u].cnt += k;
            pushup(u);
        } else {
            int mid = tr[u].l + tr[u].r >> 1;
            if(l <= mid) modify(u << 1, l, r, k);
            if(mid < r) modify(u << 1 | 1, l, r, k);
            pushup(u);
        }
    }

    public List<List<Integer>> getSkyline(int[][] buildings) {
        int n = buildings.length;
        segs = new Segment[n * 2 + 10];
        tr = new Node[n * 8 + 10];
        alls = new int[n + 1];

        int m = 0, idx = 1;
        for(int i = 0; i < n; i ++) {
            int x1 = buildings[i][0], y1 = 0;
            int x2 = buildings[i][1], y2 = buildings[i][2];
            segs[m ++] = new Segment(x1, y1, y2, 1);
            segs[m ++] = new Segment(x2, y1, y2, -1);
            alls[idx ++] = y2;
        }

        Arrays.sort(segs, 0, m, (a, b) -> (a.x - b.x));

        Arrays.sort(alls); // sort
        int ed = unique(alls, 0, alls.length - 1); // unique
        alls = Arrays.copyOfRange(alls, 0, ed); // erase

        build(1, 0, alls.length);

        List<List<Integer>> res = new ArrayList<>();

        int last = 0;
        for(int i = 0; i < m; i ++) {
            if(i != 0) {
                if(segs[i].x != segs[i - 1].x && last != tr[1].len) {
                    List<Integer> temp = new ArrayList<>();
                    temp.add(segs[i - 1].x);
                    temp.add(tr[1].len);
                    res.add(temp);
                    last = tr[1].len;
                }
            }
            modify(1, find(alls, segs[i].y1), find(alls, segs[i].y2) - 1, segs[i].k);
        }

        List<Integer> temp = new ArrayList<>();
        temp.add(segs[m - 1].x);
        temp.add(0);
        res.add(temp);
        return res;
    }
}
```

### 6、最小生成树

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



### 7、基环树

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
