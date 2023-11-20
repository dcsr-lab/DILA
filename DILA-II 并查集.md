# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之并查集



#### 1、[6464. 最大公约数遍历](https://leetcode.cn/problems/greatest-common-divisor-traversal/)（分解质因数+并查集）

**问题**：

给你一个下标从 **0** 开始的整数数组 `nums` ，你可以在一些下标之间遍历。对于两个下标 `i` 和 `j`（`i != j`），当且仅当 `gcd(nums[i], nums[j]) > 1` 时，我们可以在两个下标之间通行，其中 `gcd` 是两个数的 **最大公约数** 。

你需要判断 `nums` 数组中 **任意** 两个满足 `i < j` 的下标 `i` 和 `j` ，是否存在若干次通行可以从 `i` 遍历到 `j` 。

如果任意满足条件的下标对都可以遍历，那么返回 `true` ，否则返回 `false` 。

例子：

```
输入：nums = [2,3,6]
输出：true
解释：这个例子中，总共有 3 个下标对：(0, 1) ，(0, 2) 和 (1, 2) 。
从下标 0 到下标 1 ，我们可以遍历 0 -> 2 -> 1 ，我们可以从下标 0 到 2 是因为 gcd(nums[0], nums[2]) = gcd(2, 6) = 2 > 1 ，从下标 2 到 1 是因为 gcd(nums[2], nums[1]) = gcd(6, 3) = 3 > 1 。
从下标 0 到下标 2 ，我们可以直接遍历，因为 gcd(nums[0], nums[2]) = gcd(2, 6) = 2 > 1 。同理，我们也可以从下标 1 到 2 因为 gcd(nums[1], nums[2]) = gcd(3, 6) = 3 > 1 。
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^5`

**思路**：**分解质因数**+**并查集**

两个下标之间可以通行可以定义为：当且仅当 `gcd(nums[i], nums[j]) > 1` 时，我们可以在两个下标之间通行。

暴力的思想是：计算所有两两整数直接的最大公约数，并且判断其之间是否通行。考虑到数据范围，直接暴力枚举时间复杂较高。

它的等价定义是：如果$nums[i]$ 和 $nums[j]$ 的公约数大于1，那么两者的最大公约数也至少大于1。

我们不妨**分解每一个数的所有质因数**，将有公共质因数的数(下标)放到相同的集合中(这意味集合中的所有数都有相同大于1的公约数)，集合中的数(对应的下标)可以互相通行。实际上可以通行的两个下标 `i` 和 `j`仍然满足 `i < j` 的，即可以将集合中的下标从小到大排序，但是实现时是没有必要的。

整数数组中的 $n$ 个数构成了由 $n$ 个节点组成的图，任意满足条件的下标对都可以遍历意思是这个图是否是连通图。我们可以采**用并查集来判断一个图是否是连通图**。

```java
class Solution {

    HashMap<Integer, ArrayList<Integer>> map = new HashMap<>();
    int[] p;

    public void find_primes(int x, int index) { // 分解质因数
        for(int i = 2; i <= x / i; i ++ ) 
            if(x % i == 0) {
                while(x % i == 0) x /= i;

                ArrayList<Integer> arr = map.getOrDefault(i, new ArrayList<Integer>());
                arr.add(index);
                map.put(i, arr);
            }
        if(x > 1) {
            ArrayList<Integer> arr = map.getOrDefault(x, new ArrayList<Integer>());
            arr.add(index);
            map.put(x, arr);
        }
    }

    public int find(int x) {
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public boolean canTraverseAllPairs(int[] nums) { // 并查集判断连通图
        int n = nums.length;

        for(int i = 0; i < n; i ++ ) find_primes(nums[i], i); // 分解质因数

        p = new int[n];
        for(int i = 0; i < n; i ++ ) p[i] = i; // 初始化并查集

        Set<Integer> keySet = map.keySet();
        for(Integer key : keySet) {
            ArrayList<Integer> a = map.get(key);
            for(int i = 1; i < a.size(); i ++ )
                p[find(a.get(i))] = find(a.get(i - 1)); // 集合合并
        }

        int parent = find(0);
        for(int i = 0; i < n; i ++ ) 
            if(parent != find(i)) return false;

        return true;
    }
}
```

#### 2、261. 以图判树

**问题**：

给定编号从 `0` 到 `n - 1` 的 `n` 个结点。给定一个整数 `n` 和一个 `edges` 列表，其中 `edges[i] = [ai, bi]` 表示图中节点 `ai` 和 `bi` 之间存在一条无向边。

如果这些边能够形成一个合法有效的树结构，则返回 `true` ，否则返回 `false` 。

<img src="https://assets.leetcode.com/uploads/2021/03/12/tree2-graph.jpg" alt="img" width="255px" />

**思路**：**树的定义**，包含 `n` 个结点 `n-1` 条边的无向连通图是树

- 先判断是否包含`n`个结点`n-1`条边
- 如果是，合并所有的集合，判断是否是连通图

```java
class Solution {

    int[] p; // 并查集

    public int find(int x) { // 查找 x 的祖宗结点
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public boolean validTree(int n, int[][] edges) {
        if(edges.length != n - 1) return false; // 特判, 树是包含n个结点n-1条边的无向连通图

        p = new int[n];
        for(int i = 0; i < p.length; i ++) p[i] = i; // 初始化并查集
        
        int cnt = n;
        for(int i = 0; i < edges.length; i ++) {
            int x = find(edges[i][0]), y = find(edges[i][1]);
            if(x != y) {
                p[x] = y; // 合并, 两个集合
                cnt -- ;
            }
        }
        if(cnt > 1) return false; // 连通分量的数量>1, 非连通图
        return true;
    }
}
```

#### 3、323. 无向图中连通分量的数目

**问题**：

你有一个包含 `n` 个节点的图。给定一个整数 `n` 和一个数组 `edges` ，其中 `edges[i] = [ai, bi]` 表示图中 `ai` 和 `bi` 之间有一条边。

返回 *图中已连接分量的数目* 。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203121420359.jpeg" alt="img" width="250px" />



**思路**：**并查集求连通块的数量**

```java
class Solution {

    int[] p; // 并查集

    public int find(int x) { // 查找x的祖宗结点
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    public int countComponents(int n, int[][] edges) {
        p = new int[n];
        for(int i = 0; i < p.length; i ++) p[i] = i; // 初始化并查集
        int cnt = n; // 初始连通分量的数目

        for(int i = 0; i < edges.length; i ++) {
            int x = find(edges[i][0]), y = find(edges[i][1]);
            if(x != y) { 
                p[x] = y; // 合并两个集合
                cnt --; // 连通分量的数量减少1
            }
        }
        return cnt;
    }
}
```

#### 4、305. 岛屿数量 II

**问题**：

给你一个大小为 `m x n` 的二进制网格 `grid` 。网格表示一个地图，其中，`0` 表示水，`1` 表示陆地。最初，`grid` 中的所有单元格都是水单元格（即，所有单元格都是 `0`）。

可以通过执行 `addLand` 操作，将某个位置的水转换成陆地。给你一个数组其中 `positions[i] = [ri, ci]` 是要执行第 `i` 次操作的位置 `(ri, ci)` 。

返回一个整数数组 `answer` ，其中 `answer[i]` 是将单元格 `(ri, ci)` 转换为陆地后，地图中岛屿的数量。

**岛屿** 的定义是被「水」包围的「陆地」，通过水平方向或者垂直方向上相邻的陆地连接而成。你可以假设地图网格的四边均被无边无际的「水」所包围。

**示例**：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203031507452.jpeg" alt="img" width="500px" />

```
输入：m = 3, n = 3, positions = [[0,0],[0,1],[1,2],[2,1]]
输出：[1,1,2,3]
```

**思路**：**并查集**

- 用并查集维护所有的陆地，将水平方向或者垂直方向上相邻陆地合并成一个岛屿
- 刚开始岛屿的数量为 `cnt = 0` ，没增加一块陆地 `cnt ++`, 每次相邻的陆地合并 `cnt --`

```python
class Solution:

    def __init__(self):
        self.p = {} # p[x]存储x结点的父结点, 键和值都是二元组

    def find(self, x): # x是一个元组, 求x的祖宗结点
        if self.p[x] != x:
            self.p[x] = self.find(self.p[x])
        return self.p[x]

    def numIslands2(self, m: int, n: int, positions: List[List[int]]) -> List[int]:    
        res, cnt = [], 0 # cnt表示每次操作结束后连通块的数量
        dx, dy = [0, -1, 0, 1], [1, 0, -1, 0] # 偏移量
        
        for i in range(len(positions)):
            t = (positions[i][0], positions[i][1])
            if t in self.p: # positions含有重复元素, t此前已经处理过了
                res.append(cnt)
                continue
            
            self.p[t] = t # 用并查集维护这个集合
            cnt += 1 # 连通块数量加一

            for j in range(4): # 遍历上下左右四个方向
                tx, ty = t[0] + dx[j], t[1] + dy[j]
                if tx < 0 or tx >= m or ty < 0 or ty >= n: # (tx,ty) 越界
                    continue
                if (tx, ty) not in self.p: # g[tx][ty] == 0
                    continue
                a, b = t, (tx, ty)
                x, y = self.find(a), self.find(b) # 分别求a和b的祖宗结点
                if x != y: # a 与 b不是一个集合
                    self.p[y] = x # 合并a与b所在的集合
                    cnt -= 1 # 连通块数量减一
            res.append(cnt)
        return res
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
