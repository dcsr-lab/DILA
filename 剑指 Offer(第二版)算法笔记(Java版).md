# 剑指 Offer (第二版) 算法笔记

<div align="center">
    <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202110171205398.jpg" alt="芝士" width = "150px" align= "center"/>
</div>



> 引言
>
> 此文章是为了
>
> :man:：`Laplace`	:timer_clock: ：`2021-11-07`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  



### 1、动态规划

##### （1）剑指 Offer 10- I. 斐波那契数列

```java
class Solution {
    final int N = 110, mod = 1000000007;
    int[] f = new int[N];

    public int fib(int n) {
        f[0] = 0; f[1] = 1;
        if (n == 0) return f[0]; // 易错点一
        if (n == 1) return f[1];
        for(int i = 2; i <= n; i ++)
            f[i] = (f[i-1] + f[i-2]) % mod; // 易错点二

        return f[n] % mod; 
    }
}
```

##### （2）剑指 Offer 42. 连续子数组的最大和:writing_hand: 

**问题**：

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求**所有子数组的和的最大值**。

要求时间复杂度为 $O(n)$ 

**思路**：

假设 $nums$ 数组的长度是 $n$，下标从 $0$ 到 $n−1$。

我们用 $f(i)$ 代表以第 $i$ 个数结尾的「连续子数组的最大和」，那么很显然我们要求的答案就是：

$$
\max _{0 \leq i \leq n-1}\{f(i)\}
$$
因此我们只需要求出每个位置的 $f(i)$，然后返回 $f$ 数组中的最大值即可。那么我们如何求 $f(i)$ 呢？我们可以考虑 $nums[i]$ 单独成为一段还是加入$f(i−1)$ 对应的那一段，这取决于 $nums[i]$ 和 $f(i-1) + nums$ 的大小，我们希望获得一个比较大的，于是可以写出这样的动态规划转移方程：

$$
f(i)=\max \{f(i-1)+n u m s[i], n u m s[i]\}
$$
不难给出一个时间复杂度 $O(n)$、空间复杂度 $O(n)$ 的实现，即用一个$f$ 数组来保存 $f(i)$ 的值，用一个循环求出所有 $f(i)$。考虑到 $f(i)$ 只和 $f(i−1)$ 相关，于是我们可以只用一个变量 $pre$ 来维护对于当前 $f(i)$ 的 $f(i−1)$ 的值是多少，从而让空间复杂度降低到 $O(1)$，这有点类似「滚动数组」的思想**[1]**。

```java
class Solution {
    public int maxSubArray(int[] nums) {
        // f[i] 表示以第i个元素结尾的连续子数组的最大和
        // f[i] = max(f[i-1] + nums[i], nums[i])
        // res存储所有连续子数组和的最大值, sum存储f[i-1]以第i-1个元素结尾的连续子数组的最大和
        int res = Integer.MIN_VALUE, sum = 0; 
        for(int i = 0; i < nums.length; i ++ ){
            sum = Math.max(0, sum); // 如果f[i-1]为负数, 则放弃这个子区间
            sum += nums[i];
            res = Math.max(res, sum); // 所有连续子数组的元素和取最大值
        }
        return res;
    }
}
```

##### （3）剑指 Offer 10- II. 青蛙跳台阶问题

**问题**：一只青蛙一次可以跳上`1`级台阶，也可以跳上`2`级台阶。求该青蛙跳上一个 `n` 级的台阶总共有多少种跳法。

答案需要取模 `1e9+7`（`1000000007`），如计算初始结果为：`1000000008`，请返回 `1`。

**思路**：$f[i] = f[i-1] + f[i-2],(f[0] = 1,f[1] = 1)$

```java
class Solution {
    final int MOD = 1000000007;

    public int numWays(int n) {
        int[] f = new int[n + 10];
        f[0] = 1; f[1] = 1; f[2] = 2; // 初始化
        for(int i = 3; i <= n; i ++) 
            f[i] = (f[i - 1] + f[i - 2]) % MOD; // 状态迁移
        return f[n];
    }
}
```

##### （4）746. 使用最小花费爬楼梯

**问题**：

给你一个整数数组 `cost` ，其中 `cost[i]` 是从楼梯第 `i` 个台阶向上爬需要支付的费用。一旦你支付此费用，即可选择向上爬**一个**或者**两个台阶**。

你可以选择从下标为 `0` 或下标为 `1` 的台阶开始爬楼梯。

请你计算并返回达到楼梯顶部的**最低花费**

**思路**：此问题是**上一个问题的扩展**

$f[i] = min(f[i-1] + cost[i-1] , f[i-2]+cost[i-2])$，$(f[0] = 0,f[1] = 0)$

```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int n = cost.length;
        int[] f = new int[n + 10];
        f[0] = 0; f[1] = 0; // 初始化状态
        for(int i = 2; i <= n; i ++) 
            f[i] = Math.min(f[i - 1] + cost[i - 1], f[i - 2] + cost[i - 2]); // 状态迁移
        return f[n]; // 下标为 n 的楼梯即为楼梯顶
    }
}
```

##### （5）剑指 Offer 47. 礼物的最大价值

**问题**

在一个 `m*n` 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你**最多能拿到多少价值的礼物**？

**思路**

线性DP $O(nm)$

```java
class Solution {
    public int maxValue(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        int[][] f = new int[n + 1][m + 1]; // f[i][j]表示从左上角到(i,j)最多能拿到的礼物的价值

        for(int i = 1; i <= n; i ++ )
            for(int j = 1; j <= m; j ++ )
                f[i][j] = Math.max(f[i][j - 1], f[i - 1][j]) + grid[i - 1][j - 1]; // 状态转移
            
        return f[n][m];
    }
}
```



### 2、双指针

##### （1）剑指 Offer 21. 调整数组顺序使奇数位于偶数前面

**问题**：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数在数组的前半部分，所有偶数在数组的后半部分

**思路**：采用**双指针**算法，思想来源于快速排序

- 设置两个指针，左指针和右指针
- 左指针从左向右遍历寻找第一个偶数
- 右指针从右向左遍历寻找第一个奇数
- 交换两个数，重复上述步骤，直至两个指针相遇

```java
class Solution {
    public void swap(int a[], int i, int j){
        int temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }

    public int[] exchange(int[] nums) {
        if(nums == null) return null;
        if(nums.length == 0) return nums;

        int i = -1, j = nums.length;
        while(i < j){
            do i ++; while(i < nums.length && nums[i] % 2 != 0); // 从左到右查找第一个偶数
            do j --; while(j >= 0 && nums[j] % 2 != 1); // 从右到左查找第一个奇数(j>=0防止数组索引越界)
            if(i < j) swap(nums, i, j); // 将奇数放在左边, 偶数放在右边
        }

        return nums;
    }
}
```

##### （2）剑指 Offer 57. 和为s的两个数字

**问题**：输入一个**递增排序**的数组和一个数字`s`，在数组中查找两个数，使得它们的和正好是`s`。如果有多对数字的和等于`s`，则输出任意一对即可。

**思路**：

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int i = 0, j = nums.length - 1; i < j;){
            if(nums[i] + nums[j] < target) i ++ ;
            else if(nums[i] + nums[j] > target) j -- ;
            else return new int[]{nums[i], nums[j]};
        }
        return new int[0];
    }
}
```



### 3、链表

##### （1）剑指 Offer 22. 链表中倒数第k个节点（双指针）

**解法一：双指针**

- 设置两个指针, 第一个指针走到第`k`个元素时, 第二个指针出发
- 当第一个指针走到链表尾时, 第二个指针指向第`k`个结点

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
    public ListNode getKthFromEnd(ListNode head, int k) {
        
        int cnt = 1; // 记录走到了第几个结点
        ListNode i = null, j = null; // i 表示第一个指针，j表示第二个指针
        for(i = head, j = head; i != null; i = i.next){
            if(cnt > k) j = j.next; // 当i走过第k个结点是，j开始走
            cnt ++ ;
        }
        return j;
    }
}
```

##### （2）剑指 Offer 52. 两个链表的第一个公共节点:writing_hand: （双指针）

**解法一：双指针**

- 设置两个指针，第一个指针指向链表 A，移动到链表尾时，再指向链表 B；第二个指针指向链表 B，移动到链表尾时，指向链表 A;
- 当两个指针相遇时，即为两个链表的第一个公共结点

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // 双指针
        ListNode i = headA, j = headB;
        while(i != j){
            i = i == null ? headB : i.next;
            j = j == null ? headA : j.next;
        }
        return i;
    }
}
```

**解法二：哈希表**

- 遍历链表A，用哈希表存储各结点
- 遍历链表B，找到第一个出现在哈希表中的结点，即为两个链表的第一个公共结点

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // 哈希表 O(n) 非O(1)
        Set<ListNode> set = new HashSet<>();
        for(ListNode i = headA; i != null; i = i.next)
            set.add(i);
        for(ListNode i = headB; i != null; i = i.next)
            if(set.contains(i))
                return i;
        return null;
    }
}
```

##### （3）剑指 Offer 25. 合并两个排序的链表（二路归并）

采用**二路归并**思想合并两个有序的链表

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
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode i = l1, j = l2;
        ListNode h = new ListNode(0), k = h;
        while(i != null && j != null){
            if(i.val <= j.val) {
                k.next = i; i = i.next;
            } else {
                k.next = j; j = j.next;
            }
            k = k.next;
        }
        if(i != null) k.next = i;
        if(j != null) k.next = j;
        return h.next;
    }
    
}
```

##### （4）剑指 Offer 18. 删除链表的节点

**分类讨论**

- 删除的是头结点：`head = head.next`
- 删除的是普通结点：`j.next = i.next`，`j`是`i`的前继结点

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
    public ListNode deleteNode(ListNode head, int val) {
        for(ListNode i = head, j = head; i != null; i = i.next){ // i指向当前结点，j指向前继结点
            if(i.val == val) { 
                if(i != head) j.next = i.next; 
                else head = head.next; // 如果是头结点，直接删除头结点
                return head;
            }
            else j = i;
        }
        return head;
    }
}
```



### 4、栈

##### （1）剑指 Offer 30. 包含min函数的栈

**问题**：定义栈的数据结构，请在该类型中实现一个能够**得到栈的最小元素的 `min` 函数**在该栈中，调用 `min`、`push` 及 `pop` 的时间复杂度都是 $O(1)$ 

**思路**：

- 两个栈来实现，一个普通栈，一个最小值栈
- 普通栈存储当前集合中的元素
- 最小值栈的栈顶元素表示当前集合中的最小值

```java
class MinStack {

    Deque<Integer> stack = new ArrayDeque<>(); // 普通栈
    Deque<Integer> minStk = new ArrayDeque<>(); // 最小值栈，栈顶元素维护当前集合中的最小值

    public MinStack() {}
    
    public void push(int x) {
        stack.push(x);
        if(minStk.isEmpty()) minStk.push(x);
        else minStk.push(Math.min(minStk.peek(), x));
    }
    
    public void pop() {
        stack.pop();
        minStk.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int min() {
        return minStk.peek();
    }
}
```



### 5、单调队列

##### （1）剑指 Offer 59 - I. 滑动窗口的最大值

**问题**：给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有**滑动窗口**里的**最大值**

**思路**：使用**双端队列**实现**单调递减队列**，求解**滑动窗口**的最大值

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums.length == 0) return nums;
        int[] a = nums; // 原数组
        int n = a.length; // 元素个数

        int hh = 0, tt = -1; // 队头指针和队尾指针
        int[] q = new int[n]; // 双端队列实现滑动窗口，存储的是元素的下标
        int[] res = new int[n - (k-1)];

        for(int i = 0; i < n; i ++){
            if(hh <= tt && i -(k-1) > q[hh]) hh ++ ; // 队头元素滑出队列，队头元素出队
            while(hh <= tt && a[q[tt]] <= a[i]) tt --; //滑入元素比队尾元素大,队尾元素出队,滑入元素直达比队尾元素小

            q[++ tt] = i;
            if(i >= k-1) res[i-(k-1)] = a[q[hh]];
        }
        return res;
    }
}
```

##### （2）剑指 Offer 59 - II. 队列的最大值

**问题**：

请定义一个队列并实现函数 `max_value` 得到队列里的最大值，要求函数`max_value`、`push_back` 和 `pop_front` 的均摊时间复杂度都是 $O(1)$。

若队列为空，`pop_front` 和 `max_value` 需要返回 -1

**思路**：

- 将该问题视为**退化的滑动窗口问题**，滑动窗口的大小为无限大
- 使用**双端队列**实现**单调递减队列**，求解**滑动窗口**的最大值

```java
class MaxQueue {
    // 退化的滑动窗口问题, 双端队列实现单调递减队列
    Queue<Integer> queue = new ArrayDeque<>(); // 普通队列
    Deque<Integer> deq = new ArrayDeque<>(); // 双端队列，队头元素存储当前窗口中的最大值

    public MaxQueue() { }
    
    public int max_value() {
        if(queue.isEmpty()) return -1;
        return deq.peekFirst();
    }
    
    public void push_back(int value) {
        queue.offer(value);
        while(!deq.isEmpty() && deq.peekLast() <= value) deq.pollLast(); // 当deq队尾元素比入队元素小时，队尾元素出队，直到队尾元素比入队元素大
        deq.offerLast(value);
    }
    
    public int pop_front() {
        if(queue.isEmpty()) return -1;
        int front = queue.peek(); // 取队头元素
        queue.poll(); // 对头元素出队列
        if(front == deq.peekFirst()) deq.pollFirst(); // 队头元素已经滑出当前窗口
        return front;
    }
}

```



### 6、搜索 BFS 和 DFS 

##### （1）剑指 Offer 12. 矩阵中的路径

**问题**：给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202201251821588.jpeg" alt="img" width="200px" />

- 找出单词首字母所有可能出现的位置
- 以每一个位置为起点，搜索该单词

```java
class Node{
    int x, y;
    Node(int x, int y){
        this.x = x;
        this.y = y;
    }
}

class Solution {
    final int M = 200, N = 200;
    char[][] g = new char[M][N]; // 二维字符串网格
    boolean[][] st = new boolean[M][N]; // 状态数组，标记某个位置是否遍历过
    ArrayList<Node> post = new ArrayList<>(); // 存储单词首字母出现的位置信息
    char[] w = new char[M*N]; // 单词字符数组
    int[] dx = {1,0,-1,0}, dy = {0, 1, 0, -1}; // 偏移量
    int m, n; 

    public boolean dfs(Node u, int k){
        if(g[u.x][u.y] == w[k] && k + 1 == w.length) return true; // 以u为起点搜索到此单词
        if(g[u.x][u.y] != w[k] || k + 1 > w.length) return false; // 以u为起点无法搜索到此单词

        boolean res = false;
        for(int i = 0; i < 4; i ++ ){
            int tx = u.x + dx[i], ty = u.y + dy[i];
            if(tx < 0 || tx >= m || ty < 0 || ty >= n) continue; // 越界
            if(st[tx][ty]) continue; // 已经搜索过

            st[tx][ty] = true; 
            if(res = dfs(new Node(tx, ty), k + 1)) break;
            st[tx][ty] = false; // 恢复现场           
        }
        return res;
    }

    public boolean exist(char[][] board, String word) {
        m = board.length; n = board[0].length;
        g = board;
        w = word.toCharArray();
        
        for(int i = 0; i < m; i ++) // 求出单词首字母所有可能出现的位置信息
            for(int j = 0; j < n; j ++)
                if(w[0] == g[i][j]) post.add(new Node(i,j));

        if(w != null && w.length != 0 && post.size() != 0)
            for(Node node : post){ // 以首字母出现每一个位置为起点进行搜索
                for(int i = 0; i < n; i ++) Arrays.fill(st[i], false); // 将指定的值填充给指定的数组的每个元素
                st[node.x][node.y] = true;
                if(dfs(node, 0)) return true;
            }

        return false;
    }
}
```

##### （2）剑指 Offer 13. 机器人的运动范围

**问题**：地上有一个$m$行$n$列的方格，从坐标 $[0,0]$ 到坐标 $[m-1,n-1]$ 。一个机器人从坐标 $[0, 0]$ 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也**不能进入行坐标和列坐标的数位之和大于$k$的格子**。例如，当$k$为$18$时，机器人能够进入方格 $[35, 37]$ ，因为$3+5+3+7=18$。但它不能进入方格 $[35, 38]$，因为$3+5+3+8=19$。请问该机器人能够到达多少个格子？

**思路**

- 使用**BFS**实现**FloodFill算法**求解机器人从起点最多能到达多少个格子

```java
class Pair{
    int x, y;
    Pair(){}
    Pair(int x, int y){
        this.x = x;
        this.y = y;
    }
}

class Solution {

    int[][] g; // 地图
    boolean[][] st; // 判重数组
    int m, n, k; // m行n列

    public int get(int x){ // 计算x的数位之和
        int res = 0;
        while(x != 0){
            res += x % 10;
            x /= 10;
        }
        return res;
    }

    public boolean check(int x, int y){ // 判断行坐标和列坐标的数位之和是否大于k
        return (get(x) + get(y)) > k;
    }

    public int bfs(){ // 从(0,0)处bfs搜索(或FloodFill)机器人能够到达的所有位置
        int hh = 0, tt = 0; // 队头指针和队尾指针
        Pair[] q = new Pair[m*n]; // 队列
        q[0] = new Pair(0,0); // 初始化队列
        st[0][0] = true;

        int[] dx = {-1, 0, 1, 0}, dy = {0, -1, 0, 1}; // 偏移量
        while(hh <= tt){ // 队列不空
            Pair t = q[hh ++];

            for(int i = 0; i < 4; i ++){ // 向左、右、上、下移动
                int tx = t.x + dx[i], ty = t.y + dy[i];
                if(tx < 0 || tx >= m || ty < 0 || ty >= n) continue; // 越界
                if(check(tx, ty)) continue; // 行坐标和列坐标的数位之和大于k
                if(st[tx][ty]) continue; // 已经走过

                q[++ tt] = new Pair(tx, ty);
                st[tx][ty] = true;
            }
        }

        return tt + 1;
    }

    public int movingCount(int m, int n, int k) {
        this.m = m; this.n = n; this.k = k;
        g = new int[m][n];
        st = new boolean[m][n];
        int res = bfs();
        return res;
    }
}
```



### 7、树

##### （1）剑指 Offer 33. 二叉搜索树的后序遍历序列

**解法一：反证法**

- 假设`postorder`是某棵二叉搜索树的后序遍历结果；
- 对该棵二叉搜索数进行中序遍历就可以得到一个单调递增的数组，那么对后序遍历结果`postorder`进行升序排序同样可以得到该二叉搜索树的中序遍历结果`inorder`；
- 如果假设成立，即可以用`inorder`和`postorder`构造出一颗二叉搜索树，如果无法构造，则与假设矛盾，即`postorder`不是某二叉搜索树的后序遍历结果

```java
class Solution {
    final int N = 1010;
    int n;
    int[] post = null, in = null; // 后序序列 前序序列
    HashMap<Integer,Integer> map = new HashMap<>(); // 每个结点在后序序列中的位置

    public boolean build(int il, int ir, int pl, int pr){
        int root = post[pr];
        int k = map.get(root);

        if(k < il || k > ir) return false; // 表示后序序列的根结点root，在中序序列中找不到时，该子树无法构造

        // il == k 表示无左子树, 相当于左子树构建成功；ir == k 同理
        // 利用 || 的短路效果用作做判断
        if ((il == k || build(il, k - 1, pl, pl + (k - 1 - il)))
         && (ir == k || build(k + 1, ir, pl + (k - 1 - il) + 1, pr - 1)))
            return true;
        else return false;
    }

    public boolean verifyPostorder(int[] postorder) {
        if ((n = postorder.length) == 0) return true; // 空树
        post = postorder;

        in = Arrays.copyOfRange(postorder, 0, n);
        Arrays.sort(in);
        for(int i = 0; i < n; i ++) map.put(in[i], i);

        boolean res = build(0, n-1, 0, n-1);
        return res;
    }
}
```

##### （2）剑指 Offer 07. 重建二叉树

根据**前序序列和中序序列**重新构建二叉树

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

    HashMap<Integer, Integer> pos = new HashMap<>(); // 存储中序遍历每个结点的位置
    int[] pre = null, in = null; // 前序序列和中序序列
    int n; // 二叉树结点数

    public TreeNode build(int il, int ir, int pl, int pr){
        int root = pre[pl];
        TreeNode u = new TreeNode(root);
        int k = pos.get(root); // 根结点在中序遍历的位置, 哈希表O(1)

        if(il < k) u.left = build(il, k - 1, pl + 1, pl + 1 + (k-1-il));
        if(k < ir) u.right = build(k + 1, ir, pl + 1 + (k-1-il) + 1, pr);
        
        return u;
    }

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if(inorder.length == 0) return null;
        pre = preorder; in = inorder;
        n = in.length;
        for(int i = 0; i < n; i ++) pos.put(in[i], i);

        TreeNode root = build(0, n-1, 0, n-1);
        return root;
    }
}
```

##### （3）剑指 Offer 55 - II. 平衡二叉树

- 此题平衡二叉树的定义：如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树
- 中序遍历二叉树，求出二叉树的中序序列和以每个结点为根结点的子树的深度
- 判断任意节点是否满足定义

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

    ArrayList<TreeNode> inorder = new ArrayList<>(); // 中序序列
    HashMap<TreeNode, Integer> h = new HashMap<>(); // 表示已key根结点的子树的高度为value

    public int dfs(TreeNode u){
        if(u == null) return 0; // 空结点的深度为 0

        int hl = dfs(u.left); // u 的左子树的深度
        inorder.add(u);
        int hr = dfs(u.right); // u 的右子树的深度

        h.put(u, Math.max(hl, hr) + 1); // 以u为根节点的子树的深度等于左/右子树最大深度+1
        return h.get(u);
    }

    public boolean isBalanced(TreeNode root) {
        dfs(root);

        for(int i = 0; i < inorder.size(); i ++){
            TreeNode t = inorder.get(i);
            int hl = t.left == null ? 0 : h.get(t.left); // 空结点深度为 0
            int hr = t.right == null ? 0 : h.get(t.right);
            if(Math.abs(hl - hr) > 1) return false;
        }

        return  true;
    }
}
```

##### （4）剑指 Offer 68 - I. 二叉搜索树的最近公共祖先

- 二叉树搜索树特点：`根结点 > 左孩子`，`根结点 < 右孩子`
- 前序遍历特点：遍历顺序为`根->左->右`
- 在前序序列中**找到第一个满足二叉搜索树特点的结点**为`p,q`的 LCA

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

    ArrayList<TreeNode> pre  = new ArrayList<>(); // 二叉搜索树的前序序列

    public void dfs(TreeNode u){ // 前序遍历, 求二叉搜索树的前序序列
        if(u == null) return ;

        pre.add(u);
        dfs(u.left);
        dfs(u.right);
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        dfs(root);

        int a = p.val, b = q.val;
        TreeNode father = null;

        for(int i = 0; i < pre.size(); i ++) // 找到第一个满足二叉搜索树特点的结点
            if((pre.get(i).val >= a && pre.get(i).val <= b) 
                || (pre.get(i).val >= b && pre.get(i).val <= a)){
                father = pre.get(i);
                break;
            }

        return father;
    }
}
```

##### （5）剑指 Offer 68 - II. 二叉树的最近公共祖先

**一般方法**

- 遍历二叉搜索树，将各个结点**离散化**到 `0~n-1`，便于后续处理，同时，用数组存储**每个结点的深度**和**对应的父结点**
- 用**爬山法**去找最近公共祖先，每次把较深的点，向上移动一次，直到两个结点相等，就找到了最近公共祖先

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
    HashMap<TreeNode, Integer> map = new HashMap<>(); // 离散化
    HashMap<Integer, TreeNode> remap = new HashMap<>(); 
    ArrayList<Integer> depth = new ArrayList(); // 存储每个结点的深度
    ArrayList<TreeNode> parent = new ArrayList(); // 存储每个结点对应的父结点
    int idx = 0;

    public void dfs(TreeNode u, TreeNode p ,int k){
        if(u == null) return ;

        map.put(u,idx); // 将所有结点的值离散化到0-n-1
        remap.put(idx ++, u); // 离散化后的值离散化后的值与原结点的映射关系
        depth.add(k); // 离散化后的值对应的该结点的深度
        parent.add(p); // 离散化后的值对应的该结点的父结点

        dfs(u.left, u, k + 1);
        dfs(u.right, u, k + 1);
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        dfs(root, null, 1);

        int a = map.get(p), b = map.get(q);
        while(a != b){ // 爬山法找最近公共祖先，每次把较深的点，向上移动一次，直到两个结点相等，就找到了最近公共祖先
            if(depth.get(a) > depth.get(b)) a = map.get(parent.get(a));
            else b = map.get(parent.get(b));
        }

        return remap.get(a);
    }
}
```

##### （6）剑指 Offer 34. 二叉树中和为某一值的路径

**问题**：给你二叉树的根节点 `root` 和一个整数目标和 `targetSum` ，找出所有 **从根节点到叶子节点** 路径总和等于给定目标和的路径。

**解法一**

- 从根节点开始搜索，遍历所有的路径
- 采用**做减法**的方式找出，路径总和等于给定目标和的路径

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
import java.util.stream.*;

class Solution {
    final int N = 5010, M = 1000;
    List<List<Integer>> res = new LinkedList<>(); // 定义嵌套数组，存储所有的目标和的路径

    public void dfs(TreeNode u, int target, LinkedList<Integer> array){
        if(u == null) return ; // 当root为null，空树，结束搜索

        array.add(u.val); // 向路径中添加当前结点
        if(u.left == null && u.right == null && target - u.val == 0) // u是叶子结点, 且从根节点到叶子节点路径总和等于给定目标和, 此时array为一条合法路径
           res.add(new LinkedList<>(array));
        
        if(u.left != null) dfs(u.left, target-u.val, array);
        if(u.right != null) dfs(u.right, target-u.val, array);
        array.remove(array.size()-1); // 恢复现场
    }    

    public List<List<Integer>> pathSum(TreeNode root, int target) {
        dfs(root, target ,new LinkedList<Integer>());
        return res;
    }
}
```

**解法二**

- 从根节点开始搜索，遍历所有的路径
- 采用**做加法**的方式找出，路径总和等于给定目标和的路径

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
import java.util.stream.*;

class Solution {
    final int N = 5010, M = 1000;
    Integer[] path = new Integer[N];
    List<List<Integer>> res = new LinkedList<>();

    public void dfs(TreeNode u, int target, int h){
        if(u == null && h == 0) return ; // 树中无结点,空树
        if(u == null) return ; // 叶子结点
        path[h] = u.val;
        if(u.left == null && u.right == null){ // u是叶子结点
            Integer[] array = Arrays.copyOfRange(path,0,h+1); // 范围数组拷贝
            if(Stream.of(array).mapToInt(i->i).sum() == target) // 路径总和等于目标和
                res.add(Stream.of(array).collect(Collectors.toList()));
            return ;  
        }
        dfs(u.left, target,h + 1);
        dfs(u.right, target,h + 1);
    }

    public List<List<Integer>> pathSum(TreeNode root, int target) {
        dfs(root, target , 0);
        return res;
    }

}
```

##### （7）剑指 Offer 55 - I. 二叉树的深度

**二叉树中序遍历**

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
    int res = 0;

    // 中序遍历二叉树，求最大的深度
    public void dfs(TreeNode u, int h){
        if(u == null) return ;
        res = Math.max(res, h);
        if(u.left != null) dfs(u.left, h + 1);
        if(u.right != null) dfs(u.right, h + 1);
    }

    public int maxDepth(TreeNode root) {
        dfs(root, 1);
        return res;
    }
}
```

##### （8）剑指 Offer 37. 序列化二叉树:writing_hand: 

类似题目 [449. 序列化和反序列化二叉搜索树](https://leetcode.cn/problems/serialize-and-deserialize-bst/)

- 序列化二叉树：使用**BFS**实现“**二叉树层序遍历**”，满足 [LeetCode 序列化二叉树的格式 ](https://support.leetcode-cn.com/hc/kb/article/1194353/) 
- 反序列化二叉树：利用队列根据“**层序遍历**”的结果，重建二叉树
- 这里的“层序遍历”是**特殊的**层序遍历，满足 LeetCode 序列化二叉树的格式，允许存在空结点，但末尾一定不含有空结点

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202201272125355.png" alt="image-20220127212529224" width="260px" />

序列化结果 [5,2,3,null,null,2,4,3,1]

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
public class Codec {
    final String NULL = "null";
    /**
        根据root根结点得到水平顺序遍历的二叉树, 
        例如案例的序列化格式[1,2,3,null,null,4,5], 
        null 来表达某一分支上没有子节点
     */
    public String bfs(TreeNode root){ // 二叉树的层序遍历
        List<String> arr = new ArrayList<>();
        Queue<TreeNode> q = new LinkedList<>(); // 队列, 允许存null值
        q.offer(root); // 根结点入队列

        while(!q.isEmpty()){
            TreeNode t = q.poll(); // 队头元素出队列
            if(t == null) { // t 为空结点
                arr.add(NULL); 
                continue;
            }
            else arr.add(String.valueOf(t.val));
            q.offer(t.left); q.offer(t.right); // 左孩子和右孩子入队列
        }

        while(!arr.isEmpty() && arr.get(arr.size()-1).equals(NULL)) // 去除结尾多于的null结点
            arr.remove(arr.size()-1);

        return arr.toString().replace(" ",""); // 去除多于的空格
    }
    /**
        根据串行化格式序列化格式[1,2,3,null,null,4,5], 利用队列反序列化重建二叉树
        nodes.length > 0
     */
    public TreeNode build(Integer[] nodes, int n){
        TreeNode root = new TreeNode(nodes[0]);
        Queue<TreeNode> q = new ArrayDeque<>(); // 队列, 不允许存null
        q.offer(root);
        int idx = 0;

        while(!q.isEmpty() && idx < n - 1){
            TreeNode t = q.poll();
            if(++ idx < n && nodes[idx] != null) t.left = new TreeNode(nodes[idx]);//如果t有左子树, 构建左子树
            if(++ idx < n && nodes[idx] != null) t.right = new TreeNode(nodes[idx]);//如果t有右子树,构建右子树

            if(t.left != null) q.offer(t.left); // 左子树入队列
            if(t.right != null) q.offer(t.right); // 右子树入队列
        }

        return root;
    }

    // 序列化将一个二叉树编码为一个字符串
    public String serialize(TreeNode root) {
        return bfs(root);
    }

    // 反序列化将字符串解码为一个二叉树
    public TreeNode deserialize(String data) {
        if("[]".equals(data)) return null; // 空树, 特判

        // 将data字符串转换成int类型的数组
        String[] strs = data.replace("]","").replace("[","").split(","); // 去除无用字符
        int n = strs.length; // 结点数
        Integer[] nodes = new Integer[n];
        for(int i = 0; i < n; i ++)
            if(strs[i].equals(NULL)) nodes[i] = null;
            else nodes[i] = Integer.parseInt(strs[i]);

        return build(nodes, n);
    }
}
```

##### （9）剑指 Offer 36. 二叉搜索树与双向链表:writing_hand: 

**问题**：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表

二叉搜索树

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202201291346585.png" alt="img" width="400px" />

二叉搜索树转化为双向循环链表

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202201291347864.png" alt="img" width="600px" />

**解法一**：$O(logn)$

- **通过二叉树的中序遍历将各个结点串成一个双向链表**

```java
class Solution {
    Node cur, pre, head; // 链表的当前结点，前继结点，头结点

    public void dfs(Node u){ // 通过二叉树的二中序遍历将各个结点串成一个双向链表
        if(u == null) return ;
        dfs(u.left); // 递归左结点

        cur = u;
        if(head == null) head = u; // head只赋值一次且第一个赋值的结点即为头结点
        if(pre != null && cur != null) { //如果pre和cur指针皆不为空, 则将前继结点和当前结点串起来
            pre.right = cur;
            cur.left = pre;
        }
        pre = cur;

        dfs(u.right); // 递归右结点
    }

    public Node treeToDoublyList(Node root) {
        if(root == null) return null; // 特判
        dfs(root);
        
        head.left = cur; // 构建循环双链表
        cur.right = head; // 最后cur指向最后一个结点
        
        return head;
    }
}
```

**解法二**：$O(n)$

- 中序遍历二叉树并存储中序序列，遍历中序序列构建双向链表

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {

    ArrayList<Node> a = new ArrayList<>();

    public void dfs(Node u){
        if(u == null) return ;
        dfs(u.left);
        a.add(u);
        dfs(u.right);
    }

    public Node treeToDoublyList(Node root) {
        if(root == null) return null;

        dfs(root);
        for(int i = 0; i < a.size(); i ++){
            if(i != a.size() - 1){
                a.get(i).right = a.get(i + 1);
                a.get(i + 1).left = a.get(i);
            }
            if(i == 0) a.get(i).left = a.get(a.size() - 1);
            if(i == a.size() - 1) a.get(i).right = a.get(0);
        }
        return a.get(0);
    }
}
```

##### （10）剑指 Offer 26. 树的子结构 :writing_hand: 

**问题**：输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

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
    
    public boolean dfs(TreeNode A, TreeNode B){  // 判断B为根结点的树是否是以A为根结点的树的子结构
        if(B == null) return true; // 无论结点A是否存在, 结点B不存在是，相当于B是A的子结构
        if(A == null) return false; // 当结点B存在, 结点A不存在, B不是是A的子结构
        return A.val == B.val && dfs(A.left, B.left) && dfs(A.right, B.right); // 根结点,左结点,右结点都相同
    }
    
    public boolean isSubStructure(TreeNode A, TreeNode B) { // 从A中搜索所有可能的子树与B进行比较
        if(A == null || B == null) return false; // 空树不是任意一个树的子结构
        return dfs(A,B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
    }
}
```



### 8、枚举

##### （1）剑指 Offer 38. 字符串的排列

**问题**：输入一个字符串，打印出该字符串中字符的所有排列。可以以任意顺序返回这个字符串数组，但里面不能有重复元素

**思路**：**递归实现排列型枚举**

```java
class Solution {

    char[] chs; // 表示可以枚举的所有字符的字符数组
    boolean used[]; // 存储的字符数组的下标，表示该字符数组是否使用过
    HashSet<String> res = new HashSet<>(); // 存储所有的结果，去重无序
    StringBuilder sb = new StringBuilder(); // 临时存储其中的一种方案
    int n;

    public void dfs(int u){
        if(u > n) { // 枚举到一种方案, 将其添加到集合中
            String s = sb.toString();
            if(!res.contains(s)) res.add(s);
            return ;
        }
        for(int i = 0; i < n; i ++) // 为u位置枚举每一个可用字符
            if(!used[i]){ // 找打第一个没有被用过的字符串
                sb.append(chs[i]);
                used[i] = true;

                dfs(u + 1); // 枚举下一个位置
                sb.deleteCharAt(sb.length()-1); // 恢复现场
                used[i] = false;
            }
    }

    public String[] permutation(String s) {
        n = s.length(); // 初始化
        chs = s.toCharArray();
        used = new boolean[n];

        dfs(1); // 从第一个位置起开始枚举
        return res.toArray(new String[1]);
    }
}
```



### 9、位运算

##### （1）剑指 Offer 64. 求1+2+…+n:writing_hand: 

`&&`运算的**短路特性**，起到`if-else` **逻辑判断**作用

```java
class Solution {
    
    public int dfs(int n){
        int res = n;
        n > 0 && (res += dfs(n-1)) > 0; //当 n < 0时，结束递归
        return res; 
    }

    public int sumNums(int n) {
        return dfs(n);
    }
}
```

##### （2）剑指 Offer 15. 二进制中1的个数

`x >> k & 1` 求`x`的二进制表示的第`k`位(`k`从`0`开始)

```java
public class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        for(int i = 0; i < 32; i ++){
            if((n >> i & 1) == 1) // x >> k & 1 求x的二进制表示的第k位(k从0开始)
                res ++ ;
        }
        return res;
    }
}
```

##### （3）剑指 Offer 65. 不用加减乘除做加法:writing_hand: 

问题：写一个函数，求两个整数之和，要求在函数体内不得使用 “+”、“-”、“*”、“/” 四则运算符号。

**思路**：

对应 **二进制加法** ,**每一位**输出的情况是可以列出来，如图：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202211824448.png" alt="image.png" width="800px" />

**循环版**

```java
class Solution {
    public int add(int a, int b) {
        while(b != 0){
            int t = a & b; // 进位
            a = a ^ b; // 本位
            b = t << 1; 
        }
        return a;
    }
}
```

**递归版**

```java
class Solution {
    public int add(int a, int b) {
        return b != 0 ? add(a ^ b, (a & b) << 1) : a;
    }
}
```

##### （4）[剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode.cn/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

**问题**：

一个整型数组 `nums` 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是$O(n)$，空间复杂度是$O(1)$。

例子：

```
输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]
```

**数据范围**：

- `2 <= nums.length <= 10000`

**思路**：**分组异或** 

思路来自[LeetCode官方题解](https://leetcode.cn/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/solution/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-by-leetcode/)

核心思想是：

- 先对所有数字进行一次异或，得到**两个出现一次的数字（设为 $a$，$b$）的异或值** $res$。
- 在异或结果中找到任意为 1 的位，$lowbit(x)$操作可以找到$x$的从左到右的最后一位1和之后的所有0。
- 根据这一位对所有的数字分成两个组，使得两个出现一次的数字一定在不同组，相同的数字一定出现在同一组。
  - 底层原理是 $res_i$表示$res$二进制表示从左往右的第 $i$ 位，如果该位为 $1$，那么$a_i$ 和 $b_i$不一样。依据该原理就可以将原序列分成两个组，每一组各有一个出现一次的数且相同的数一定出现在同一组内。
- 在每个组内进行异或操作，得到两个数字。

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

##### （5）[136. 只出现一次的数字](https://leetcode.cn/problems/single-number/)

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

##### （6）[剑指 Offer II 070. 排序数组中只出现一次的数字](https://leetcode.cn/problems/skFtm2/)

**问题**：

给定一个只包含整数的有序数组 `nums` ，每个元素都会出现两次，唯有一个数只会出现一次，请找出这个唯一的数字。

你设计的解决方案必须满足 `O(log n)` 时间复杂度和 `O(1)` 空间复杂度。

例子：

```
输入: nums = [1,1,2,3,3,4,4,8,8]
输出: 2

特例
输入: nums = [1]
输出: 1
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^5`

**思路**：

题目给定的数组是一个已经排好顺序的数组，且仅有一个数字只出现一次，其余数组都出现两次。

那么，出现两次的数一定是相邻。因此，我们可以采用步长为 $2$的指针来迭代次数组。

令指针 $i$ 初始指向位置0，如果$nums[i] == nums[i + 1]$，我们就让指针向前走两步，直到找到使得$nums[i] != nums[i + 1]$，那么此时$nums[i]$就是只出现一次的数。

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int i = 0;
        while(i < nums.length - 1) {
            if(nums[i] != nums[i + 1]) break;
            i += 2;
        }
        return nums[i];
    }
}
```



##### （7）[剑指 Offer 56 - II. 数组中数字出现的次数 II](https://leetcode.cn/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/) / [剑指 Offer II 004. 只出现一次的数字](https://leetcode.cn/problems/WGki4K/) 

**问题**：

在一个数组 `nums` 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

例子：

```
输入：nums = [3,4,3,3]
输出：4
```

**数据范围**：

- `1 <= nums.length <= 10000`
- `1 <= nums[i] < 2^31`

**思路**：

如果一个数出现了三次，意味着这个数二进制表示的第 $i$ 位也出现了三次。

统计所有数二进制表示的第$i$位是1的个数$cnt$。设出现一次的数为 $a$。

那么，一定有 $a_i == cnt \ \% \ 3$，因为出现三次的数二进制表示的第$i$位之和要么是3要么是0，其模3时都为0；出现一次数第$i$位要么是0要么是1，其模3还等于自身。

因此，只要我们能够快速统计二进制表示所有数第$i$位是1的个数$cnt$，我们就能够计算出$a$的二进制表示的每一位。

采用 $x >> k \ \& \ 1$ 能够快速计算出 $x$ 二进制表示的从右往左的第 $k$ 位。

时间复杂度是 $O(32n)$，空间复杂度是 $O(1)$。

```java
class Solution {
    public int singleNumber(int[] nums) {

        int res = 0;
        for(int i = 0; i < 32; i ++ ) {
            int cnt = 0; // 记录所有数i进制表示所有数第i位1的个数
            for(int j = 0; j < nums.length; j ++ )
                cnt += nums[j] >> i & 1;
            res += (cnt % 3) * (int)Math.pow(2, i); // 出现三次的数 % 3 = 0
        }
        return res;
    }
}
```

简洁实现

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0; // 记录按位或的结果
        for(int i = 0; i < 32; i ++){
            int t = 0;
            for(int val : nums) t += val >> i & 1;
            res |= t % 3 << i;
        }
        return res;
    }
}
```



### 10、排序

##### （1）912. 排序数组 （归并排序）

**问题**：给你一个整数数组 `nums`，请你将该数组升序排列

**思路**：使用**归并排序**求解

```java
class Solution {
    public static void mergeSort(int[] q, int l, int r){
        if(l >= r) return ; // 递归结束条件

        int mid = l + r >> 1; // 区间的中点
        mergeSort(q, l, mid); mergeSort(q, mid + 1, r); // 分别递归排序左子区间, 右子区间

        // 二路归并
        int i = l, j = mid + 1, k = 0;
        int[] tmp = new int[r - l + 1]; // 临时数组
        while(i <= mid && j <= r){ // 每次选出左右子区间中最小的元素放入临时数组
            if(q[i] <= q[j]) tmp[k ++] = q[i ++];
            else tmp[k ++] = q[j ++];
        }

        // 将剩余的元素放入临时数组
        while(i <= mid) tmp[k ++] = q[i ++];
        while(j <= r) tmp[k ++] = q[j ++];

        // 将排序好的临时数组放入原数组中
        for(int x = l, y = 0; x <= r; x ++) q[x] = tmp[y ++];
    }

    public int[] sortArray(int[] nums) {
        mergeSort(nums,0,nums.length - 1);
        return nums;
    }
}
```

##### （2）剑指 Offer 40. 最小的k个数 (堆)

**堆排序**或者**快速排序**找出数组最小的 k 个数

```java
class Solution {
    final int N = 10010;
    int[] h = new int[N]; // 堆
    int cnt; // 堆的大小

    public void swap(int[] h, int a, int b){ // 交换数组的两个元素
        int temp = h[a];
        h[a] = h[b]; h[b] = temp;
    }

    public void down(int u){ // 堆的向上调整操作
        int t = u;
        if(2*u <= cnt && h[2*u] < h[t]) t = 2*u;
        if(2*u+1 <= cnt && h[2*u + 1] < h[t]) t = 2*u+1;

        if(t != u){
            swap(h, t, u);
            down(t);
        }
    }

    public int[] getLeastNumbers(int[] arr, int k) {
        /*
        // O(nlogn)
        Arrays.sort(arr);
        return Arrays.copyOfRange(arr,0,k);
        */
        
        for(int i = 0; i < arr.length; i ++) h[i + 1] = arr[i]; // 完全二叉树下标从1开始
        cnt = arr.length;
        for(int i = cnt/2; i != 0; i --) down(i); // 构建小根堆
        
        int[] res = new int[k];
        for(int i = 0; i < k; i ++){
            res[i] = h[1]; // 取最小值
            h[1] = h[cnt]; // 删除最小值
            cnt --;
            down(1); // 调整小根堆
        }
        return res;
        
    }
}
```

##### （3）剑指 Offer 51. 数组中的逆序对:writing_hand: 

**解法一**：**归并排序**

使用**归并排序**求数组中的逆序对的数量

- 逆序对是指一个数比它后面的数大就是逆序对，即 $i<j$ 且 $a[i] > a[j]$
- 归并排序将数组分为左右两个子区间，逆序对的分布分为三种情况
  - 情况A：两个数都在左区间
  - 情况B：两个数都在右区间
  - 情况C：一个数在左区间，一个数在右区间
- **通过递归左区间，将情况A转换为情况C；通过递归右区间将情况B转换为情况C**
- **情况C计算**，当 $a[i] > a[j]$ 时，说明 $a[i]$ ~ $a[mid]$ 之间的数都大于 $a[j]$，此时逆序对的数量为 $s[j] = mid - i + 1$
- 总的逆序对的数量为 $s$ 之和

其他解法有：**树状数组**、**冒泡算法**

```java
class Solution {

    public static int mergeSort(int[] q, int l, int r){
        if(l >= r) return 0; // 递归结束的条件, 集合中只有一个元素，逆序对的数量为0

        int mid = l + r >> 1; // 区间的中点
        // res = 左区间的逆序对 + 右区间的逆序对
        int res = mergeSort(q, l, mid) + mergeSort(q, mid+1, r); // 通过递归建情况A和B转换为C

        int i = l, j = mid + 1, k = 0;
        int[] tmp = new int[r-l+1]; // 临时数组
        while(i <= mid && j <= r){
            if(q[i] <= q[j]) tmp[k ++] = q[i ++]; // i < j 且 q[i] <= [j], 顺序对
            else {
                tmp[k ++] = q[j ++];
                res += mid - i + 1; // 计算情况C的逆序对
            }
        }

        while(i <= mid) tmp[k ++] = q[i ++];
        while(j <= r) tmp[k ++] = q[j ++];

        for(int x = l, y = 0; x <= r; x ++) q[x] = tmp[y ++];
        return res; // res 表示区间[l,r]之间的逆序对
    }

    public int reversePairs(int[] nums) {
        return mergeSort(nums, 0, nums.length - 1);
    }
}
```

**解法二**：**树状数组**

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

### 11、数学

##### （1）剑指 Offer 39. 数组中出现次数超过一半的数字:writing_hand: （摩尔投票算法）

**问题**：数组中**有一个数字**出现的次数**超过数组长度的一半**，请找出这个数字。你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**思路**：**摩尔投票算法** 

- 在任何数组中，出现次数大于该数组长度一半的值**只能有一个**

```java
class Solution {
    public int majorityElement(int[] nums) {
        int cnt = 0, candidate = 0; // cnt 表示票数, candidate 表示候选人
        for(int i = 0; i < nums.length; i ++){
            if(cnt == 0) candidate = nums[i]; // 只有票数cnt为0时,才改变候选人
            cnt += candidate == nums[i] ? 1 : -1; // 如果当前的人与候选人一样,则加一票,否则减一票
        }
        return candidate;
    }
}
```

##### （2）229. 求众数 II:writing_hand: (改进的摩尔投票算法)

**问题**：给定一个大小为 $n$ 的整数数组，找出其中所有出现**超过 $⌊ n/3 ⌋$ 次**的元素。

**思路**：**改进的摩尔投票算法**

- 在任何数组中，出现次数大于该数组长度$⌊ n/3 ⌋$的值**最多只有两个**

> 把这道题比作一场多方混战，战斗结果一定只有最多两个阵营幸存，其他阵营被歼灭。数组中的数字即代表某士兵所在的阵营。
>
> 我们维护两个潜在幸存阵营 A 和 B。我们遍历数组，如果遇到了属于 A 或者属于 B 的士兵，则把士兵加入 A 或 B 队伍中，该队伍人数加一。继续遍历。
>
> 如果遇到了一个士兵既不属于 A 阵营，也不属于 B 阵营，这时有两种情况：
>
> - A 阵营和 B 阵营都还有活着的士兵，那么进行一次厮杀，参与厮杀的三个士兵全部阵亡：A 阵营的一个士兵阵亡，B阵营的一个士兵阵亡，这个不知道从哪个阵营来的士兵也阵亡。继续遍历。
> - A 阵营或 B 阵营已经没有士兵了。这个阵营暂时从地球上消失了。那么把当前遍历到的新士兵算作新的潜在幸存阵营，这个新阵营只有他一个人。继续遍历。
>
> 大战结束，最后A和B阵营就是初始人数最多的阵营。判断一下 A，B 的人数是否超过所有人数的三分之一就行了。

```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int a = 0, cnta = 0, b = 0, cntb = 0; // a和b表示候选人, cnta和cntb表示票数

        for(int i = 0; i < nums.length; i ++){ // 摩尔投票
            if((cnta == 0 || a == nums[i]) && nums[i] != b) { // 给A阵营补充兵力
                a = nums[i]; cnta ++;
            } else if (cntb == 0 || b == nums[i]){ // 给B阵营补充兵力
                b = nums[i]; cntb ++;
            } else { cnta --; cntb --; } // A、B、nums[i]进行一场厮杀
        } 

        cnta = 0; cntb = 0; // 分别统计a和b出现的次数
        for(int i = 0; i < nums.length; i ++) 
            if(a == nums[i]) cnta ++ ;
            else if(b == nums[i]) cntb ++;

        List<Integer> res = new ArrayList<>(); // 出现超过 ⌊ n/3 ⌋ 次的元素最多有2个
        if(cnta > (nums.length / 3)) res.add(a);
        if(cntb > (nums.length / 3)) res.add(b);
        return res;
    }
}
```

##### （3）剑指 Offer 16. 数值的整数次方（快速幂）

**问题**：实现 `pow(x, n)`，即计算 `x` 的 `n` 次幂函数（即，$x^n$）。不得使用库函数，同时不需要考虑大数问题。

**思路**：**快速幂**

```java
class Solution {
    double qmi(double a, long k){ // 快速幂求 a^k
        double res = 1;
        while(k != 0){
            if((k & 1) == 1) res *= a;
            k >>= 1;
            a = a * a;
        }
        return res;
    }

    public double myPow(double x, int n) {
        if(n > 0) return qmi(x, n);
        return 1 / qmi(x, -(long)n); // 防止2^31使int溢出
    }
}
```

##### （4）372. 超级次方（高精度快速幂+扩展欧拉费马降幂）

**问题**：你的任务是计算 `ab` 对 `1337` 取模，`a` 是一个正整数，**`b` 是一个非常大的正整数**且会以数组形式给出。

**思路**：**高精度快速幂**和**扩展欧拉费马降幂**

```java
class Solution {
    public int phi(int x){ // 求x的欧拉函数, x 必须是质数
        int res = x;

        for(int i = 2; i <= x / i; i ++) // 分解质因数
            if(x % i == 0){
                res = res / i * (i - 1);
                while(x % i == 0) x /= i;
            }
        if(x > 1) res = res / x * (x - 1);

        return res;
    }

    public int qmi(int a, int k, int p){ // 普通快速幂
        int res = 1;
        while(k != 0) {
            if((k & 1) == 1) res = (int)((long)res * a % p);
            k >>= 1;
            a = (int)((long)a * a % p);
        }
        return res;
    }

    public int superPow(int a, int[] b) { // 高精度快速幂
        if(b.length == 0) return 1;
        int k = 0, p = 1337, phip = phi(p);
        for(int dight : b)
            k = (k * 10 + dight) % phip;

        return qmi(a, k, p) * qmi(a, phip, p) % p; // 扩展欧拉费马降幂
    }
}
```

### 12、堆 (优先队列)

##### （1）剑指 Offer 41. 数据流中的中位数

**问题**：

如何得到一个数据流中的中位数？如果从数据流中读出**奇数个数值**，那么中位数就是所有数值排序之后位于**中间的数值**。如果从数据流中读出**偶数个数值**，那么中位数就是所有数值排序之后中间**两个数的平均值**。

设计一个支持以下两种操作的数据结构：

- `void addNum(int num)` - 从数据流中添加一个整数到数据结构中。
- `double findMedian()` - 返回目前所有元素的中位数。

**思路**：

- 输入的时候将数字分为两半，**小**的一半放在**大根堆**中，**大**的一半放在**小根堆**的中

- 输入的同时保证**两堆的大小之差不超过一**，如果超过，则将数量多的堆弹出堆顶元素放到另一个堆中
- 取中位数的时候，奇数返回数量多的堆顶元素；偶数返回两堆的堆顶平均数即可

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202171310952.png" alt="41-1.png" width="400px" />

```java
class MedianFinder {
    //  bh 维护前一半最小的元素, sh 维护后一半最大的元素, 使得bh.peek() <= sh.peek()
    Queue<Integer> bh = new PriorityQueue<>((i1,i2)->Integer.compare(i2,i1));
    Queue<Integer> sh = new PriorityQueue<>(); 
    
    public MedianFinder() {}
    
    public void addNum(int num) { // 向个数最小的堆中添加元素, 并调整堆使得 bh.peek() <= sh.peek()
        if(bh.size() > sh.size()){
            bh.offer(num);
            sh.offer(bh.poll());
        } else {
            sh.offer(num);
            bh.offer(sh.poll());
        }
    }
    
    public double findMedian() { // 获取数据流中的中位数
        if(bh.size() > sh.size()) return bh.peek();
        else if(bh.size() < sh.size()) return sh.peek();
        return ((long)bh.peek() + sh.peek()) / (double)2;  // 偶数个数值
    }
}
```

##### （2）480. 滑动窗口中位数

**问题**：

中位数是有序序列最中间的那个数。如果序列的长度是偶数，则没有最中间的数；此时中位数是最中间的两个数的平均数。

给你一个数组 `nums`，有一个长度为 `k` 的窗口从最左端滑动到最右端。窗口中有 `k` 个数，每次窗口向右移动 `1` 位。你的任务是找出每次窗口移动后得到的新**窗口中元素的中位数**，并输出由它们组成的数组。

**注意**：`k` 始终**小于等于**输入的非空数组的元素个数

**思路**：此题是上一题的进阶题目，解法类似。区别在于用双优先队列维护一个窗口，当窗口大小满时，要先删除一个数，再添加一个数

```java
class Solution {
    // 用大根堆和小根堆维护一个滑动窗口
    Queue<Integer> bh = new PriorityQueue<>((i1,i2)->Integer.compare(i2,i1));
    Queue<Integer> sh = new PriorityQueue<>();

    public void insert(int val){ // 向窗口中添加一个元素
        if(bh.size() > sh.size()){
            bh.offer(val);
            sh.offer(bh.poll());
        } else {
            sh.offer(val);
            bh.offer(sh.poll());
        }
    }

    public double add(int val){ // 向窗口中添加一个元素, 并返回窗口的中位数
        insert(val);
        if(bh.size() > sh.size()) return bh.peek();
        else if(bh.size() < sh.size()) return sh.peek();
        return ((long)bh.peek() + sh.peek()) / (double)2;
    } 

    public void delete(int val){ // 从窗口中删除一个指定元素
        if(val <= bh.peek()) bh.remove(val);
        else sh.remove(val);
    }

    public double[] medianSlidingWindow(int[] nums, int k) {
        double[] res = new double[nums.length - k + 1];
        int idx = 0;

        for(int l = 0, r = 0; r < nums.length; r ++){
            if(r - l + 1 > k){ // 窗口中元素个数大于k
                delete(nums[l ++]); // 窗口缩小
                res[idx ++] = add(nums[r]); // 窗口扩大
            } 
            else if(r - l + 1 < k) insert(nums[r]); // 窗口中元素个数小于k
            else res[idx ++] = add(nums[r]); / 窗口中元素个数等于k
        }
        return res;
    }
}
```

### 13、滑动窗口问题

##### （1）剑指 Offer 57 - II. 和为s的连续正数序列 :writing_hand: 

**问题**：

输入一个正整数 `target` ，输出所有和为 `target` 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列

**思路**：**滑动窗口**问题，直接暴力滑动窗口

**解法一**：**用队列维护滑动窗口**

```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        int[] q = new int[target]; // 用队列维护滑动窗口, 存储着连续的子序列
        int hh = 0, tt = -1; // 队头指针和队尾指针
        int sum = 0; // sum 滑动窗口所有元素的和

        List<int[]> arr = new ArrayList<>();
        for(int i = 1, j = 1; i <= (target + 1 >> 1);){ // j 始终指向下一个待加入滑动窗口的元素
            while(sum < target){ // 如果连续子序列的和小于target, 就向滑动窗口加入元素
                q[++ tt] = j;
                sum += j;
                ++ j;
            }
            if(sum == target) arr.add(Arrays.copyOfRange(q, hh, tt + 1));
            // sum > target 或者 sum == target 都试图减小sum, 滑动窗口向右移动一位,q队列的队头元素出队列
            int t = q[hh ++];
            sum -= t;
            i ++;
        }

        int[][] res = new int[arr.size()][];
        for(int i = 0; i < arr.size(); i ++) res[i] = arr.get(i);

        return res;
    }
}
```

**解法二**：

```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        List<int[]> arr = new ArrayList<>();

        int sum = 0; // 表示滑动窗口的所有元素之和
        // 滑动窗口, l 表示窗口左边界, r表示窗口右边界
        // 如果sum <= target, r右移, 增大窗口
        // 如果sum > target, l右移, 减小窗口
        for(int l = 1, r = 1; r <= (target + 1 >> 1); r ++){
            sum += r; // 向滑动窗口右侧加入元素
            while(l <= r && sum > target) sum -= l ++; // 右移l, 将滑动窗口的左侧的元素滑出,改变sum

            if(sum == target){ // 得到一个合法序列
                int[] temp = new int[r - l + 1];
                for(int i = 0; i < temp.length; i ++) temp[i] = i + l;
                arr.add(temp);
            }
        }

        int[][] res = new int[arr.size()][];
        for(int i = 0; i < arr.size(); i ++) res[i] = arr.get(i);

        return res;
    }
}
```

##### （2）剑指 Offer 48. 最长不含重复字符的子字符串:writing_hand: 

**问题**：请从字符串中找出一个最长的**不包含重复字符的子字符串**，计算该**最长子字符串的长度**。

**思路**：**滑动窗口**问题

- 用一个滑动窗口维护不包含重复字符的子字符串
- 每次向窗口加入一个元素
- 在加入该元素之前，判断窗口中是否存在该元素
  - 如果存在，则从窗口删除该元素及在该元素之前添加的元素
  - 否则，将该元素加入窗口
- 统计窗口在移动过程中的最大值

**解法一**：用`set`维护一个不重复的窗口

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> set = new HashSet<>(); // 用set维护一个不重复的窗口
        int res = 0; // 最长的不包含重复字符的子字符串的长度

        // 滑动窗口, l窗口的左边界, r窗口的右边界
        for(int l = 0, r = 0; r < s.length(); r ++){
            char ch = s.charAt(r);
            // 如果ch字符在滑动窗口中, 则ch及左边所有字符都滑出滑动窗口
            while(l <= r && set.contains(ch)) set.remove(s.charAt(l ++ )); 
            set.add(ch); // ch 滑入滑动窗口
            res = Math.max(res, set.size());
        }
        return res;
    }
}
```

**解法二**：用一个**哈希表**维护不重复窗口，哈希表增/删时间复杂为$O(1)$，总体速度要比上述方法快

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        boolean[] st = new boolean[256]; //哈希表维护一个窗口,s[i]为true表示ASCII值为i的字符在窗口中出现
        int len = 0; // 窗口长度，表示的不含重复字符的子字符串的长度
        int res = 0; // 最长的不包含重复字符的子字符串的长度

        // 滑动窗口, l窗口的左边界, r窗口的右边界
        for(int l = 0, r = 0; r < s.length(); r ++){
            int ch = (int)s.charAt(r);

            while(l <= r && st[ch]){ // 如果ch字符在窗口中, 则ch及左边所有字符都滑出窗口
                st[(int)s.charAt(l)] = false;
                l ++ ;
                len --;
            }

            st[ch] = true; // ch 滑入窗口
            len ++;
            res = Math.max(res, len);
        }
        return res;
    }
}
```



### 14、大数

##### （1）[剑指 Offer 17. 打印从1到最大的n位数](https://leetcode.cn/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)

**问题**：

输入数字 `n`，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

例子：

```
输入: n = 1
输出: [1,2,3,4,5,6,7,8,9]
```

**思路**：

如果不考虑大数问题，这个题目很简单，找出右边界( $10 \times n - 1$)，然后一个循环即可

```java
class Solution {

    public int[] printNumbers(int n) {
        // 如果不考虑大数问题，这个题目很简单，找出右边界(10*n - 1)，然后一个循环即可
        int[] res = new int[(int)Math.pow(10, n) - 1];
        for(int i = 1; i <= res.length; i ++) res[i - 1] = i;
        return res;
    }
}
```

考虑大数问题，可能会溢出`int32`，因此考虑使用`StringBuilder`存储结果。我们可以使用`dfs`从高位向低位枚举$n$位中的每一位数。

需要注意，除最高位不能取$0$外，其余位都可以取到$0~9$。

```java
class Solution {

    List<String> res = new ArrayList<>();
    int n = 0;
    public void dfs(int u, StringBuilder builder) {
        if(u >= n) return ;

        int start = u == 0 ? 1 : 0;
        for(int i = start; i <= 9; i ++ ) {
            builder.append(i);
            String str = builder.toString();
            res.add(str);
            dfs(u + 1, new StringBuilder(str));
            builder.delete(builder.length() - 1, builder.length());
        }
    }

    public int[] printNumbers(int n) {
        // 如果不考虑大数问题，这个题目很简单，找出右边界(10*n - 1)，然后一个循环即可
        // int[] res = new int[(int)Math.pow(10, n) - 1];
        // for(int i = 1; i <= res.length; i ++) res[i - 1] = i;
        // return res;

        // 考虑大数问题，此题升级为枚举
        this.n = n;
        dfs(0, new StringBuilder());
        System.out.println(res.toString());
        return new int[0];
    }
}
```

### 15、模拟

##### （1）[剑指 Offer 29. 顺时针打印矩阵](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

**问题**：

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

例子：

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**数据范围**：

- `0 <= matrix.length <= 100`
- `0 <= matrix[i].length <= 100`

**思路**：

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if(matrix.length == 0) return new int[0];

        int n = matrix.length, m = matrix[0].length;
        boolean[][] st = new boolean[n][m];

        int[] res = new int[n * m];
        int[] dx = {0, 1, 0, -1}, dy = {1, 0, -1, 0}; // 右，下，左，上

        int x = 0, y = -1, k = 0;
        int circle = (int)Math.ceil(Math.min(n, m) / 2.0);
        while((circle --) != 0) { // 绕的圈数
            for(int i = 0; i < dx.length; i ++ ) { // 四个方向
                int tx = x + dx[i], ty = y + dy[i];
                // 不满足条件时，更换方向
                while(tx >= 0 && tx < n && ty >= 0 && ty < m && !st[tx][ty]) { // 满足条件就朝着当前方向一直走
                    x = tx; y = ty;
                    res[k ++] = matrix[tx][ty];
                    st[tx][ty] = true; // 标记
                    tx += dx[i]; ty += dy[i];
                }
            }
        }
        return res;
    }
}
```

##### （2）[剑指 Offer 61. 扑克牌中的顺子](https://leetcode.cn/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

**问题**：

从**若干副扑克牌**中随机抽 `5` 张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

例子：

```
输入: [1,2,3,4,5]
输出: True
```

**数据范围**：

- 数组长度为 5 
- 数组的数取值为 [0, 13] .

**思路**：

```java
class Solution {
    public boolean isStraight(int[] nums) {
        int n = nums.length;
        Arrays.sort(nums);
        
        int zero = 0;
        for(int i = 0; i < n; i ++ ) 
            if(nums[i] == 0) zero ++ ;

        int cnt = 0;
        for(int i = zero; i < nums.length - 1; i ++ ) {
            if(nums[i + 1] == nums[i]) return false;
            cnt += nums[i + 1] - nums[i] - 1;
        }

        return zero >= cnt;
    }
}
```

##### （3）[剑指 Offer 67. 把字符串转换成整数](https://leetcode.cn/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/) （实现atio, 字符串模拟）

一样的题目· [8. 字符串转换整数 (atoi)](https://leetcode.cn/problems/string-to-integer-atoi/)

**问题**：

写一个函数 `StrToInt`，实现把字符串转换成整数这个功能。不能使用 `atoi` 或者其他类似的库函数。

- 首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

- 当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

- 该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

- 注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。


在任何情况下，若函数不能进行有效的转换时，请返回 0。

说明：假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 $[−2^{31},  2^{31} − 1]$。如果数值超过这个范围，请返回 $INT MAX (2^{31} − 1)$ 或 $INTMIN (−2^{31})$ 。

例子：

```
输入：
"42"
"   -42"
"4193 with words"
"words and 987"
"-91283472332"
"+-2"
"2147483648"

输出：
42
-42
4193
0
-2147483648
0
2147483647
```

**思路**：

- 去除字符串str中的前导0以及无用的开头空格字符，直到找到第一个非空格字符位置;
- 如果str的有效整数之前包含非数字字符，则该字符串无效，返回0;
- 如果str的有效整数之后包含存在多余的字符，应该忽略这些字符;
- 如果数超过数值范围为 $[−2^{31},  2^{31} − 1]$，则返回其边界值。

```java
class Solution {

    public boolean isDigital(char ch) { // 判断ch是否是数字0~9
        if(ch - '0' < 0) return false;
        if(ch - '9' > 0) return false;
        return true;
    }

    public boolean isSign(char ch) { // 判断ch是否是符号 + or -1
        if(ch == '+' || ch == '-') return true;
        return false;
    }

    public String blank(String str) { // 去除字符串str，前面的空格，直到找到第一个非空格字符
        int k = 0;
        while(k < str.length() && str.charAt(k) == ' ') k ++ ;
        return str.substring(k);
    }

    public String zero(String str) { // 去除字符串str，前面的前导0，直到找到第一个非0字符串
        int k = 0;
        while(k < str.length() && str.charAt(k) == '0') k ++ ;
        if (k >= str.length() || !isDigital(str.charAt(k))) k --; // 如果字符为空或当前第一个字符不是数字，则保留一个0
        return str.substring(k); 
    }

    public int strToInt(String str) {
        int sign = 1;
        str = blank(str); // 去除空格
        if(str.length() == 0 || (!isSign(str.charAt(0)) && !isDigital(str.charAt(0))))
            return 0;
        
        if(str.charAt(0) == '-') sign = -1; // 判断符号
        if(isSign(str.charAt(0))) str = str.substring(1);
        if(str.length() == 0 || !isDigital(str.charAt(0))) return 0;
        
        str = zero(str); // 去除前导0

        long res = 0;
        while(str.length() > 0 && isDigital(str.charAt(0))) {
            if(sign * res > Integer.MAX_VALUE) return  Integer.MAX_VALUE;
            else if(sign * res < Integer.MIN_VALUE) return Integer.MIN_VALUE;
            res = res * 10 + (str.charAt(0) - '0');
            str = str.substring(1);
        }

        if(sign * res > Integer.MAX_VALUE) return  Integer.MAX_VALUE;
        else if(sign * res < Integer.MIN_VALUE) return Integer.MIN_VALUE;

        return sign * (int)res;
    }
}
```

##### （4）[剑指 Offer 62. 圆圈中最后剩下的数字](https://leetcode.cn/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/) （约瑟夫环问题）:writing_hand: 

**问题**：

0,1,···,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字（删除后从下一个数字开始计数）。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

例子：

```
输入: n = 5, m = 3
输出: 3
```

**数据范围**：

- `1 <= n <= 10^5`
- `1 <= m <= 10^6`

**思路**：

**方法一：模拟**

集合     (当前是第i个数 + 要向前走m-1个数) mod 集合中元素的个数 == 要删除的元素是第几个数

[0 1 2 3 4]    (1 + 3 - 1) mod 5 == 3 (2)

[0 1 3 4]      (3 + 3 - 1) mod 4 == 1 (0)

[1 3 4]        (1 + 3 - 1) mod 3 == 0 (2 - 1)

[1 3]          (1 + 3 - 1) mod 2 == 1 (0)

[3]

```java
class Solution {
    public int lastRemaining(int n, int m) {
        if(n == 1) return 0;

        ArrayList<Integer> arr = new ArrayList<>();   
        for(int i = 0; i < n; i ++ ) arr.add(i);

        int last = 1; // 初始指向第一个数
        while(arr.size() > 1) {
            last = (last + (m - 1)) % arr.size(); // 计算下一次删除的数是第几个数
            if(last == 0) last = arr.size(); // 特判, 下一次删除的数是第0个数，实际上是删除最后一个数
            arr.remove(last - 1);
            if(last - 1 == arr.size()) last = 1; // 特判, 当前删除的是最后一个数，初始化last指向第一个数
        }

        return arr.get(0);
    }
}
```

更简便的写法

 (当前数的索引位置 + 要向前走m-1个数) mod 集合中元素的个数 == 要删除的元素的索引位置

```java
class Solution {
    public int lastRemaining(int n, int m) {
        if(n == 1) return 0;

        ArrayList<Integer> arr = new ArrayList<>();   
        for(int i = 0; i < n; i ++ ) arr.add(i);

        int last = 0; // 初始指向第一个数的位置
        while(arr.size() > 1) {
            last = (last + (m - 1)) % arr.size(); // 计算下一次删除的数的位置
            arr.remove(last);
        }

        return arr.get(0);
    }
}
```

**方法二：数学**

[这或许是你能找到的最详细约瑟夫环数学推导！]([这或许是你能找到的最详细约瑟夫环数学推导！ (zhihu.com)](https://www.zhihu.com/tardis/bd/art/121159246?source_id=1001)) 



### 16、贪心

##### （1）[剑指 Offer 45. 把数组排成最小的数](https://leetcode.cn/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

**问题**：

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的**所有数字中最小**的一个。

例子：

```
输入: [3,30,34,5,9]
输出: "3033459"
```

**数据范围**：

- `0 < nums.length <= 100`
- 输出结果可能非常大，所以你需要返回一个字符串而不是整数
- 拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

**思路**：

重新定义排序规则：

- 贪心选择，首位数字**小**的排在前面；
- 如果首位数字相同, 两种字符串组合按字典序比较 a + b > b + a ?

```java
class Solution {
    // 自定义排序规则
    public String minNumber(int[] nums) {
        List<Integer> arr = new ArrayList<Integer>();
        for(int i = 0; i < nums.length; i ++ )
            arr.add(nums[i]);

        Collections.sort(arr, (a, b) -> {
            String x = String.valueOf(a);
            String y = String.valueOf(b);
            if (x.charAt(0) == y.charAt(0)) return (x + y).compareTo(y + x);
            return x.charAt(0) - y.charAt(0) > 0 ? 1 : -1;
        });

        StringBuilder builder = new StringBuilder();
        for(int i = 0; i < arr.size(); i ++ )
            builder.append(arr.get(i));

        return builder.toString();
    }
}
```





### :mag_right: 参考文献 

[1] [连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/solution/lian-xu-zi-shu-zu-de-zui-da-he-by-leetco-tiui/) 

[2] https://leetcode.cn/

[3] https://www.acwing.com/

### :closed_lock_with_key: License

本文内容不是将网上的资料随意拼凑而来，除了少部分引用书上和技术文档的原文（这部分内容都在末尾的参考文献中加了出处），其余都是作者的原创。在您引用本文内容或者对内容进行修改演绎时，请署名并以相同方式共享，谢谢。

转载文章请在开头明显处标明该页面地址，公众号等其它转载请联系 [1246550576@qq.com](mailto:1246550576@qq.com)

### 📝排版申明

笔记内容按照一定标准进行排版，以保证内容的可读性。

不使用 `![]()` 这种方式来引用图片，而是用 `<img>` 标签。为了能够控制图片以合适的大小显示，使用 `<div align="center"> <img src=""/> </div>` 达到居中的效果。

排版美化工具：[Typora-Purple](https://theme.typora.io/theme/Purple/)

