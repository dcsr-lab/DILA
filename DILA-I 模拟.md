# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :star2: 表示经典题目；:writing_hand:表示需要再次复习；  
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之模拟



### 1、模拟

#### （1）6014. 构造限制重复的字符串（字符串模拟）

**问题**：

给你一个字符串 `s` 和一个整数 `repeatLimit` ，用 `s` 中的字符**构造一个新字符串** `repeatLimitedString` ，使任何字母 **连续出现的次数**都**不超过 `repeatLimit` 次**。你不必使用 s 中的全部字符。

返回字典序最大的 `repeatLimitedString` 。

字典序定义：如果在字符串 `a` 和 `b` 不同的第一个位置，字符串 `a` 中的字母在字母表中出现时间比字符串 `b` 对应的字母晚，则认为字符串 `a` 比字符串 `b` 字典序更大 。如果字符串中前 `min(a.length, b.length)` 个字符都相同，那么较长的字符串字典序更大。

**思路**：

```java
class Solution {
        
    public String repeatLimitedString(String s, int repeatLimit) {
        int[] hash = new int[26];
        for(int i = 0; i < s.length(); i ++) // 统计字符串s中每个字符出现的次数
            hash[s.charAt(i) - 'a'] ++ ;
        
        StringBuilder sb = new StringBuilder(); // 重新构建的字符串
        
        for(int i = 25; i >= 0;){ // 从字典序的大的开始枚举
            int len = Math.min(repeatLimit, hash[i]); // len是该字符最多可能出现的次数        
            hash[i] -= len;
            while(len -- != 0) // 字符 i + 'a' 重复出现len次
                sb.append((char)(i + 'a'));

            if(hash[i] != 0){ //字符 i+'a'的次数还没有用完,但是i+'a'出现的次数不能超过repeatLimit,需要找次大的字符
                boolean flag = true;
                for(int j = i - 1; j >= 0; j --)
                    if(hash[j] != 0){
                        sb.append((char)(j + 'a'));
                        hash[j] --;
                        flag = false; // 找到次大的字符了
                        break;
                    }
                if(!flag) continue; // 防止i--, 继续使用i+'a'构造字符串
            }
            i -- ;
        }
        return sb.toString();
    }
}
```

#### （2）640. 求解方程 （字符串模拟）

**问题** 

求解一个给定的方程，将`x`以字符串 `"x=#value"` 的形式返回。该方程仅包含 `'+'` ， `'-'` 操作，变量 `x` 和其对应系数。

如果方程没有解，请返回 `"No solution"` 。如果方程有无限解，则返回 `“Infinite solutions”` 。

题目保证，如果方程中只有一个解，则 `'x'` 的值是一个整数。

**思路**

字符串模拟

```java
class Solution {

    public boolean isdigit(char ch) {
        if(ch < '0' || ch > '9') return false;
        return true;
    }

    public int getnum(char[] str, int st, int ed) {
        int res = 0;
        for(int i = st; i <= ed; i ++) {
            int t = str[i] - '0';
            res = res * 10 + t;
        }
        return res;
    }

    public int getcoef(char[] str) { // 求表达式str中x的系数
        int coef = 0;
        for(int i = 0; i < str.length; i ++ )
            if(str[i] == 'x') {
                int j = i - 1;

                int st = j, ed = j;
                while(st >= 0 && isdigit(str[st])) st --;

                int res = st == ed ? 1 : getnum(str, st + 1, ed);
                if(st >= 0 && str[st] == '-') coef -= res;
                else coef += res;
            }
        return coef;
    }

    public int getcons(char[] str) { // 求表达式str中常数项
        int cons = 0;
        for(int i = str.length - 1; i >= 0; ) 
            if(isdigit(str[i])) {
                int j = i;

                int st = j, ed = j;
                while(st >= 0 && isdigit(str[st])) st --;

                int res = getnum(str, st + 1, ed);
                if(st >= 0 && str[st] == '-') cons -= res;
                else cons += res;
            
                i = st;
            } 
            else if(str[i] == 'x') // 跳过未知数项
                while(i >= 0 && (str[i] == 'x' || isdigit(str[i]))) i --;
            else i --; // 跳过运算符 + -
        return cons;
    }

    public String solveEquation(String equation) {
        int pos = 0;
        while(equation.charAt(pos) != '=') pos ++ ;

        char[] left = equation.substring(0, pos).toCharArray();
        char[] right = equation.substring(pos + 1, equation.length()).toCharArray();

        int lx = 0, lnum = 0, rx = 0, rnum = 0;
        
        lx = getcoef(left); // 求左边表达式中x的系数
        rx = getcoef(right); // 求右边表达式中x的系数

        lnum = getcons(left); // 求左边表达式中常数项
        rnum = getcons(right); // 求右边表达式中常数项

        int coef = lx - rx, cons = rnum - lnum;

        if(coef == 0 && cons == 0) return "Infinite solutions"; // 恒等式, 有无穷个解
        else if(coef == 0 && cons != 0) return "No solution"; // 无解
        else if(coef != 0 && cons == 0) return "x=0";

        return "x=" + cons/coef;
    }
}
```

#### （3）[2451. 差值数组不同的字符串](https://leetcode.cn/problems/odd-string-difference/)（字符串模拟）

**问题**：

给你一个字符串数组 `words` ，每一个字符串长度都相同，令所有字符串的长度都为 `n` 。

每个字符串 `words[i]` 可以被转化为一个长度为 `n - 1` 的 **差值整数数组** `difference[i]` ，其中对于 `0 <= j <= n - 2` 有 `difference[i][j] = words[i][j+1] - words[i][j]` 。注意两个字母的差值定义为它们在字母表中 **位置** 之差，也就是说 `'a'` 的位置是 `0` ，`'b'` 的位置是 `1` ，`'z'` 的位置是 `25` 。

- 比方说，字符串 `"acb"` 的差值整数数组是 `[2 - 0, 1 - 2] = [2, -1]` 。

`words` 中所有字符串 **除了一个字符串以外** ，其他字符串的差值整数数组都相同。你需要找到那个不同的字符串。

请你返回 `words`中 **差值整数数组** 不同的字符串。

例子：

```
输入：words = ["adc","wzy","abc"]
输出："abc"
解释：
- "adc" 的差值整数数组是 [3 - 0, 2 - 3] = [3, -1] 。
- "wzy" 的差值整数数组是 [25 - 22, 24 - 25]= [3, -1] 。
- "abc" 的差值整数数组是 [1 - 0, 2 - 1] = [1, 1] 。
不同的数组是 [1, 1]，所以返回对应的字符串，"abc"。
```

**数据范围**：

- `3 <= words.length <= 100`
- `n == words[i].length`
- `2 <= n <= 20`
- `words[i]` 只含有小写英文字母。

思路：优化思路是将`differences`用`string`表示，方便比较。

```java
class Solution {
    public String oddString(String[] words) {
        int n = words.length, m = words[0].length();
        String[] difference  = new String[n];

        for(int i = 0; i < n ; i ++ ) {
            String word = words[i];
            StringBuilder builder = new StringBuilder();
            for(int j = 1; j < m; j ++ ) {
                builder.append(word.charAt(j) - word.charAt(j - 1));
                builder.append(",");
            }
            difference[i] = builder.toString();
        }

        for(int i = 0; i < n; i ++ ) {
            String a = i == 0 ? difference[n - 1] : difference[i - 1];
            String b = difference[i];
            String c = i == n - 1 ? difference[0] : difference[i + 1];
            if(!b.equals(a) && !b.equals(c)) return words[i];
        }

        return "";
    }
}
// 模拟题
// 特殊案例：["abm","bcn","alm"]
```

#### （4）[2460. 对数组执行操作](https://leetcode.cn/problems/apply-operations-to-an-array/)（数组模拟）

**问题**：

给你一个下标从 **0** 开始的数组 `nums` ，数组大小为 `n` ，且由 **非负** 整数组成。

你需要对数组执行 `n - 1` 步操作，其中第 `i` 步操作（从 **0** 开始计数）要求对 `nums` 中第 `i` 个元素执行下述指令：

- 如果 `nums[i] == nums[i + 1]` ，则 `nums[i]` 的值变成原来的 `2` 倍，`nums[i + 1]` 的值变成 `0` 。
- 否则，跳过这步操作。

在执行完 **全部** 操作后，将所有 `0` **移动** 到数组的 **末尾** 。

- 例如，数组 `[1,0,2,0,0,1]` 将所有 `0` 移动到末尾后变为 `[1,2,1,0,0,0]` 。

返回结果数组。

**注意** 操作应当 **依次有序** 执行，而不是一次性全部执行。

例子：

```
输入：nums = [1,2,2,1,1,0]
输出：[1,4,2,0,0,0]
解释：执行以下操作：
- i = 0: nums[0] 和 nums[1] 不相等，跳过这步操作。
- i = 1: nums[1] 和 nums[2] 相等，nums[1] 的值变成原来的 2 倍，nums[2] 的值变成 0 。数组变成 [1,4,0,1,1,0] 。
- i = 2: nums[2] 和 nums[3] 不相等，所以跳过这步操作。
- i = 3: nums[3] 和 nums[4] 相等，nums[3] 的值变成原来的 2 倍，nums[4] 的值变成 0 。数组变成 [1,4,0,2,0,0] 。
- i = 4: nums[4] 和 nums[5] 相等，nums[4] 的值变成原来的 2 倍，nums[5] 的值变成 0 。数组变成 [1,4,0,2,0,0] 。
执行完所有操作后，将 0 全部移动到数组末尾，得到结果数组 [1,4,2,0,0,0] 。
```

**数据范围**：

- `2 <= nums.length <= 2000`
- `0 <= nums[i] <= 1000`

**思路**：

```java
class Solution {
    public int[] applyOperations(int[] nums) {
        for(int i = 0; i < nums.length - 1; i ++ )
            if(nums[i] == nums[i + 1]) {
                nums[i] *= 2;
                nums[i + 1] = 0;
            }
        
        // for(int i = 0, j = nums.length - 1; i < j; ) {
        //     while(nums[i] != 0) i ++ ;
        //     while(nums[j] == 0) j -- ;

        //     if(i < j) {
        //         int temp = nums[i];
        //         nums[i] = nums[j];
        //         nums[j] = temp;
        //     }
        // }
        int[] res = new int[nums.length];
        int k = 0;
        for(int i = 0; i < nums.length; i ++ )
            if(nums[i] != 0) res[k ++] = nums[i];
    		
      	// return nums;
        return res;
    }
}
```

#### （5）[2352. 相等行列对](https://leetcode.cn/problems/equal-row-and-column-pairs/)（数组模拟）

**问题**：

给你一个下标从 **0** 开始、大小为 `n x n` 的整数矩阵 `grid` ，返回满足`Ri` 行和 `Cj` 列相等的行列对 `(Ri, Cj)` 的数目*。*

如果行和列以相同的顺序包含相同的元素（即相等的数组），则认为二者是相等的。

例子：

<img src="https://assets.leetcode.com/uploads/2022/06/01/ex1.jpg" alt="img" style="zoom:50%;" />

```java
输入：grid = [[3,2,1],[1,7,6],[2,7,7]]
输出：1
解释：存在一对相等行列对：
- (第 2 行，第 1 列)：[2,7,7]
```

**数据范围**：

- `n == grid.length == grid[i].length`
- `1 <= n <= 200`
- `1 <= grid[i][j] <= 10^5`

**思路**：哈希表

```java
class Solution {
    public int equalPairs(int[][] grid) {
        int n = grid.length;
        Map<String, Integer> row = new HashMap<>(); // 存储所有行的排列
        Map<String, Integer> col = new HashMap<>(); // 存储所有列的排列

        for(int i = 0; i < n; i ++ ) {
            StringBuilder builder = new StringBuilder();
            for(int j = 0; j < n; j ++ ) builder.append(grid[i][j] + ","); // 加分隔符，防止类似用例[[11,1],[1,11]]
            String s = builder.toString();
            row.put(s, row.getOrDefault(s, 0) + 1);
        }

        for(int j = 0; j < n; j ++ ) {
            StringBuilder builder = new StringBuilder();
            for(int i = 0; i < n; i ++ ) builder.append(grid[i][j] + ",");
            String s = builder.toString();
            col.put(s, col.getOrDefault(s, 0) + 1);
        }

        int cnt = 0;
        for(String key : row.keySet()) // 哈希表
            cnt += row.getOrDefault(key, 0) * col.getOrDefault(key, 0);

        return cnt; 
    }
}
```



#### （6）[LCP 62. 交通枢纽](https://leetcode.cn/problems/D9PW8w/) （图论模拟）

**问题**：

为了缓解「力扣嘉年华」期间的人流压力，组委会在活动期间开设了一些交通专线。`path[i] = [a, b]` 表示有一条从地点 `a`通往地点 `b` 的 **单向** 交通专线。
若存在一个地点，满足以下要求，我们则称之为 **交通枢纽**：

- 所有地点（除自身外）均有一条 **单向** 专线 **直接** 通往该地点；
- 该地点不存在任何 **通往其他地点** 的单向专线。

请返回交通专线的 **交通枢纽**。若不存在，则返回 `-1`。

**注意：**

- 对于任意一个地点，至少被一条专线连通。

例子：

```
输入：path = [[0,1],[0,3],[1,3],[2,0],[2,3]]
输出：3

解释：如下图所示：
地点 0,1,2 各有一条通往地点 3 的交通专线，
且地点 3 不存在任何通往其他地点的交通专线。
```

<img src="https://pic.leetcode-cn.com/1663902572-yOlUCr-image.png" alt="image.png" style="zoom:33%;" />

**数据范围**：

- `1 <= path.length <= 1000`
- `0 <= path[i][0], path[i][1] <= 1000`
- `path[i][0]` 与 `path[i][1]` 不相等

**思路**：考点是图的入度和出度

```java
class Solution {
    public int transportationHub(int[][] path) {
        int[][] d = new int[1010][2]; // d[i][0]表示i的入度, d[i][1]表示i的出度

        Set<Integer> set = new HashSet<>();
        for(int i = 0; i < path.length; i ++) {
            int a = path[i][0], b = path[i][1];
            d[a][1] ++; d[b][0] ++ ; 
            set.add(a); set.add(b);
        }

        int n = set.size(); // 图中节点的个数
        for(int i = 0; i < d.length; i ++ ) {
            int a = d[i][0], b = d[i][1];
            if(a == n - 1 && b == 0) return i; // 入度为n - 1，出度为n - 1的节点是交通枢纽
        }
        return -1; // 不存在交通枢纽
    }
}
```

#### （7）[2496. 数组中字符串的最大值](https://leetcode.cn/problems/maximum-value-of-a-string-in-an-array/) （字符串模拟）

**问题**：

一个由字母和数字组成的字符串的 **值** 定义如下：

- 如果字符串 **只** 包含数字，那么值为该字符串在 `10` 进制下的所表示的数字。
- 否则，值为字符串的 **长度** 。

给你一个字符串数组 `strs` ，每个字符串都只由字母和数字组成，请你返回 `strs` 中字符串的 **最大值** 。

例子：

```
输入：strs = ["alic3","bob","3","4","00000"]
输出：5
解释：
- "alic3" 包含字母和数字，所以值为长度 5 。
- "bob" 只包含字母，所以值为长度 3 。
- "3" 只包含数字，所以值为 3 。
- "4" 只包含数字，所以值为 4 。
- "00000" 只包含数字，所以值为 0 。
所以最大的值为 5 ，是字符串 "alic3" 的值。
```

**数据范围**：

- `1 <= strs.length <= 100`
- `1 <= strs[i].length <= 9`
- `strs[i]` 只包含小写英文字母和数字。

**思路**：

```java
class Solution {

    public boolean check(String str) {
        for(int i = 0; i < str.length(); i ++ )
            if(str.charAt(i) - 'a' >= 0 && str.charAt(i) - 'z' <= 0) 
                return false;
        return true;
    }

    public int maximumValue(String[] strs) {
        int res = Integer.MIN_VALUE;
        for(int i  = 0; i < strs.length; i ++ ) {
            if(check(strs[i])) res = Math.max(Integer.parseInt(strs[i]), res);
            else res = Math.max(strs[i].length(), res);
        }
        return res;
    }
}
```

#### （8）[LCP 73. 探险营地](https://leetcode.cn/problems/0Zeoeg/)（字符串模拟）

问题：

探险家小扣的行动轨迹，都将保存在记录仪中。`expeditions[i]` 表示小扣第 `i` 次探险记录，用一个字符串数组表示。其中的每个「营地」由大小写字母组成，通过子串 `->` 连接。

> 例："Leet->code->Campsite"，表示到访了 "Leet"、"code"、"Campsite" 三个营地。

`expeditions[0]` 包含了初始小扣已知的所有营地；对于之后的第 `i` 次探险(即 `expeditions[i]` 且 i > 0)，如果记录中包含了之前均没出现的营地，则表示小扣 **新发现** 的营地。

请你找出小扣发现新营地最多且索引最小的那次探险，并返回对应的记录索引。如果所有探险记录都没有发现新的营地，返回 `-1`

**注意：**

- 大小写不同的营地视为不同的营地；
- 营地的名称长度均大于 `0`。

例子：

```
输入：expeditions = ["leet->code","leet->code->Campsite->Leet","leet->code->leet->courier"]

输出：1

解释：
初始已知的所有营地为 "leet" 和 "code"
第 1 次，到访了 "leet"、"code"、"Campsite"、"Leet"，新发现营地 2 处："Campsite"、"Leet"
第 2 次，到访了 "leet"、"code"、"courier"，新发现营地 1 处："courier"
第 1 次探险发现的新营地数量最多，因此返回 1
```

**数据范围**：

- $1 <= expeditions.length <= 1000$
- $0 <= expeditions[i].length <= 1000$
- 探险记录中只包含大小写字母和子串"->"

**思路**：

```java
class Solution {
    public int adventureCamp(String[] expeditions) {
        int n = expeditions.length;
        Set<String> set = new HashSet<>();

        int[] cnts = new int[n];
        int t = 0;
        for(int i = 0; i < n; i ++ ) {
            if("".equals(expeditions[i])) continue;
            String[] areas = expeditions[i].split("->");
            for(int j = 0; j < areas.length; j ++)
                if(!set.contains(areas[j])) {
                    set.add(areas[j]);
                    cnts[i] ++ ;
                }
            if(i == 0) cnts[i] = 0;
            if(cnts[i] > cnts[t]) t = i;
        }
        return t == 0 ? -1 : t;
    }
}
```

#### （9）[1253. 重构 2 行二进制矩阵](https://leetcode.cn/problems/reconstruct-a-2-row-binary-matrix/)（数组模拟）

**问题**：

给你一个 `2` 行 `n` 列的二进制数组：

- 矩阵是一个二进制矩阵，这意味着矩阵中的每个元素不是 `0` 就是 `1`。
- 第 `0` 行的元素之和为 `upper`。
- 第 `1` 行的元素之和为 `lower`。
- 第 `i` 列（从 `0` 开始编号）的元素之和为 `colsum[i]`，`colsum` 是一个长度为 `n` 的整数数组。

你需要利用 `upper`，`lower` 和 `colsum` 来重构这个矩阵，并以二维整数数组的形式返回它。

如果有多个不同的答案，那么任意一个都可以通过本题。

如果不存在符合要求的答案，就请返回一个空的二维数组。

例子：

```
输入：upper = 2, lower = 1, colsum = [1,1,1]
输出：[[1,1,0],[0,0,1]]
解释：[[1,0,1],[0,1,0]] 和 [[0,1,1],[1,0,0]] 也是正确答案。
```

**数据范围**：

- `1 <= colsum.length <= 10^5`
- `0 <= upper, lower <= colsum.length`
- `0 <= colsum[i] <= 2`

**思路**：

```java
class Solution {
    public List<List<Integer>> reconstructMatrix(int upper, int lower, int[] colsum) {
        int n = colsum.length;
        int sum = 0, cnt = 0; // sum 记录colsum中元素之和, cnt记录colsum[i]==2的个数
        // 即第一行(第二行)非空的位置应该至少为cnt个
        for(var v : colsum) {
            sum += v;
            if(v == 2) cnt ++ ;
        }
        // 总和不相等, 第一行的非空的位置小于cnt, 第一行的非空的位置小于cnt, 
        if(sum != upper + lower || cnt > upper || cnt > lower) return new ArrayList<>();;
        
        // 贪心：优先为第一行分配，且优先分配colsum[j] == 2的位置
        var a = new ArrayList<Integer>();
        for(int j = 0; j < n; j ++)
            if(colsum[j] == 2 && upper > 0) {
                a.add(1); upper --; colsum[j] --;
            } else a.add(0);
        // 为第一行还没有分配过，且colsum[j] == 1的位置分配数
        for(int j = 0; j < n; j ++)
            if(colsum[j] == 1 && a.get(j) == 0 && upper > 0) {
                a.set(j, 1); upper --; colsum[j] --;
            }
        // 为第二行还没有分配过，且colsum[j] == 1的位置分配数
        var b = new ArrayList<Integer>();
        for(int j = 0; j < n; j ++)
            if(colsum[j] >= 1 && lower > 0) {
                b.add(1); lower -- ; colsum[j] --;
            } else b.add(0);

        var res = new ArrayList<List<Integer>>();
        res.add(a); res.add(b);
        return res;
    }
}
```



#### （10）[2532. 过桥的时间](https://leetcode.cn/problems/time-to-cross-a-bridge/)（堆模拟）

**问题**：

共有 **`k` 位工人**计划将 **`n` 个箱子**从旧仓库移动到新仓库。给你两个整数 `n` 和 `k`，以及一个二维整数数组 `time` ，数组的大小为 `k x 4` ，其中 `time[i] = [leftToRighti, pickOldi, rightToLefti, putNewi]` 。

一条河将两座仓库分隔，只能通过一座桥通行。旧仓库位于河的右岸，新仓库在河的左岸。**开始时，所有 `k` 位工人都在桥的左侧等待**。为了移动这些箱子，第 `i` 位工人（下标从 **0** 开始）可以：

- 从左岸（新仓库）跨过桥到右岸（旧仓库），用时 `leftToRighti` 分钟。
- 从旧仓库选择一个箱子，并返回到桥边，用时 `pickOldi` 分钟。**不同工人可以同时搬起所选的箱子。**
- 从右岸（旧仓库）跨过桥到左岸（新仓库），用时 `rightToLefti` 分钟。
- 将箱子放入新仓库，并返回到桥边，用时 `putNewi` 分钟。不同工人可以同时放下所选的箱子。

如果满足下面任一条件，则认为工人 `i` 的 **效率低于** 工人 `j` ：

- `leftToRighti + rightToLefti > leftToRightj + rightToLeftj`
- `leftToRighti + rightToLefti == leftToRightj + rightToLeftj` 且 `i > j`

工人通过桥时需要遵循以下规则：

- 如果工人 `x` 到达桥边时，工人 `y` 正在过桥，那么工人 `x` 需要在桥边等待。

- 如果没有正在过桥的工人，那么**在桥右边等待的工人可以先过桥**。如果同时有多个工人在右边等待，那么 **效率最低** 的工人会先过桥。
- 如果没有正在过桥的工人，**且桥右边也没有在等待的工人**，同时**旧仓库还剩下至少一个箱子需要搬运**，此时在桥左边的工人可以过桥。如果同时有多个工人在左边等待，那么 **效率最低** 的工人会先过桥。

所有 `n` 个盒子都需要放入新仓库，请你返回最后一个搬运箱子的工人 **到达河左岸** 的时间。

例子：

```
输入：n = 1, k = 3, time = [[1,1,2,1],[1,1,3,1],[1,1,4,1]]
输出：6
解释：
从 0 到 1 ：工人 2 从左岸过桥到达右岸。
从 1 到 2 ：工人 2 从旧仓库搬起一个箱子。
从 2 到 6 ：工人 2 从右岸过桥到达左岸。
从 6 到 7 ：工人 2 将箱子放入新仓库。
整个过程在 7 分钟后结束。因为问题关注的是最后一个工人到达左岸的时间，所以返回 6 。
```

**数据范围**：

- `1 <= n, k <= 10^4`
- `time.length == k`
- `time[i].length == 4`
- `1 <= leftToRighti, pickOldi, rightToLefti, putNewi <= 1000`

思路：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-07-11 14.20.21.png" alt="截屏2023-07-11 14.20.21" style="zoom:50%;" />

如上图所示，我们用两个优先队列分别维护桥左右等待上桥的工人，效率最低的工人会排在堆顶，会先过桥

然后，用分别用两个优先队列维护在两个仓库中工作的工人，最先完成工作的工人会排在堆顶。

我们还需要维护桥的转态$bridge$，$bridge=-1$表示桥上有工人从左向右过桥，$bridge=1$表示桥上有工人从右向左过桥，$bridge=0$表示桥上没有工人过桥。同时用$bworker$维护桥上工人的信息。

最后依据过桥规则进行模拟。

```java
class Solution {
    public int findCrossingTime(int n, int k, int[][] time) {
        // id leftToRighti, pickOldi, rightToLefti, putNewi time
        // 0       1           2           3           4      5
        var l = new PriorityQueue<int[]>((a, b) -> { // 存储桥的左边等待过桥的工人
            if(a[1] + a[3] == b[1] + b[3]) return -(a[0] - b[0]);
            return -(a[1] + a[3] - (b[1] + b[3]));
        });

        var r = new PriorityQueue<int[]>((a, b) -> { // 存储桥的右边等待过桥的工人
            if(a[1] + a[3] == b[1] + b[3]) return -(a[0] - b[0]);
            return -(a[1] + a[3] - (b[1] + b[3]));
        });

        // repol 和 repor越先完成的越容易排在堆顶
        var repol = new PriorityQueue<int[]>((a, b) -> ((a[4] + a[5]) - (b[4] + b[5])));
        var repor = new PriorityQueue<int[]>((a, b) -> ((a[2] + a[5]) - (b[2] + b[5])));

        for(int i = 0; i < time.length; i ++) // 初始状态下，所有的工人都在桥的左边
            l.offer(new int[]{i, time[i][0], time[i][1], time[i][2], time[i][3], 0});

        // 当旧仓库为空 且 桥的左边没有工人 且 桥的右边的工人数量为k时停止
        int t = 0;
        int bridge = 0;
        int[] bworker = new int[6]; 
        while(!(n == 0 && (r.size() + repor.size()) == 0 && (l.size() + repol.size() == k))) { 
            // 检查在新旧仓库搬物品的工人，如果他们已经完成了工作，则让他们加入等待队列            
            while(repol.size() != 0 && t >= repol.peek()[4] + repol.peek()[5]) {
                int[] x = repol.poll();
                x[5] = t;
                l.offer(x);
            }

            while(repor.size() != 0 && t >= repor.peek()[2] + repor.peek()[5]) {
                int[] x = repor.poll();
                x[5] = t;
                r.offer(x);
            }

            if(bridge == -1 && t >= bworker[1] + bworker[5]) {
                int[] x = bworker;
                x[5] = t;
                repor.offer(x);
                bridge = 0;
            }

            if(bridge == 1 && t >= bworker[3] + bworker[5]) {
                int[] x = bworker;
                x[5] = t;
                repol.offer(x);
                bridge = 0;
            }

            if(bridge == 0 && r.size() != 0) {
                bworker = r.poll();
                bworker[5] = t;
                bridge = 1;
            }
            
            if(bridge == 0 && l.size() != 0 && r.size() == 0 && n > 0) {
                bworker = l.poll();
                bworker[5] = t;
                bridge = -1;
                n --; //当该名工人被派出去后,旧仓库的箱子数量应该减少1,该名工人最终一定会将一个箱子搬回新仓库
            }

            t ++;
        }
        return t - 1; // 我们是在t时刻判断最后一名工人到达左岸的时间，因此最后一名工人实际到达左岸时间是t-1
    }
}
```

#### （11）[54. 螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)（矩阵模拟）

**问题**

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

示例 ：

<img src="https://assets.leetcode.com/uploads/2020/11/13/spiral1.jpg" alt="img" style="zoom: 67%;" />

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**数据范围**：

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

**思路**：

**顺时针遍历矩阵输出值**

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int n = matrix.length, m = matrix[0].length;
        boolean[][] st = new boolean[n][m]; // 判重, 只走一次
        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};

        int x = 0, y = -1, k = 1;
        int circle = (int)Math.ceil(Math.min(n, m) / 2.0); // 绕的圈数, 向上取整
        List<Integer> res = new ArrayList<>();
        while((circle --) != 0) {
            for(int i = 0; i < dx.length; i ++) { // 顺时针走
                int tx = x + dx[i], ty = y + dy[i];
                while(tx >= 0 && tx < n && ty >= 0 && ty < m && !st[tx][ty]) { // 一直朝着dx[i], dy[i] 的方向走
                    x = tx; y = ty; // 记录上一次没有越界的tx, ty
                    res.add(matrix[tx][ty]);
                    st[tx][ty] = true;
                    tx += dx[i]; ty += dy[i];
                }
            }
        } 
        return res;
    }
}
```

#### （12）[59. 螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/)（矩阵模拟）

**问题**

给你一个正整数 `n` ，生成一个包含 `1` 到 `n^2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。

例子

<img src="https://assets.leetcode.com/uploads/2020/11/13/spiraln.jpg" alt="img" style="zoom:67%;" />

```
输入：n = 3
输出：[[1,2,3],[8,9,4],[7,6,5]]
```

**数据范围**：

- `1 <= n <= 20`

**思路**：

**顺时针遍历矩阵填值**

```java
class Solution {
    public int[][] generateMatrix(int n) { // 等价与顺时针打印矩阵
        int[][] matrix = new int[n][n];
        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};

        int x = 0, y = -1, k = 1;
        int circle = (int)Math.ceil(n / 2.0); // 绕的圈数, 向上取整
        while((circle --) != 0) {
            for(int i = 0; i < dx.length; i ++) { // 顺时针走
                int tx = x + dx[i], ty = y + dy[i];
                while(tx >= 0 && tx < n && ty >= 0 && ty < n && matrix[tx][ty] == 0) { // 一直朝着dx[i], dy[i] 的方向走
                    x = tx; y = ty; // 记录上一次没有越界的tx, ty
                    matrix[tx][ty] = k ++;
                    tx += dx[i]; ty += dy[i];
                }
            }
        } 
        return matrix;
    }
}
```

#### （13）[2326. 螺旋矩阵 IV](https://leetcode.cn/problems/spiral-matrix-iv/)（矩阵模拟）

**问题**

给你两个整数：`m` 和 `n` ，表示矩阵的维数。

另给你一个整数链表的头节点 `head` 。

请你生成一个大小为 `m x n` 的螺旋矩阵，矩阵包含链表中的所有整数。链表中的整数从矩阵 **左上角** 开始、**顺时针** 按 **螺旋** 顺序填充。如果还存在剩余的空格，则用 `-1` 填充。

返回生成的矩阵。

例子

<img src="https://assets.leetcode.com/uploads/2022/05/09/ex1new.jpg" alt="img" style="zoom: 53%;" />

```
输入：m = 3, n = 5, head = [3,0,2,6,8,1,7,9,4,2,5,5,0]
输出：[[3,0,2,6,8],[5,0,-1,-1,1],[5,2,4,9,7]]
解释：上图展示了链表中的整数在矩阵中是如何排布的。
注意，矩阵中剩下的空格用 -1 填充。
```

**数据范围**：

- `1 <= m, n <= 10^5`
- `1 <= m * n <= 10^5`
- 链表中节点数目在范围 `[1, m * n]` 内
- `0 <= Node.val <= 1000`

**思路**：顺时针变量矩阵，同时遍历链表，**用链表的值填充矩阵**。

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
    public int[][] spiralMatrix(int m, int n, ListNode head) {
        int[][] matrix = new int[m][n];
        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
        
        for(int i = 0; i < m; i ++ ) Arrays.fill(matrix[i], -1);

        int x = 0, y = -1;
        int circle = (int)Math.ceil(Math.min(n, m) / 2.0);
        ListNode u = head;
        while((circle --) != 0) {
            for(int i = 0; i < dx.length; i ++) {
                int tx = x + dx[i], ty = y + dy[i];
                while(tx >= 0 && tx < m && ty >= 0 && ty < n && matrix[tx][ty] == -1) {
                    x = tx; y = ty;
                    matrix[tx][ty] = u.val;
                    u = u.next;
                    if(u == null) return matrix;
                    tx += dx[i]; ty += dy[i];
                }
            }
        }
        return matrix;
    }
}
```

#### （14）[885. 螺旋矩阵 III](https://leetcode.cn/problems/spiral-matrix-iii/)（矩阵模拟）

**问题**

在 `rows x cols` 的网格上，你从单元格 `(rStart, cStart)` 面朝东面开始。网格的西北角位于第一行第一列，网格的东南角位于最后一行最后一列。

你需要以顺时针按螺旋状行走，访问此网格中的每个位置。每当移动到网格的边界之外时，需要继续在网格之外行走（但稍后可能会返回到网格边界）。

最终，我们到过网格的所有 `rows x cols` 个空间。

按照访问顺序返回表示网格位置的坐标列表。

例子

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/08/24/example_2.png)

```
输入：rows = 5, cols = 6, rStart = 1, cStart = 4
输出：[[1,4],[1,5],[2,5],[2,4],[2,3],[1,3],[0,3],[0,4],[0,5],[3,5],[3,4],[3,3],[3,2],[2,2],[1,2],[0,2],[4,5],[4,4],[4,3],[4,2],[4,1],[3,1],[2,1],[1,1],[0,1],[4,0],[3,0],[2,0],[1,0],[0,0]]
```

**数据范围**：

- `1 <= rows, cols <= 100`
- `0 <= rStart < rows`
- `0 <= cStart < cols`

**思路**

```java
class Solution {
    public int[][] spiralMatrixIII(int rows, int cols, int rStart, int cStart) {
        int n = rows, m = cols;
        int[][] matrix = new int[n][m];
        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
        int[][] res = new int[n * m][2];

        int x = rStart, y = cStart, k = 1;
        int lastdx = 0, lastdy = 0;
        res[k - 1] = new int[]{x, y};
        matrix[x][y] = k ++; // 初始化
        
        for(int i = 0; k <= n * m; i = (i + 1) % dx.length) {
            int tx = x, ty = y;
            while((tx + dx[i] >= 0 && tx + dx[i] < n && ty + dy[i] >= 0 &&
                ty + dy[i] < m && matrix[tx + dx[i]][ty + dy[i]] != 0)) {
                tx = tx + lastdx; 
                ty = ty + lastdy;
                if(tx >= 0 && tx < n && ty >= 0 && ty < m && matrix[tx][ty] == 0) {
                    res[k - 1] = new int[]{tx, ty};
                    matrix[tx][ty] = k ++;
                } 
            } 
            tx += dx[i]; ty += dy[i];
            if(tx >= 0 && tx < n && ty >= 0 && ty < m) { 
                res[k - 1] = new int[]{tx, ty};
                matrix[tx][ty] = k ++;
            }
            lastdx = dx[i]; lastdy = dy[i];
            x = tx; y = ty;
        }
        return res;
    }
}
```

#### （15）[874. 模拟行走机器人](https://leetcode.cn/problems/walking-robot-simulation/)（矩阵模拟）

**问题**

机器人在一个无限大小的 XY 网格平面上行走，从点 `(0, 0)` 处开始出发，面向北方。该机器人可以接收以下三种类型的命令 `commands` ：

- `-2` ：向左转 `90` 度
- `-1` ：向右转 `90` 度
- `1 <= x <= 9` ：向前移动 `x` 个单位长度

在网格上有一些格子被视为障碍物 `obstacles` 。第 `i` 个障碍物位于网格点  `obstacles[i] = (xi, yi)` 。

机器人无法走到障碍物上，它将会停留在障碍物的前一个网格方块上，但仍然可以继续尝试进行该路线的其余部分。

返回从原点到机器人所有经过的路径点（坐标为整数）的最大欧式距离的平方。（即，如果距离为 `5` ，则返回 `25` ）

**注意：**

- 北表示 `+Y` 方向。
- 东表示 `+X` 方向。
- 南表示 `-Y` 方向。
- 西表示 `-X` 方向。

例子

```
输入：commands = [4,-1,3], obstacles = []
输出：25
解释：
机器人开始位于 (0, 0)：
1. 向北移动 4 个单位，到达 (0, 4)
2. 右转
3. 向东移动 3 个单位，到达 (3, 4)
距离原点最远的是 (3, 4) ，距离为 32 + 42 = 25
```

**数据范围：**

- `1 <= commands.length <= 104`
- `commands[i]` is one of the values in the list `[-2,-1,1,2,3,4,5,6,7,8,9]`.
- `0 <= obstacles.length <= 10^4`
- `-3 * 104 <= xi, yi <= 3 * 10^4`
- 答案保证小于 `2^31`

**思路**

```java
class Solution {
    public int robotSim(int[] commands, int[][] obstacles) {
        TreeSet<int[]> set = new TreeSet<int[]>((a, b) -> (a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]));
        
        int[] dx = new int[]{0, 1, 0, -1}, dy = new int[]{1, 0, -1, 0};
        int x = 0, y = 0, t = 0,res = 0;

        for(int i = 0; i < obstacles.length; i ++) 
            set.add(new int[]{obstacles[i][0], obstacles[i][1]});

        for(int i = 0; i < commands.length; i ++) {
            int op = commands[i];

            if(op == -1) t = (t + 1) % dx.length;
            else if (op == -2) t = (t - 1 + 4) % dx.length;
            else
                while((op --) != 0) {
                    if(set.contains(new int[]{x + dx[t], y + dy[t]}))  break; // 防止撞上障碍物
                    x += dx[t]; y += dy[t];
                    res = Math.max(res, x * x + y * y);
                }                
        }
        return res;
    }
}
```

#### （16）[860. 柠檬水找零](https://leetcode.cn/problems/lemonade-change/)

**问题**

在柠檬水摊上，每一杯柠檬水的售价为 `5` 美元。顾客排队购买你的产品，（按账单 `bills` 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 `5` 美元、`10` 美元或 `20` 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 `5` 美元。

注意，一开始你手头没有任何零钱。

给你一个整数数组 `bills` ，其中 `bills[i]` 是第 `i` 位顾客付的账。如果你能给每位顾客正确找零，返回 `true` ，否则返回 `false` 。

例子

```
输入：bills = [5,5,5,10,20]
输出：true
解释：
前 3 位顾客那里，我们按顺序收取 3 张 5 美元的钞票。
第 4 位顾客那里，我们收取一张 10 美元的钞票，并返还 5 美元。
第 5 位顾客那里，我们找还一张 10 美元的钞票和一张 5 美元的钞票。
由于所有客户都得到了正确的找零，所以我们输出 true。
```

**数据范围**

- `1 <= bills.length <= 10^5`
- `bills[i]` 不是 `5` 就是 `10` 或是 `20` 

**思路**

```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0, ten = 0;
        for(int i = 0; i < bills.length; i ++) {
            if(bills[i] == 5) five ++ ;
            else if(bills[i] == 10) {
                if(five == 0) return false;
                else five --;
                ten ++ ;
            } else {
                if(ten == 0 && five < 3) return false;
                else if(five == 0) return false;
                
                if(ten == 0) five -= 3;
                else {
                    ten --; five --;
                }
            }
        }
        return true;
    }
}
```

#### （17）[1288. 删除被覆盖区间](https://leetcode.cn/problems/remove-covered-intervals/)（数组模拟）

**问题**

给你一个区间列表，请你删除列表中被其他区间所覆盖的区间。

只有当 `c <= a` 且 `b <= d` 时，我们才认为区间 `[a,b)` 被区间 `[c,d)` 覆盖。

在完成所有删除操作后，请你返回列表中剩余区间的数目。

例子

```
输入：intervals = [[1,4],[3,6],[2,8]]
输出：2
解释：区间 [3,6] 被区间 [2,8] 覆盖，所以它被删除了。
```

**数据范围**

- `1 <= intervals.length <= 1000`
- `0 <= intervals[i][0] < intervals[i][1] <= 10^5`
- 对于所有的 `i != j`：`intervals[i] != intervals[j]`

**思路** 

求解区间问题的一般思路是**，先对区间进行排序，然后在使用二分、贪心、动态规划等方法求解问题**。

此题中，先按区间的左端点排序，左端点值小的排在前面；**如果存在两个区间左端点值相同，那么右端点值大的排在前面**。

为什么要设计这样的排序方式呢？「左端点值相同，那么右端点值大的排在前面」是为了保证**排在前面的区间一定可以覆盖排在后面的区间**。

然后我们从前往后枚举没一个区间，设$[st, ed]$指向前一个**未被覆盖的区间**，初始化指向第一个区间。$[l,r]$指向当前枚举的区间，初始化指向第二个区间（如果存在）。

变量$res$ 用于计数，初始化为 $1$。

- 如果 $[st,ed]$ 覆盖 $[l,r]$，则将跳过$[l,r]$，枚举下一个区间。
- 如果  $[st,ed]$ 与 $[l,r]$相交但不覆盖 或者 不相交，则 将$[st,ed]$的端点值更新为$[l,r]$ ，并将 $res+=1$。

```java
class Solution {
    public int removeCoveredIntervals(int[][] intervals) {
        // 所有的区间按左端点排序
        Arrays.sort(intervals, (a, b) -> (a[0] != b[0] ? a[0] - b[0] : -(a[1] - b[1])));

        int st = intervals[0][0], ed = intervals[0][1];
        int res = 1;
        for(int i = 1; i < intervals.length; i ++) {
            int l = intervals[i][0], r = intervals[i][1];
            if((ed >= l && st < l && ed < r) || l >= ed) { //两个区间相交,但是不覆盖 或不相交
                res ++;
                st = l; ed = r;
            }
        }
        return res;
    }
}
```

#### （18）[833. 字符串中的查找与替换](https://leetcode.cn/problems/find-and-replace-in-string/)（字符串模拟）

**问题**

你会得到一个字符串 `s` (索引从 0 开始)，你必须对它执行 `k` 个替换操作。替换操作以三个长度均为 `k` 的并行数组给出：`indices`, `sources`, `targets`。

要完成第 `i` 个替换操作:

1. 检查 **子字符串** `sources[i]` 是否出现在 **原字符串** `s` 的索引 `indices[i]` 处。
2. 如果没有出现， **什么也不做** 。
3. 如果出现，则用 `targets[i]` **替换** 该子字符串。

例如，如果 `s = "abcd"` ， `indices[i] = 0` , `sources[i] = "ab"`， `targets[i] = "eee"` ，那么替换的结果将是 `"eeecd"` 。

所有替换操作必须 **同时** 发生，这意味着替换操作不应该影响彼此的索引。测试用例保证元素间**不会重叠** 。

例如，一个 `s = "abc"` ， `indices = [0,1]` ， `sources = ["ab"，"bc"]` 的测试用例将不会生成，因为 `"ab"` 和 `"bc"` 替换重叠。

*在对 `s` 执行所有替换操作后返回 **结果字符串** 。*

**子字符串** 是字符串中连续的字符序列。

例子

<img src="https://assets.leetcode.com/uploads/2021/06/12/833-ex2-1.png" alt="img" style="zoom:67%;" />

```
输入：s = "abcd", indices = [0,2], sources = ["ab","ec"], targets = ["eee","ffff"]
输出："eeecd"
解释：
"ab" 从 s 中的索引 0 开始，所以它被替换为 "eee"。
"ec" 没有从原始的 S 中的索引 2 开始，所以它没有被替换。
```

**数据范围**

- `1 <= s.length <= 1000`
- `k == indices.length == sources.length == targets.length`
- `1 <= k <= 100`
- `0 <= indices[i] < s.length`
- `1 <= sources[i].length, targets[i].length <= 50`
- `s` 仅由小写英文字母组成
- `sources[i]` 和 `targets[i]` 仅由小写英文字母组成

**思路**

```java
class Solution {
    public String findReplaceString(String s, int[] indices, String[] sources, String[] targets) {
        int[] idxs = new int[s.length()];
        // sort, 将索引排序
        Arrays.fill(idxs, -1);
        for(int i = 0; i < indices.length; i ++) 
            idxs[indices[i]] = i;
        // 模拟
        StringBuilder str = new StringBuilder("");
        for(int i = 0; i < idxs.length;) {
            if(idxs[i] != -1) {
                boolean flag = true;
                for(int j = 0; j < sources[idxs[i]].length(); j ++)
                    if(s.charAt(i + j) != sources[idxs[i]].charAt(j)) {
                        flag = false;
                        break;
                    }

                if(flag) {
                    str.append(targets[idxs[i]]);
                    i = i + sources[idxs[i]].length();
                    continue;
                }
            }
            str.append(s.charAt(i ++));
        }
        return str.toString();
    }
}
```

#### （19）[2682. 找出转圈游戏输家](https://leetcode.cn/problems/find-the-losers-of-the-circular-game/)

**问题**

`n` 个朋友在玩游戏。这些朋友坐成一个圈，按 **顺时针方向** 从 `1` 到 `n` 编号。从第 `i` 个朋友的位置开始顺时针移动 `1` 步会到达第 `(i + 1)` 个朋友的位置（`1 <= i < n`），而从第 `n` 个朋友的位置开始顺时针移动 `1` 步会回到第 `1` 个朋友的位置。

游戏规则如下：

第 `1` 个朋友接球。

- 接着，第 `1` 个朋友将球传给距离他顺时针方向 `k` 步的朋友。
- 然后，接球的朋友应该把球传给距离他顺时针方向 `2 * k` 步的朋友。
- 接着，接球的朋友应该把球传给距离他顺时针方向 `3 * k` 步的朋友，以此类推。

换句话说，在第 `i` 轮中持有球的那位朋友需要将球传递给距离他顺时针方向 `i * k` 步的朋友。

当某个朋友第 2 次接到球时，游戏结束。

在整场游戏中没有接到过球的朋友是 **输家** 。

给你参与游戏的朋友数量 `n` 和一个整数 `k` ，请按升序排列返回包含所有输家编号的数组 `answer` 作为答案。

例子

```
输入：n = 4, k = 4
输出：[2,3,4]
解释：以下为游戏进行情况：
1）第 1 个朋友接球，第 1 个朋友将球传给距离他顺时针方向 4 步的玩家 —— 第 1 个朋友。
2）第 1 个朋友接到两次球，游戏结束。
```

**数据范围**

- `1 <= k <= n <= 50`

**思路**

```java
class Solution {
    public int[] circularGameLosers(int n, int k) {
        boolean[] st = new boolean[n + 1];
        int idx = 1; // 已经接到球
        for(int i = 1; ; i ++) {
            if(st[idx]) break;
            st[idx] = true;
            idx = ((idx + i * k) % n == 0 ? n : (idx + i * k) % n);
        }

        ArrayList<Integer> arr = new ArrayList<>();
        for(int i = 1; i < st.length; i ++)
            if(!st[i]) arr.add(i);

        int[] res = new int[arr.size()];
        for(int i = 0; i < arr.size(); i ++)
            res[i] = arr.get(i);
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
