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

# 第一部分



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



### 2、排序与查找

#### （1）5217. 将杂乱无章的数字排序

问题：

给你一个下标从 **0** 开始的整数数组 `mapping` ，它表示一个十进制数的映射规则，`mapping[i] = j` 表示这个规则下将数位 `i` 映射为数位 `j` 。

一个整数 **映射后的值** 为将原数字每一个数位 `i` （`0 <= i <= 9`）映射为 `mapping[i]` 。

另外给你一个整数数组 `nums` ，请你将数组 `nums` 中每个数按照它们映射后对应数字非递减顺序排序后返回。

**注意：**

- 如果两个数字映射后对应的数字大小相同，则将它们按照输入中的 **相对顺序** 排序。
- `nums` 中的元素只有在排序的时候需要按照映射后的值进行比较，返回的值应该是输入的元素本身。
- `mapping[i]` 的值 **互不相同** 。

**示例**：

```
输入：mapping = [8,9,4,0,2,1,3,5,7,6], nums = [991,338,38]
输出：[338,38,991]
```

**思路**：

- 采用稳定的排序方式，归并排序
- 两个数据比较大小时，采用映射后的值
- 排序是对原数组进行排序

```java
class Solution {
    
    public int get(int x, int[] mapping){ // 得到x在mapping映射规则下映射后的数
        if(x == 0) return mapping[x]; // 特判
        Deque<Integer> stk = new ArrayDeque<>();
        
        while(x != 0){
            stk.push(mapping[x % 10]);
            x /= 10;
        }
        
        int res = 0;
        while(!stk.isEmpty())
            res = res * 10 + stk.pop();
        return res;
    }
    
    public void mergeSort(int[] a, int[] q, int l, int r){ // 采用a进行数据的大小比较排序, 同时将对应q也进行排序
        if(l == r) return ; // 区间中只有一个数
        
        int mid = l + r >> 1;
        mergeSort(a, q, l, mid); mergeSort(a, q, mid + 1, r); // 分治
        
        int i = l, j = mid + 1, k = 0;
        int[] tmpa = new int[r - l + 10];
        int[] tmpq = new int[r - l + 10];
        
        while(i <= mid && j <= r){ // 二路归并
            if(a[i] <= a[j]){ tmpa[k] = a[i]; tmpq[k ++] = q[i ++];  }
            else { tmpa[k] = a[j]; tmpq[k ++] = q[j ++]; }
        }
        
        while(i <= mid) { tmpa[k] = a[i]; tmpq[k ++] = q[i ++]; }
        while(j <= r) { tmpa[k] = a[j]; tmpq[k ++] = q[j ++]; }
        
        for(int t = 0; t < k; t ++) { q[l + t] = tmpq[t]; a[l + t] = tmpa[t]; }
    }
    
    public int[] sortJumbled(int[] mapping, int[] nums) {
        int[] a = new int[nums.length];
        
        for(int i = 0; i < nums.length; i ++ )
            a[i] = get(nums[i], mapping);
        
        mergeSort(a, nums, 0, nums.length - 1);
        return nums;
    }
}
```

#### （2）[27. 移除元素](https://leetcode.cn/problems/remove-element/)

**问题**：

给你一个数组 `nums` 和一个值 `val`，你需要 **[原地](https://baike.baidu.com/item/原地算法)** 移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 `O(1)` 额外空间并 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组**。元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

例子

```java
输入：nums = [0,1,2,2,3,0,4,2], val = 2
输出：5, nums = [0,1,4,0,3]
解释：函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。注意这五个元素可为任意顺序。你不需要考虑数组中超出新长度后面的元素。
```

**数据范围**：

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 50`
- `0 <= val <= 100`

**思路**：

- 将所有的数从小到大进行排序
- **二分搜索**val的左边界和右边界
- 采用**双指针**算法，将val移到数组的末尾

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if(nums.length == 0) return 0;

        int n = nums.length;
        Arrays.sort(nums); // 排序

        int start = 0, end = 0;
        int l = 0, r = n - 1;
        while(l < r) { // 左边界
            int mid = l + r >> 1;
            if(nums[mid] >= val) r = mid;
            else l = mid + 1;
        }

        if(nums[l] != val) return n;
        start = l;
        r = n - 1;

        while(l < r) { // 右边界
            int mid = l + r  + 1>> 1;
            if(nums[mid] <= val) l = mid;
            else r = mid - 1;
        }
        end = r;

        for(int i = start, j = n - 1; i <= end; i ++, j --) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
        return n - (end - start + 1);
    }
}
```

#### （3）[35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

**问题**：

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 `O(log n)` 的算法。

例子：

```
输入: nums = [1,3,5,6], target = 2
输出: 1
```

**数据范围**：

- `1 <= nums.length <= 104`
- `-104 <= nums[i] <= 104`
- `nums` 为 **无重复元素** 的 **升序** 排列数组
- `-104 <= target <= 104`

思路：

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        if(target > nums[nums.length - 1]) return nums.length; // 特判
        
        int l = 0, r = nums.length - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        return l;
    }
}
```

### 3、双指针

#### （1）228. 汇总区间

**问题**：

给定一个  **无重复元素** 的 **有序** 整数数组 `nums` 。

返回 **恰好覆盖数组中所有数字** 的 **最小有序** 区间范围列表 。也就是说，`nums` 的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个范围但不属于 `nums` 的数字 `x` 。

列表中的每个区间范围 `[a,b]` 应该按如下格式输出：

- `"a->b"` ，如果 `a != b`
- `"a"` ，如果 `a == b`

**示例**：

```python
输入：nums = [0,1,2,4,5,7]
输出：["0->2","4->5","7"]
```

**思路**：**双指针**

- 设置两个指针`i`, `j`，`[i,j)`是要找的连续区间

```python
class Solution:
    def summaryRanges(self, nums: List[int]) -> List[str]:
        n, res = len(nums), []

        i = 0 #  [i, j)表示连续区间, i指向连续区间的开始
        while i < n:
            j = i + 1 # j指针指向连续区间的结束的下一个位置
            while j < n and nums[j] - nums[i] == j - i: # j右移
                j += 1
            # 找到一个连续区间
            st, ed = nums[i], nums[j - 1]
            res.append(str(st) if st == ed else "%d->%d" % (st, ed))
            i = j # 更新i
        return res
```

#### （2）163. 缺失的区间

**问题**：

给定一个排序的整数数组 **nums** ，其中元素的范围在 **闭区间** **[lower, upper]** 当中，返回不包含在数组中的缺失区间。

**示例：**

```
输入: nums = [0, 1, 3, 50, 75], lower = 0 和 upper = 99,
输出: ["2", "4->49", "51->74", "76->99"]
```

**思路**：**双指针**

```python
class Solution:
    def findMissingRanges(self, nums: List[int], lower: int, upper: int) -> List[str]:
        nums = [lower - 1] + nums + [upper + 1] # 加入左右边界
        n, res = len(nums), []

        i = 0 # (i, j) 表示缺失区间
        while i < n:
            j = i + 1
            while j < n and (nums[j] == nums[i] + 1 or nums[j] == nums[i]): # 同步右移 i, j
                i, j = i + 1, j + 1
            
            if j < n: # 找到一个缺失区间
                st, ed = nums[i] + 1, nums[j] - 1
                res.append(str(st) if st == ed else "%d->%d" % (st, ed))
            i = j # 更新 i
        return res
```

#### （3）189. 轮转数组:writing_hand:  

**问题**：

给你一个数组，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。

**示例**：

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
```

**思路**：**三次反转数组**

- 使用**双指针**实现数组反转

- 

- 第一次反转数组 `[0, n -1]`
- 第二次反转数组 `[0, k - 1]`
- 第三次反转数组 `[k, n - 1]`

```java
class Solution {

    public void reverse(int[] a, int l, int r){ // 反转数组中[l, r]的所有数
        for(int i = l, j = r; i < j; i ++, j --) { // 采用双指针反转
            int t = a[i];
            a[i] = a[j];
            a[j] = t;
        }
    }

    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n; // 细节
        reverse(nums, 0, n - 1);
        reverse(nums, 0, k - 1); reverse(nums, k, n - 1);
    }
}
```

#### （4）[1. 两数之和](https://leetcode.cn/problems/two-sum/)（双指针/哈希表）

**问题**：

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并**返回它们的数组下标**。

你可以假设**每种输入只会对应一个答案**。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

例子：

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**数据范围**：

- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **只会存在一个有效答案**

**思路**：

双指针法：$O(nlogn)$

- 构建哈希表，键位$nums[i]$，值为数组下标$i$;
- 对数组$num$从小到大排序；
- 设置两个指针，一个指针$i$指向数组的左端点，另一个指针$j$指向数组的右端点。指针移动的规则为:
  - 如果$nums[i] + nums[j] < target$ ，则 $i ++ $；
  - 如果$nums[i] + nums[j] > target$，则 $j ++$;
  - 如果$nums[i] + nums[j] == target$，迭代停止，找到了两个整数。依据之前构建的哈希表，找到其值对应的下标

```java
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
```

哈希表法：$O(n)$

```java
public int[] twoSum(int[] nums, int target) {
    HashMap<Integer,Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++){
        if(map.containsKey(nums[i])){
            return new int[]{map.get(nums[i]), i};
        }
        map.put(target - nums[i], i);
    }
    return null;
}
```

#### （5）[653. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/two-sum-iv-input-is-a-bst/)（双指针）

**问题**：

给定一个二叉搜索树 `root` 和一个目标结果 `k`，如果二叉搜索树中存在两个元素且它们的和等于给定的目标结果，则返回 `true`。

例子：

<img src="https://assets.leetcode.com/uploads/2020/09/21/sum_tree_1.jpg" alt="img" style="zoom:67%;" />

```
输入: root = [5,3,6,2,4,null,7], k = 9
输出: true
```

**数据范围**：

- 二叉树的节点个数的范围是 `[1, 10^4]`.
- `-10^4 <= Node.val <= 10^4`
- 题目数据保证，输入的 `root` 是一棵 **有效** 的二叉搜索树
- `-10^5 <= k <= 10^5`

**思路**：$O(n)$

- 二叉搜索树的**中序遍历**的结点序列是一个有序序列;
- 设置两个指针，一个指针$i$指向数组的左端点，另一个指针$j$指向数组的右端点。指针移动的规则为:
  - 如果$nums[i] + nums[j] < target$ ，则 $i ++ $；
  - 如果$nums[i] + nums[j] > target$，则 $j ++$;
  - 如果$nums[i] + nums[j] == target$，迭代停止，找到了两个整数

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
    List<Integer> inorder = new ArrayList<>();

    public void dfs(TreeNode u) {

        if(u.left != null) dfs(u.left);
        inorder.add(u.val);
        if(u.right != null) dfs(u.right);
    }

    public boolean findTarget(TreeNode root, int k) {
        if(root == null) return false;
        dfs(root);

        for(int i = 0, j = inorder.size() - 1; i < j;) {
            int a = inorder.get(i), b = inorder.get(j);
            if(a + b < k) i ++ ;
            else if(a + b > k) j --;
            else return true;
        }
        return false;
    }
}
```

#### （6）[167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)（双指针）

**问题**：

给你一个下标从 **1** 开始的整数数组 `numbers` ，该数组已按 **非递减顺序排列** ，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。给你一个下标从 **1** 开始的整数数组 `numbers` ，该数组已按 **非递减顺序排列** ，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。

以长度为 2 的整数数组 `[index1, index2]` 的形式返回这两个整数的下标 `index1` 和 `index2`。

你可以假设每个输入 **只对应唯一的答案** ，而且你 **不可以** 重复使用相同的元素。

你所设计的解决方案必须**只使用常量级的额外空间**。

例子：

```
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。返回 [1, 2] 。
```

**数据范围**：

- `2 <= numbers.length <= 3 * 10^4`
- `-1000 <= numbers[i] <= 1000`
- `numbers` 按 **非递减顺序** 排列
- `-1000 <= target <= 1000`
- **仅存在一个有效答案**

**思路**：$O(n)$

设置两个指针，一个指针$i$指向数组的左端点，另一个指针$j$指向数组的右端点。指针移动的规则为:

- 如果$nums[i] + nums[j] < target$ ，则 $i ++ $；
- 如果$nums[i] + nums[j] > target$，则 $j ++$;
- 如果$nums[i] + nums[j] == target$，迭代停止，找到了两个整数

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        for(int i = 0, j = numbers.length - 1; i < j; ) {
            if(numbers[i] + numbers[j] == target)
                return new int[]{i + 1 ,j + 1};
            else if(numbers[i] + numbers[j] > target) j --;
            else i ++ ;
        }
        return new int[0];
    }
}
```

#### （7）[15. 三数之和](https://leetcode.cn/problems/3sum/)（双指针）

**问题**：

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且`j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

例子：

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**数据范围**：

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

**思路**：

双指针法：$O((1+n)n/2))$

- 将$nums$数组从小到大排序，$nums$数组中可能存在重复元素，排序的好处是可以使得重复的元素聚集在一起，方便去重。
- 将$nums[i],i=0,...,nums.length-1$视为$target$，从$i + 1, ...,nums,length-1$中寻找两数之和等于$target$。可能存在多对数之和等于$target$

```java
for(int l = i + 1, r = n - 1; l < r; ) { // l 从i + 1开始，因为之前的情况已经被讨论过了
    int sum = nums[l] + nums[r] + nums[i];
    if(sum == 0) {
        list.add(Arrays.asList(new Integer[]{ nums[i], nums[l], nums[r]}));
    
        while(l + 1 < r && nums[l] == nums[l + 1]) l ++ ; // 去除重复的元素
        while(l < r - 1 && nums[r] == nums[r - 1]) r --; // 去重重复的元素
        l ++ ; r -- ; // 同时更新l, r
    }
    else if(sum > 0) r -- ;
    else l ++ ;
}
```

- 对于$nums$中的每一个元素都执行上述第二步的算法流程。

完整的代码如下：

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    int n = nums.length;

    Arrays.sort(nums); // 从小到大排序
    for(int i = 0; i < n - 2; i ++ ) {
        if(i > 0 && nums[i] == nums[i - 1]) continue; // 去重, nums[i]已经处理过了
        if(nums[i] > 0) break; // 因为nums经过从小到大排序, i 之后的元素都大于0, 不可能存在有三个数之和等于0的情况

        for(int l = i + 1, r = n - 1; l < r; ) { // l 从i + 1开始，因为之前的情况已经被讨论过了
            int sum = nums[l] + nums[r] + nums[i];
            if(sum == 0) {
                list.add(Arrays.asList(new Integer[]{ nums[i], nums[l], nums[r]}));
            
                while(l + 1 < r && nums[l] == nums[l + 1]) l ++ ; // 去除重复的元素
                while(l < r - 1 && nums[r] == nums[r - 1]) r --; // 去重重复的元素
                l ++ ; r -- ; // 同时更新l, r
            }
            else if(sum > 0) r -- ;
            else l ++ ;
        }
    }
    return list;
}
```

哈希表法：$O(n^2)$，将三数之后转换成两数之和

- 对于任意一个数 $nums[k],k=0,...,nums.length-1$，如果存在另外两个数 $nums[i]$和$nums[j]$，使得$nums[i] + nums[j] + nums[k] == 0$成立。
- 那么如何找到两个数 $nums[i]$和$nums[j]$，使得他们之和等于 $-nums[k]$。这就是两数之和问题。
- 遍历所有的 $k=0,1,...,nums.length-1$，将使$nums[i] + nums[j] + nums[k] == 0$成立的集合$[nums[i],nums[j],nums[k]]$收集到集合`List`中并去重。
- 采用`HashSet`去重，`HashSet<List<Integer>> res = new HashSet<List<Integer>>()`。

```java
class Solution {
    HashSet<List<Integer>> res = new HashSet<List<Integer>>();
  
    // 两数之和O(n):给定一个整数数组 nums 和一个整数目标值 target，在该数组中找出 和为目标值 target 的那两个整数
    public void search(int[] nums, int index, int target) { 
        HashMap<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i < nums.length; i ++ ) {
            if(i == index) continue;
            if(map.containsKey(nums[i])) {
                Integer[] arr = new Integer[]{nums[i], nums[map.get(nums[i])], -target};
                Arrays.sort(arr);
                res.add(Arrays.asList(arr));
            } else map.put(target - nums[i], i);
        }
    }

    public List<List<Integer>> threeSum(int[] nums) {
        HashMap<Integer, Boolean> map = new HashMap<>(); // 去重
        for(int i = 0; i < nums.length; i ++ )
            if(!map.getOrDefault(nums[i], false)) { // 判断是否重复处理过
                search(nums, i, -nums[i]);
                map.put(nums[i], true);
            }
        return new ArrayList<>(res);
    }
}
```

#### （8）[18. 四数之和](https://leetcode.cn/problems/4sum/)（双指针）

**问题**：

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。

请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` 若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

**例子**：

```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**数据范围**：

- `1 <= nums.length <= 200`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`

思路：

双指针法：$O(n^3)$

```javascript
public List<List<Integer>> fourSum(int[] nums, int target) {
    List<List<Integer>> res = new ArrayList<>();
    int n = nums.length;

    Arrays.sort(nums);

    for(int i = 0; i < n - 3; i ++ ) {
        if(i > 0 && nums[i] == nums[i - 1]) continue;
        if(nums[i] > target / 4) break;

        for(int j = i + 1; j < n - 2; j ++ ) {
            if(j > i + 1 && nums[j] == nums[j - 1]) continue;
            if(nums[i] > ((long)target - nums[i]) / 3) break;

            for(int l = j + 1, r = n - 1; l < r;) {
                long sum = nums[i] + nums[j] + nums[l] + nums[r];
                if(sum == target) {
                    res.add(Arrays.asList(new Integer[]{nums[i], nums[j], nums[l], nums[r]}));
                    while(l + 1 < r && nums[l] == nums[l + 1]) l ++ ;
                    while(l < r - 1 && nums[r] == nums[r - 1]) r --;
                    l ++ ; r -- ;
                } else if(sum > target) r --;
                else l ++ ;
            }
        }
    }

    return res;
}
```

哈希表法：

```java
class Solution {
    HashSet<List<Integer>> res = new HashSet<List<Integer>>();

    // 两数之和O(n):给定一个整数数组 nums 和一个整数目标值 target，在该数组中找出 和为目标值 target 的那两个整数
    public void twoSum(int[] nums, int index1, int index2, long target) { 
        HashMap<Long, Integer> map = new HashMap<>();

        for(int i = 0; i < nums.length; i ++ ) {
            if(i == index1 || i == index2) continue;
            if(map.containsKey((long)nums[i])) {
                Integer[] arr = new Integer[]{nums[i], nums[map.get((long)nums[i])], nums[index1], nums[index2]};
                Arrays.sort(arr);
                res.add(Arrays.asList(arr));
            } else map.put(target - nums[i], i);
        }
    }

    public void threeSum(int[] nums, int index, long target ) {
        HashMap<Integer, Boolean> map = new HashMap<>(); // 去重
        for(int i = 0; i < nums.length; i ++ ) {
            if(i == index) continue;
            if(!map.getOrDefault(nums[i], false)) { // 判断是否重复处理过
                twoSum(nums, i, index, target - nums[i]);
                map.put(nums[i], true);
            }
        }
    }

    public List<List<Integer>> fourSum(int[] nums, int target) {
        HashMap<Integer, Boolean> map = new HashMap<>(); // 去重
        for(int i = 0; i < nums.length; i ++ )
            if(!map.getOrDefault(nums[i], false)) { // 判断是否重复处理过
                threeSum(nums, i, (long)target - nums[i]);
                map.put(nums[i], true);
            }
        return new ArrayList<>(res);
    }
}
```



#### （9）[454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)（哈希表）

**问题**：

给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：

- `0 <= i, j, k, l < n`
- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

例子：

```
输入：nums1 = [1,2], nums2 = [-2,-1], nums3 = [-1,2], nums4 = [0,2]
输出：2
解释：
两个元组如下：
1. (0, 0, 0, 1) -> nums1[0] + nums2[0] + nums3[0] + nums4[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> nums1[1] + nums2[1] + nums3[0] + nums4[0] = 2 + (-1) + (-1) + 0 = 0
```

 **数据范围**：

- `n == nums1.length`
- `n == nums2.length`
- `n == nums3.length`
- `n == nums4.length`
- `1 <= n <= 200`
- `-228 <= nums1[i], nums2[i], nums3[i], nums4[i] <= 228`

**思路**：哈希表计数 $O(n^2)$

- 上来就是4重循环，$O(n^4)$，复杂度太高了
- 用 map 缓存两个数组的和$O(n^2)$，两重循环
- 最后要计算元组个数，map 的 key 为两个数组的和，val 为和出现的次数

```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        Arrays.sort(nums1); Arrays.sort(nums2);
        Arrays.sort(nums3); Arrays.sort(nums4);
        
        Map<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i < nums1.length; i ++ ) {
            for(int j = 0; j < nums2.length; j ++ ) {
                int k = -(nums1[i] + nums2[j]);
                map.put(k, map.getOrDefault(k, 0) + 1);
            }
        }

        int res = 0;
        for(int i = 0; i < nums3.length; i ++ )
            for(int j = 0; j < nums4.length; j ++ ) {
                int k = (nums3[i] + nums4[j]);
                res += map.getOrDefault(k, 0);
            }
                
        return res;
    }
}
```



#### （10）[16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)（排序+双指针）

**问题**：

给你一个长度为 `n` 的整数数组 `nums` 和 一个目标值 `target`。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。

返回这三个数的和。

假定每组输入只存在恰好一个解。

例子：

```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

**数据范围**：

- `3 <= nums.length <= 1000`
- `-1000 <= nums[i] <= 1000`
- `-104 <= target <= 104`

**思路**：**排序**和**双指针** $O(n^2)$

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int n = nums.length;
        Arrays.sort(nums);

        int res = 0x3f3f3f3f;
        for(int i = 0; i < n - 2; i ++ ) {
            if(i > 0 && nums[i] == nums[ i - 1]) continue; // 去重

            for(int l = i + 1, r = n - 1; l < r;) {
                int sum = nums[i] + nums[l] + nums[r];
                if(Math.abs(sum - target) < Math.abs(res - target)) res = sum;

                if(sum == target) return sum ;
                else if(sum > target) r --;
                else l ++ ;
            }
        }
        return res;
    }
}
```

#### （11）[287. 寻找重复数](https://leetcode.cn/problems/find-the-duplicate-number/) （快慢指针）

**问题**：

给定一个包含 `n + 1` 个整数的数组 `nums` ，其数字都在 `[1, n]` 范围内（包括 `1` 和 `n`），可知至少存在一个重复的整数。

假设 `nums` 只有 **一个重复的整数** ，返回 **这个重复的数** 。

你设计的解决方案必须 **不修改** 数组 `nums` 且只用常量级 `O(1)` 的额外空间。

例子：

```
输入：nums = [1,3,4,2,2]
输出：2
```

**数据范围**：

- `1 <= n <= 105`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`
- `nums` 中 **只有一个整数** 出现 **两次或多次** ，其余整数均只出现 **一次**

**思路**：

如何证明 `nums` 中至少存在一个重复的数字?

依据**鸽洞原理**，有 $n$ 个鸽子洞，有 $n$ + 1 只鸽子，必然有一个鸽子洞中有两只鸽子。同理，长度为 $n+1$的数组，其数字范围都在 $[1,n]$范围内，至少存在一个数是重复的。

$nums$中**至少存在一个重复**的数字，假设$nums$中**只有一个重复**的整数，且**这个重复的整数出现两次或两次以上**，其余整数均只出现一次。**将该数组转换成链表，如何证明链表中一定存在环**？

以 $[1,3,4,2,2]$为例，按照一下映射规则将数组转换成链表：

$0->1$

$1->3$

$2->4$

$3->2$

$4->2$

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-06-19%2015.14.40.png" alt="截屏2023-06-19 15.14.40" style="zoom:50%;" />

链表中的重复节点必然至少有一条出边，两条入边。该节点必然是构成环的入口节点。

因此，可以采用**快慢指针**来判断链表是否有环，并确定环的入口节点。

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

类似的题目：[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)  [141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/) 

```java
class Solution {
    public int findDuplicate(int[] nums) {
        // 快慢指针从起点出发，慢指针走一步，快指针走两步，它们最终相遇，则一定有环
        int s = 0, f = 0;
        while((s == 0 && f == 0) || s != f) { // 如果有环，快指针和慢指针最终会相遇
            s = nums[s]; // 慢指针走一步
            f = nums[f]; f = nums[f]; // 快指针走两步
        }
        // 慢指针从起点出发，快指针从上次相遇点出发，它们同时走一步，再次相遇点为环的入口
        s = 0;
        while(s != f) {
            s = nums[s];
            f = nums[f];
        }
        return s;
    }
}
```

#### （12）[795. 区间子数组个数](https://leetcode.cn/problems/number-of-subarrays-with-bounded-maximum/)（双指针）

**问题**

给你一个整数数组 `nums` 和两个整数：`left` 及 `right` 。找出 `nums` 中连续、非空且其中最大元素在范围 `[left, right]` 内的子数组，并返回满足条件的子数组的个数。

生成的测试用例保证结果符合 **32-bit** 整数范围。

例子

```
输入：nums = [2,1,4,3], left = 2, right = 3
输出：3
解释：满足条件的三个子数组：[2], [2, 1], [3]
```

**数据范围**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^9`
- `0 <= left <= right <= 10^9`

**思路**

初始化指针$i=-1$和指针 $j=0$，指针$j$ 每次自增$1$。

设  $t$表示以$nums[j]$结尾的子数组的集合，这些子数组其最大元素在范围 $[left, right]$ 内。 $t$ 的属性是子数组的数量。

- 如果枚举$nums[j]$到比$right$大，则更新$i$为$j$。
- 如果$nums[j]$小于$left$，则以$nums[j]$结尾的子数组的集合，这些子数组其最大元素在范围 $[left, right]$ 内，**子数组的个数等于上一轮的$t$；**
- 如果$nums[j]$大于等于$left$，则更新 $t = j - i$。

```java
class Solution {
    public int numSubarrayBoundedMax(int[] nums, int left, int right) {
        int n = nums.length;
        // t表示以nums[j]结尾的子数组的集合，这些子数组其最大元素在范围 [left, right] 内。
        // t的属性是子数组的数量
        int res = 0, t = 0;
        for(int i = -1, j = 0; j < n; j ++) { // i 必须初始化-1
            if(nums[j] > right) i = j; // 如果枚举到比right大的数，则更新i为j
            if(nums[j] >= left) t = j - i; // 否则，t = j - i
            // 如果nums[j]小于left,则以nums[j]结尾的子数组的集合，这些子数组其最大元素在范围 [left, right] 内，子数组的个数等于上一轮的t
            res += t;
        }
        return res;
    }
}
```

#### （13）[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/) :star2:

**问题**

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

例子

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**数据范围**

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

**思路**

双指针算法， 时间复杂度是$O(n)$。

一开始两个指针一个指向开头一个指向结尾，**此时容器的底是最大的**，接下来随着指针向内移动，会造成容器的底变小，在这种情况下想要让容器盛水变多，就只有在容器的高上下功夫。

 那我们该如何决策哪个指针移动呢？我们能够发现不管是左指针向右移动一位，还是右指针向左移动一位，容器的底都是一样的，都比原来减少了 1。这种情况下我们想要让指针移动后的容器面积增大，**就要使移动后的容器的高尽量大**，所以我们选择指针所指的高较小的那个指针进行移动，这样我们就保留了容器较高的那条边，放弃了较小的那条边，以获得有更高的边的机会。

使用两个指针，值小的指针向内移动，这样就减小了搜索空间 **因为面积取决于指针的距离与值小的值乘积，如果值大的值向内移动，距离一定减小，而求面积的另外一个乘数一定小于等于值小的值，因此面积一定减小**，而我们要求最大的面积，因此值大的指针不动，而值小的指针向内移动遍历。

```java
class Solution {
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1; // 双指针算法
        int area = Math.min(height[l], height[r])*(r - l);
        // 短的线向里走
        while(l < r) {
            if(height[l] < height[r]) l ++ ;
            else r -- ;
            area = Math.max(area, Math.min(height[l], height[r])*(r - l));
        }
        return area;
    }
}
```

#### （14）[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/):star2:

**问题**

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

例子

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png" alt="img" style="zoom:75%;" />

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

**数据范围**

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

**思路**

双指针和容斥原理

<img src="https://assets.leetcode-cn.com/solution-static/42/1.png" alt="fig1" style="zoom:48%;" />

初始化两个指针，第一个指针$l$指向数组的起始位置并向右移动，第二个指针$r$指向数组的结束位置并向左移动。

指针$l$ 向右移动的过程中不断更新当前最大值 $leftmax$，并统计上述左上图的面积$leftsum$。

指针$r$ 向左移动的过程中不断更新当前最大值 $rightmax$，并统计上述右上图的面积$rightsum$。

设数组长度为 $n$ ，则上述中下图的面积等于 $maxheight * n$。设数组所有元素之和为 $sum$。

则接水量等于 $leftsum+rightsum-maxheight*n-sum$。

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int leftmax = -1, rightmax = -1, leftsum = 0, rightsum = 0, sum = 0;
        for(int l = 0, r = n - 1; l < n && r >= 0; l ++, r --) {
            leftmax = Math.max(leftmax, height[l]);
            rightmax = Math.max(rightmax, height[r]);

            leftsum += leftmax; rightsum += rightmax;
            sum += height[l];
        }

        return leftsum + rightsum - leftmax * n - sum;
    }
}
```



### 4、二分

#### （1）[1170. 比较字符串最小字母出现频次](https://leetcode.cn/problems/compare-strings-by-frequency-of-the-smallest-character/)

**问题**：

定义一个函数 `f(s)`，统计 `s` 中**（按字典序比较）最小字母的出现频次** ，其中 `s` 是一个非空字符串。

例如，若 `s = "dcce"`，那么 `f(s) = 2`，因为字典序最小字母是 `"c"`，它出现了 2 次。

现在，给你两个字符串数组待查表 `queries` 和词汇表 `words` 。对于每次查询 `queries[i]` ，需统计 `words` 中满足 `f(queries[i])` < `f(W)` 的 **词的数目** ，`W` 表示词汇表 `words` 中的每个词。

请你返回一个整数数组 `answer` 作为答案，其中每个 `answer[i]` 是第 `i` 次查询的结果。

例子：

```
输入：queries = ["cbd"], words = ["zaaaz"]
输出：[1]
解释：查询 f("cbd") = 1，而 f("zaaaz") = 3 所以 f("cbd") < f("zaaaz")。
```

**数据范围**：

- `1 <= queries.length <= 2000`
- `1 <= words.length <= 2000`

- `1 <= queries[i].length, words[i].length <= 10`
- `queries[i][j]`、`words[i][j]` 都由小写英文字母组成

**思路**：排序+二分

```java
class Solution {
    
    public int f(String s) {
        char[] chs = s.toCharArray();
        Arrays.sort(chs);
        int i = 1;
        while(i < chs.length && chs[i] == chs[i - 1]) i ++ ;
        return i;
    }

    public int bsearch(int[] arr, int x) { // 二分用于加速寻找
        int l = 0, r = arr.length - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1; // 枚举右端点
            if(arr[mid] <= x) l = mid; // 因为是<=, 所以如果要查找的数不存在, l指向比x小的第一个数(从左往右)
            else r = mid - 1;
        }
        while(l < arr.length && arr[l] <= x) l += 1; // 找到第一个比它大的数
        return arr.length - l;
    }

    public int[] numSmallerByFrequency(String[] queries, String[] words) {
        int n = queries.length, m = words.length;  
        int[] q = new int[n], w = new int[m];

        for(int i = 0; i < n; i ++ ) q[i] = f(queries[i]);
        for(int i = 0; i < m; i ++ ) w[i] = f(words[i]);

        Arrays.sort(w);

        int[] res = new int[n];

        for(int i = 0; i < n; i ++ )
            res[i] = bsearch(w, q[i]);

        return res;
    }
}
```

#### （2）[2187. 完成旅途的最少时间](https://leetcode.cn/problems/minimum-time-to-complete-trips/)

**问题**：

给你一个数组 `time` ，其中 `time[i]` 表示第 `i` 辆公交车完成 **一趟旅途** 所需要花费的时间。

每辆公交车可以 **连续** 完成多趟旅途，也就是说，一辆公交车当前旅途完成后，可以 **立马开始** 下一趟旅途。每辆公交车 **独立** 运行，也就是说可以同时有多辆公交车在运行且互不影响。

给你一个整数 `totalTrips` ，表示所有公交车 **总共** 需要完成的旅途数目。请你返回完成 **至少** `totalTrips` 趟旅途需要花费的 **最少** 时间。

例子：

```
输入：time = [1,2,3], totalTrips = 5
输出：3
解释：
- 时刻 t = 1 ，每辆公交车完成的旅途数分别为 [1,0,0] 。
  已完成的总旅途数为 1 + 0 + 0 = 1 。
- 时刻 t = 2 ，每辆公交车完成的旅途数分别为 [2,1,0] 。
  已完成的总旅途数为 2 + 1 + 0 = 3 。
- 时刻 t = 3 ，每辆公交车完成的旅途数分别为 [3,1,1] 。
  已完成的总旅途数为 3 + 1 + 1 = 5 。
所以总共完成至少 5 趟旅途的最少时间为 3 。
```

**数据范围**：

- `1 <= time.length <= 10^5`
- `1 <= time[i], totalTrips <= 10^7`

**思路**：

设所有公交车中完成一趟旅途所需花费的最小时间为 $t$。那么完成$totalTrips$趟旅途的最小时间一定位于区间$[0, t * totalTrips]$中。

 $t * totalTrips$ 表示花费最小的时间的公交车跑$totalTrips$多趟所需的总时间。

因此，我们可以尝试从区间$[0, t * totalTrips]$中二分出来。设二分的中点为 $mid$。

- 如果在$mid$ 时间内完成的旅途数严格小于 $totalTrips$，更新左边界 $l = mid + 1$。
- 如果在 $mid$ 时间内完成的旅途数大于等于 $totalTrips$，更新右边界 $r = mid$

那么，在 $mid$ 时间内，我们如何计算出所有的公交车能够完成的旅途数？

考虑一种暴力的做法，设在 $x$ 时间内，第 $i$ 辆公交车车完成旅途所需的时间为 $time[i]$，那么第$i$辆公交车能够完成的旅途数为 $x/time[i]$。我们将所有公交车完成的旅途数加起来。

时间复杂度：二分的时间复杂度为$logD$，$D=t*totalTrips$是二分的区间长度。计算所有车能完成的旅途数的复杂度是 $O(n)$。总的时间复杂度为 $O(nlogD)$。

注意：实现时需要考虑`int`溢出问题

```java
class Solution {

    public long check(int[] time, long x) { // 计算在时间x内，所有的公交车一共跑了多少趟
        // 暴力的做法：在时间x内，第i辆公家车跑了 x/time[i]趟，将所有公交车跑的趟数加起来，时间复杂度是 O(n)
        long cnt = 0;
        for(int i = 0; i < time.length; i ++ )
            cnt += x / time[i];
        return cnt;
    }

    public long bsearch(int[] time, long l, long r, int x) { // log(l - r)
        while(l < r) {
            long mid = (l + r) >> 1;
            if(check(time, mid) < x) l = mid + 1;
            else r = mid;
        }
        return l;
    }

    public long minimumTime(int[] time, int totalTrips) {
        int t = Integer.MAX_VALUE; 
        for(int i = 0; i < time.length; i ++ ) // 从所有公交车中找出一趟旅途所需花费的最小时间
            t = Math.min(t, time[i]);
        // 完成totalTrips趟旅途的最小时间一定位于区间[0, t * totalTrips]中
        // t * totalTrips 表示花费最小的时间的公交车跑totalTrips多趟
        return bsearch(time, 0, (long)t * totalTips, totalTrips);
    }
}
```

#### （3）1064. 不动点

**问题**：

给定已经按 **升序** 排列、由**不同整数**组成的数组 `arr`，返回满足 `arr[i] == i` 的最小索引 `i`。如果不存在这样的 `i`，返回 `-1`。

- `1 <= arr.length < 104`
- `-109 <= arr[i] <= 109`

 **思路**：**二分**，**查找左边界**

```java
class Solution {

    public int bsearch(int[] a){ // 二分枚举左边界
        int l = 0, r = a.length - 1;
        while(l < r){
            int mid = l + r >> 1;
            if(mid <= a[mid]) r = mid;
            else l = mid + 1;
        }
        if(l == r && a[l] == l) return l;
        else return -1;
    }

    public int fixedPoint(int[] arr) {
        return bsearch(arr);
    }
}
```

#### （4）[436. 寻找右区间](https://leetcode.cn/problems/find-right-interval/)

**问题**

给你一个区间数组 `intervals` ，其中 `intervals[i] = [starti, endi]` ，且每个 `starti` 都 **不同** 。

区间 `i` 的 **右侧区间** 可以记作区间 `j` ，并满足 `startj >= endi` ，且 `startj` **最小化** 。注意 `i` 可能等于 `j` 。

返回一个由每个区间 `i` 的 **右侧区间** 在 `intervals` 中对应下标组成的数组。如果某个区间 `i` 不存在对应的 **右侧区间** ，则下标 `i` 处的值设为 `-1` 。

例子

```
输入：intervals = [[1,2]]
输出：[-1]
解释：集合中只有一个区间，所以输出-1。
```

**数据范围**

- `1 <= intervals.length <= 2 * 10^4`
- `intervals[i].length == 2`
- `-10^6 <= starti <= endi <= 10^6`
- 每个间隔的起点都 **不相同**

**思路**：

设当前考虑的区间为 $intervals_i=[start_i, end_i]$。

所有区间的**左端点的值$start_i$都不同**，可以用哈希表存储区间区间左端点$start_i$到索引$i$的映射关系。

将所有区间**按左端点进行排序**。

如何求区间 $interval_i$的右侧区间是谁？我们可以在**第$i$ 个区间到第$n-1$个区间**的左端点$start$构成的**升序数组**中，查找大于等于$end_i$的第一个$start_j$，其索引$j$就是区间$interval_i$的右侧区间。

特殊地，对于区间$[start_i,end_i]$，当 $start_i=end_i$时，其右侧区间就是自己。从第$i$ 个区间开始枚举，可以保证此特殊情况也可以被考虑到。

为了降低时间复杂度，我们可以采用二分查找。

总的时间复杂度是 $O(nlogn)$

```java
class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for(int i = 0; i < n; i ++)   map.put(intervals[i][0], i); // 将左端点映射到下标

        Arrays.sort(intervals, (a, b) -> a[0] - b[0]); // 按左端点从小到大排序区间

        int[] res = new int[n];
        for(int i = 0; i < n; i ++) {
            int st = intervals[i][0], ed = intervals[i][1];
            // 二分的左边界为i，例如 [[1,1],[3,4]] => [0,-1]; 当 st = ed时，其左侧区间还是自己
            int l = i, r = n - 1; // 二分查找左端点比ed大的最小的数
            while(l < r) {
                int mid = l + r >> 1;
                if(intervals[mid][0] >= ed) r = mid;
                else l = mid + 1;
            }
            if(intervals[l][0] >= ed) // 找到了
                res[map.get(intervals[i][0])] = map.get(intervals[l][0]);
            else  res[map.get(intervals[i][0])] = -1; // 不存在右侧区间
        }
        return res;
    }
}
```

#### （5）[69. x 的平方根 ](https://leetcode.cn/problems/sqrtx/):star2:

**问题**

给你一个非负整数 `x` ，计算并返回 `x` 的 **算术平方根** 。

由于返回类型是整数，结果只保留 **整数部分** ，小数部分将被 **舍去 。**

**注意：**不允许使用任何内置指数函数和算符，例如 `pow(x, 0.5)` 或者 `x ** 0.5` 。

例子

```
输入：x = 8
输出：2
解释：8 的算术平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```

**数据范围**

- `0 <= x <= 2^31 - 1`

**思路**

**方案一**：**实数二分** $O(logn)$

实数二分问题通常是**函数求根**、**开方**等问题，比较简单，$while$ 循环终止条件是精度 $e$，更新时 $l$ 和 $r$ 都更新为 $mid$ 即可。

在开方时需要注意，使用**右边界**作为答案的输出。

```java
class Solution {
    public int mySqrt(int x) {
        double l = 0, r = x;
        final double eps = 1e-6;
        while(r - l > eps) { // 实数二分，用eps作为判断条件，左右端点更新为mid
            double mid = (l + r) / 2;
            if(mid * mid - x >= eps) r = mid;
            else l = mid;
        }
        return (int)(Math.floor(r)); // 输出r
    }
}
```

**方案二**：**牛顿迭代法**

设 $a$ 的算术平方根为 $x$，求$a$ 的算术平方根 **等价于** 求方程 $x^2=a$的正根。 可以采用**[牛顿迭代法](https://baike.baidu.com/item/%E7%89%9B%E9%A1%BF%E8%BF%AD%E4%BB%A3%E6%B3%95/10887580)**快速求解方程的根。

牛顿迭代法的迭代公式：$x_{n}=x_{n-1}-\frac{f\left(x_{n-1}\right)}{f^{\prime}\left(x_{n-1}\right)}$

依据$f(x)=x^2-a$，则迭代公式为：
$$
x_n = x_{n-1} - \frac{x^2_{n-1}-a}{2x_{n-1}}
$$
迭代终止的条件是 $|x_n - x_{n-1}|<1 \times 10^{-6}$

```java
class Solution {
    public int mySqrt(int x) {
        
        double last = 0, a = 1;
        final double eps = 1e-6;
        while(Math.abs(a - last) >= eps) {
            last = a;
            a = a - (a * a - x)/(2 * a);
        }
        return (int)Math.floor(a);
    }
}
```

关于二分法和牛顿迭代法求解非线性方程根的问题，详见数值分析课本的内容。

#### （6）[74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/) :star2:

**问题**

给你一个满足下述两条属性的 `m x n` 整数矩阵：

- 每行中的整数从左到右按非递减顺序排列。
- 每行的第一个整数大于前一行的最后一个整数。

给你一个整数 `target` ，如果 `target` 在矩阵中，返回 `true` ；否则，返回 `false` 。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/05/mat.jpg" alt="img" style="zoom:50%;" />

```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```

**数据范围**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 100`
- `-10^4 <= matrix[i][j], target <= 10^4`

**思路**

此题考查二分。给定一个**「蛇形二维数组」**，从该数组中查找某一个数是否存在。

「蛇形二维数组」是指该数组每行中的整数从左到右按非递减顺序排列，每行的第一个整数大于前一行的最后一个整数。

该数组的特点是**每一行递增有序，每一列递增有序，且某一个行的数一定大于前一行的数**。

可以考虑采用**两次二分查找**：

- 在第一列即$matrix[0...n][0]$中，进行第一次二分查找$target$，目的是确定$target$ 属于哪一行。
- 设第一二分查找的结果是第 $i$ 行，在$matrix[0][0...m]$中进行第二次二分查找。

```java
class Solution {
    
    public boolean searchMatrix(int[][] matrix, int target) {
        int n = matrix.length, m = matrix[0].length;
        // 按列二分
        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(matrix[mid][0] <= target) l = mid;
            else r = mid - 1;
        }
        // 按行二分
        int t = l;
        l = 0; r = m - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(matrix[t][mid] <= target) l = mid;
            else r = mid - 1;
        }
        if(matrix[t][l] == target) return true;
        return false;
    }
}
```

#### （7）[875. 爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)

**问题**

珂珂喜欢吃香蕉。这里有 `n` 堆香蕉，第 `i` 堆中有 `piles[i]` 根香蕉。警卫已经离开了，将在 `h` 小时后回来。

珂珂可以决定她吃香蕉的速度 `k` （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 `k` 根。如果这堆香蕉少于 `k` 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 `h` 小时内吃掉所有香蕉的最小速度 `k`（`k` 为整数）。

例子

```
输入：piles = [3,6,7,11], h = 8
输出：4
```

**数据范围**

- `1 <= piles.length <= 10^4`
- `piles.length <= h <= 10^9`
- `1 <= piles[i] <= 10^9`

**思路**

```java
class Solution {

    public boolean check(int[] piles, int mid, int h) {
        int t = 0;
        for(int i = 0; i < piles.length; i ++) {
            t += piles[i] / mid;
            if(piles[i] % mid > 0) t += 1;
        }
        return t > h;
    }

    public int minEatingSpeed(int[] piles, int h) {
        int l = 1, r = 1;
        for(int i = 0; i < piles.length; i ++) 
            r = Math.max(r, piles[i]);

        while(l < r) {
            int mid = l + r >> 1;
            if(check(piles, mid, h)) l = mid + 1;
            else r = mid;
        }
        return l;
    }
}
```

#### （8）[1283. 使结果不超过阈值的最小除数](https://leetcode.cn/problems/find-the-smallest-divisor-given-a-threshold/)

**问题**

给你一个整数数组 `nums` 和一个正整数 `threshold` ，你需要选择一个正整数作为除数，然后将数组里每个数都除以它，并对除法结果求和。

请你找出能够使上述结果小于等于阈值 `threshold` 的除数中 **最小** 的那个。

每个数除以除数后都向上取整，比方说 7/3 = 3 ， 10/2 = 5 。

题目保证一定有解。

示例

```
输入：nums = [2,3,5,7,11], threshold = 11
输出：3
```

**数据范围**

- `1 <= nums.length <= 5 * 10^4`
- `1 <= nums[i] <= 10^6`
- `nums.length <= threshold <= 10^6`

**思路**

```java
class Solution {

    public boolean check(int[] nums, int mid, int threshold) {
        int sum = 0;
        for(int i = 0; i < nums.length; i ++) {
            sum += (nums[i] / mid);
            if(nums[i] % mid > 0) sum ++;
        }
        return sum > threshold;
    }

    public int smallestDivisor(int[] nums, int threshold) {
        int n = nums.length;
        int l = 1, r = (int)1e7;
        while(l < r) {
            int mid = l + r >> 1;
            if(check(nums, mid, threshold)) l = mid + 1;
            else r = mid;
        }
        return l;
    }
}
```



#### （9）[778. 水位上升的泳池中游泳](https://leetcode.cn/problems/swim-in-rising-water/)（二分+BFS）



#### （10）[2258. 逃离火灾](https://leetcode.cn/problems/escape-the-spreading-fire/)（二分+二次BFS）



### 5、递归和回溯

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



### 6、枚举

#### （1）[活字印刷](https://leetcode.cn/problems/letter-tile-possibilities/)

**问题**：

你有一套活字字模 `tiles`，其中每个字模上都刻有一个字母 `tiles[i]`。返回你可以印出的非空字母序列的数目。

**注意：**本题中，每个活字字模只能使用一次。

**数据范围**：

- `1 <= tiles.length <= 7`
- `tiles` 由大写英文字母组成

例：

```
输入："AAB"
输出：8
解释：可能的序列为 "A", "B", "AA", "AB", "BA", "AAB", "ABA", "BAA"。
```

**思路：**

DFS搜索， N个字符组成的字符序列，每个位置有N+1种可能(选N+不选1), (N+1)^(N+1)<=O(16777216)

```go
class Solution {
    // N个字符组成的字符序列，每个位置有N+1种可能(选N+不选1), (N+1)^(N+1)<=O(16777216)
    static String tiles;
    static int[] st; // 1-select, 0-not select
    static int[] way;
    int N; // length

    static HashSet<String> set;

    public void dfs(int u) {
        if(u >= N) {
            StringBuilder res = new StringBuilder();
            for(int i = 0; i < N; i ++) {
                if(st[i] == 1 && way[i] != -1) res.append(tiles.charAt(way[i]));
            }
            if(res.toString().equals("")) return;
            set.add(res.toString());
            return;
        }

        // 第u个位置有N + 1种可能
        for(int i = 0; i < N; i ++) { // 第u个位置选
            if (st[i] == 1) continue;
            st[i] = 1;
            way[u] = i;
            dfs(u + 1);
            way[u] = -1;
            st[i] = 0; // 恢复现场 
        }
        dfs(u + 1); // 第u个位置不选
    }
    
    public int numTilePossibilities(String tiles) {
        this.N = tiles.length();
        this.tiles = tiles;
        this.st = new int[N];
        this.way = new int[N];
        this.set = new HashSet<String>();

        for(int i = 0; i < N; i ++) {
            this.way[i] = -1;
        }

        dfs(0);
        return this.set.size();
    }
}
// "" A A B AA AB AB AAB
```

#### （2）[2475. 数组中不等三元组的数目](https://leetcode.cn/problems/number-of-unequal-triplets-in-array/)

**问题**：

给你一个下标从 **0** 开始的正整数数组 `nums` 。请你找出并统计满足下述条件的三元组 `(i, j, k)` 的数目：

- `0 <= i < j < k < nums.length`
- `nums[i]`、`nums[j]` 和 `nums[k]` **两两不同** 。
  - 换句话说：`nums[i] != nums[j]`、`nums[i] != nums[k]` 且 `nums[j] != nums[k]` 。

返回满足上述条件三元组的数目*。*

例子：

```
输入：nums = [4,4,2,4,3]
输出：3
解释：下面列出的三元组均满足题目条件：
- (0, 2, 4) 因为 4 != 2 != 3
- (1, 2, 4) 因为 4 != 2 != 3
- (2, 3, 4) 因为 2 != 4 != 3
共计 3 个三元组，返回 3 。
注意 (2, 0, 4) 不是有效的三元组，因为 2 > 0 。
```

**数据范围**：

- `3 <= nums.length <= 100`
- `1 <= nums[i] <= 1000`

**思路**：

递归实现组合枚举。但是，**一个组合中的元素不能有重复元**素。

```java
class Solution {

    int n = 0, m = 3; // 从n中选取m个数
    int[] map = new int[1010]; // 存储每个数出现的次数
    int[] a = new int[110]; // 存储nums去重后的数组, 下标从0开始

    // 假设选择的三个不同的数为a[i],a[j],a[k]
    // count = map[a[i]] * map[a[j]] * map[a[k]]
    public int dfs(int u, int start, int count) { // u当前枚举的位置, start表示当前可以选的数为start~n
        if(u - 1 + n - start + 1 < m) return 0; // 剪枝；剩余的数不足m个
        if(u == m + 1) return count; // 找到一个合适的解

        int res = 0;
        for(int i = start; i <= n; i ++)
            res += dfs(u + 1, i + 1, count * map[a[i - 1]]);
        return res;
    }

    public int unequalTriplets(int[] nums) {
        int cnt = 0;
        for(int i = 0; i < nums.length; i ++ ) {
            if(map[nums[i]] == 0) a[cnt ++] = nums[i];
            map[nums[i]] ++ ;
        }
        this.n = cnt;      
        return dfs(1, 1, 1); // 下标从1开始
    }
}
```

#### （3）247. 中心对称数 II:writing_hand: 

**问题**：

给定一个整数 `n` ，返回所有长度为 `n` 的 **中心对称数** 。你可以以 **任何顺序** 返回答案。

**中心对称数** 是一个数字在旋转了 `180` 度之后看起来依旧相同的数字（或者上下颠倒地看）。

**思路**：

- 用指定的数字集，**递归实现排列型枚举**
- 判断所枚举的数是否是一个中心对称数

```java
class Solution {

    int n;
    int[] st; // 用来表示0~(n-1)分别放了那个数
    int[] digit = new int[]{0, 1, 6, 8, 9};
    int[] hash = new int[]{0, 1, -1, 3, -1, -1, 9, -1, 8, 6};
    List<String> arr = new ArrayList<>(); 

    public boolean check(String num) { // 判断num是否中心对称数
        String res = "";
        for(char ch : num.toCharArray())
            res = hash[ch - '0'] + res;
        return res.equals(num); // num旋转180, 即每个数都旋转180, 然后倒序拼接起来
    }

    public void dfs(int u){
        if(u > (0 + n - 1 >> 1)) { //采取同时枚举两边的方式, 枚举到中间位置, 则停
            String res = "";
            if(st[0] == 0 && n > 1) return ; // 特判
            for(int i = 0; i < n; i ++) res = res + st[i];
            if(check(res)) arr.add(res); // 找到一个解
            return ;
        }

        for(int i = 0; i < digit.length; i ++){
            st[u] = digit[i]; // 同时枚举两边
            st[n - 1 - u] = hash[digit[i]];
            dfs(u + 1);
            st[u] = -1; // 恢复现场
            st[n - 1 - u] =  - 1;
        }
    }

    public List<String> findStrobogrammatic(int n) {
        this.n = n;
        st = new int[n + 10];

        dfs(0);
        return arr;
    }
}
```

#### （4）248. 中心对称数 III

**问题**：

给定两个字符串 `low` 和 `high` 表示两个整数 `low` 和 `high` ，其中 `low <= high` ，返回 范围 `[low, high]` 内的 **「中心对称数」**总数 。

**中心对称数** 是一个数字在旋转了 `180` 度之后看起来依旧相同的数字（或者上下颠倒地看）。

**思路**：

- 用指定的数字集，**递归实现排列型枚举**（枚举的数位长度`n`取`[low.length(), high.length()]`）
- 判断所枚举的数是否是一个中心对称数

```java
class Solution {

    int[] st; // 用来表示0~(n-1)分别放了那个数
    int[] digit = new int[]{0, 1, 6, 8, 9};
    int[] hash = new int[]{0, 1, -1, 3, -1, -1, 9, -1, 8, 6};
    int cnt = 0;

    public boolean check(String num) { // 判断num是否中心对称数
        String res = "";
        for(char ch : num.toCharArray())
            res = hash[ch - '0'] + res;
        return res.equals(num); // num旋转180, 即每个数都旋转180, 然后倒序拼接起来
    }

    public void dfs(int u, int n, String l, String r){
        if(u > (0 + n - 1 >> 1)) { //采取同时枚举两边的方式, 枚举到中间位置, 则停
            String res = "";
            if(st[0] == 0 && n > 1) return ; // 特判
            for(int i = 0; i < n; i ++) res = res + st[i];
            if(check(res) && Long.parseLong(l) <= Long.parseLong(res) && 
                Long.parseLong(res) <= Long.parseLong(r))
                    cnt ++; // 找到一个解
            return ;
        }

        for(int i = 0; i < digit.length; i ++){
            st[u] = digit[i]; // 同时枚举两边
            st[n - 1 - u] = hash[digit[i]];
            dfs(u + 1, n, l, r);
            st[u] = -1; // 恢复现场
            st[n - 1 - u] =  - 1;
        }
    }

    public int strobogrammaticInRange(String low, String high) {
        st = new int[high.length() + 10];

        for(int i = low.length(); i <= high.length(); i ++)
            dfs(0, i, low, high); // 枚举所有长度为i的中心对称数, 并且要在[low, high]范围内
        
        return cnt;
    }
}
```

#### （5）[646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/)

**问题**

给你一个由 `n` 个数对组成的数对数组 `pairs` ，其中 `pairs[i] = [lefti, righti]` 且 `lefti < righti` 。

现在，我们定义一种 **跟随** 关系，当且仅当 `b < c` 时，数对 `p2 = [c, d]` 才可以跟在 `p1 = [a, b]` 后面。我们用这种形式来构造 **数对链** 。

找出并返回能够形成的 **最长数对链的长度** 。

你不需要用到所有的数对，你可以以任何顺序选择其中的一些数对来构造。

例子

```
输入：pairs = [[1,2],[7,8],[4,5]]
输出：3
解释：最长的数对链是 [1,2] -> [4,5] -> [7,8] 。
```

**数据范围**

- `n == pairs.length`
- `1 <= n <= 1000`
- `-1000 <= lefti < righti <= 1000`

思路

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        int n = pairs.length;
        Arrays.sort(pairs, (a, b) -> a[0] - b[0]);

        int[] f = new int[n + 1];

        for(int i = 1; i <= n; i ++)
            for(int j = 0; j < i; j ++)
                if(j == 0 || pairs[j - 1][1] < pairs[i - 1][0])
                    f[i] = Math.max(f[i], f[j] + 1);
        
        int k = 0;
        for(int i = 1; i <= n; i ++)
            if(f[k] < f[i]) k = i;
        return f[k];
    }
}
```

### 7、哈希表

#### （1）2006. 差的绝对值为 K 的数对数目

**问题**：

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回数对 `(i, j)` 的数目，满足 `i < j` 且 `|nums[i] - nums[j]| == k` 。

`|x|` 的值定义为：

- 如果 `x >= 0` ，那么值为 `x` 。
- 如果 `x < 0` ，那么值为 `-x` 。

**思路**：**哈希表**和**计数**

```java
class Solution {
    public int countKDifference(int[] nums, int k) {

        int[] hash = new int[210]; // hash[i] 存储i出现的次数
        int res = 0;

        for(int i = 0; i < nums.length; i ++){ // |nums[i]-nums[j]| = k, 即 nums[j] = nums[i]+/-k 
            int a = nums[i] + k, b = nums[i] - k; // num[j] = num[i] + k 或者 nums[j] = nums[i] - k
            res += hash[a]; // 累加 a 出现的次数
            if(b >= 0) res += hash[b]; // 累加 b 出现的次数

            hash[nums[i]] ++; // 更新nums[i]出现的次数
        }

        return res;
    }
}
```

#### （2）1748. 唯一元素的和

**问题**：

给你一个整数数组 `nums` 。数组中唯一元素是那些只出现 **恰好一次** 的元素。

请你返回 `nums` 中唯一元素的 **和** 。

**思路**：**哈希表**和**计数**

```java
class Solution {

    public int sumOfUnique(int[] nums) {
        int[] hash = new int[110]; // hash[i]表示i出现的次数

        int res = 0;
        for(int i = 0; i < nums.length; i ++){
            int a = nums[i];

            if(hash[a] == 0) res += a; // a 之前没有出现过, 则累加
            else if(hash[a] == 1) res -= a; // a 之前出现过一次, 现在也出现了, 则将之前加上的a减去
            // a 之前出现过两次, 现在也出现了, 则跳过
            hash[a] ++ ;
        }
        return res;
    }
}
```

#### （3）[1419. 数青蛙](https://leetcode.cn/problems/minimum-number-of-frogs-croaking/):writing_hand:  

**问题**：

给你一个字符串 `croakOfFrogs`，它表示不同青蛙发出的蛙鸣声（字符串 `"croak"` ）的组合。由于同一时间可以有多只青蛙呱呱作响，所以 `croakOfFrogs` 中会混合多个 `“croak”` *。* 

请你返回模拟字符串中所有蛙鸣所需不同青蛙的最少数目。

要想发出蛙鸣 "croak"，青蛙必须 依序 输出 ‘c’, ’r’, ’o’, ’a’, ’k’ 这 5 个字母。如果没有输出全部五个字母，那么它就不会发出声音。如果字符串 `croakOfFrogs` 不是由若干有效的 "croak" 字符混合而成，请返回 `-1` 。

**数据范围**：

- `1 <= croakOfFrogs.length <= 105`
- 字符串中的字符只有 `'c'`, `'r'`, `'o'`, `'a'` 或者 `'k'`

**思路**：**贪心**

- croakOfFrogs的字符必须是‘c’, ’r’, ’o’, ’a’, ’k’之一;
- croakOfFrogs的字符必须是‘c’, ’r’, ’o’, ’a’, ’k’五个字符出现的次数相等;
- 贪心地看, c总是尽可能匹配其之后最近的r, 其他字符同理。所以在统计r时, c出现的次数一定要大于r出现的次数; 否则必然至少存在一个r无法与匹配, 此时croakOfFrogs不是一个有效组合;
- 另一方面，我们其实可以把蛙鸣看成是若干区间进行重叠，遍历到croakOfFrogs的每个位置都会有一个“各个字母的频数”状态，**所有位置的状态中“并行度”最大的地方就是最少需要的青蛙数量**。由于k是一次蛙鸣的结尾，c是一次蛙鸣的开头，且c>=k，所以某个位置的“并行度”其实就是c-k，维护这个最大值即可。

```java
class Solution {
    public int minNumberOfFrogs(String croakOfFrogs) {
        int c = 0, r = 0, o = 0, a = 0, k = 0;
        
        int res = 0;
        for(int i = 0; i < croakOfFrogs.length(); i ++ ) {
            char ch = croakOfFrogs.charAt(i);
            if(ch == 'c') {
                c ++;
                res = Math.max(c - k, res);
            } 
            else if (ch == 'r' && c > r) r ++;
            else if (ch == 'o' && r > o) o ++;
            else if (ch == 'a' && o > a) a ++;
            else if (ch == 'k' && a > k) k ++;
            else return -1; // 特判
            
        }

        return c == r && r == o && o == a && a == k ? res : -1;
    }
}
// c ck k ccckk ckk
// ck ck c
```

#### （4）246. 中心对称数

**问题**：

中心对称数是指一个数字在旋转了 180 度之后看起来依旧相同的数字（或者上下颠倒地看）。

请写一个函数来判断该数字是否是中心对称数，其输入将会以一个字符串的形式来表达数字。

**思路**：

- `0~9`之间的中心对称数有`0(0),1(1),6(9),8(8),9(6)` 
- 一个数是中心对称数，当且仅当其每一个数位都是中心对称数，且旋转了180度之后与原数字相同

```java
class Solution {
    public boolean isStrobogrammatic(String num) {
        //0, 1, 2, 3, 4, 5, 6, 7, 8, 9
        //0, 1, -, -, -, -, 9, -, 8, 6

        int[] hash = new int[20];
        Arrays.fill(hash, -1);
        hash[0] = 0;    hash[1] = 1; // 中心对称数位
        hash[6] = 9;    hash[8] = 8;    hash[9] = 6;

        String res = "";
        for(char ch : num.toCharArray())
            if(hash[ch - '0'] == -1) // 如果num包含非中心对称数位, 则一定不是中心对称数
                return false;
            else res = hash[ch - '0'] + res;
        return res.equals(num); // num旋转180, 即每个数都旋转180, 然后倒序拼接起来
    }
}
```

### 8、链表

#### （1）82. 删除排序链表中的重复元素 II

给定一个已排序的链表的头 `head` ， *删除原始链表中所有重复数字的节点，只留下不同的数字* 。返回 *已排序的链表* 。

例1：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202209062358031.jpeg" alt="img" width="500px" />

例2：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202209062358007.jpeg" alt="img" width="350px"  />

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
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null) return head; // 特判
        // pre 是 cur的前继结点, post是cur的后继结点, h 指向头结点
        ListNode pre = new ListNode(0x3f3f3f3f, head), cur = head, post = head.next, h = pre;
        while(cur != null && post != null) {
            int cnt = 0;
            while(post != null && cur.val == post.val) { // 找到cur之后第一个与cur.val值不相同的结点
                post = post.next;
                cnt ++ ;
            }

            if(cnt > 0)  pre.next = post; // 存在重复结, 删除所有重复结点
            else pre = cur; // 不存在重复结点
            cur = post;
            if(post != null) post = post.next;
        }

        return h.next;
    }
}
```



#### （2）[24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)（递归）

**问题**

给你一个链表，两两**交换其中相邻的节点**，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg" alt="img" style="zoom:80%;" />

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**数据范围** 

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`

**思路**

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

#### （3）[25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/) （递归）

**问题**

给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表。

`k` 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]
```

**数据范围**

- 链表中的节点数目为 `n`
- `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

**思路**

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
    public ListNode reverseKGroup(ListNode head, int k) {
        if(head == null) return head;
        ListNode cur = head;
        int cnt = k;
        while((cnt --) != 0) {
            cur = cur.next;
            if(cur == null && cnt != 0) return head; // 将最后剩余的节点保持原有顺序
        }

        ListNode p = null, q = head;
        cnt = k;
        while((cnt --) != 0) {
            ListNode t = q.next; // 记录q的next节点
            q.next = p; // 将q的next指向q的前一个节点p
            p = q;
            q = t;
        }
        head.next = reverseKGroup(cur, k);
        return p;
    }
}
```

#### （4）[189. 轮转数组](https://leetcode.cn/problems/rotate-array/) （翻转）

**问题**

给定一个整数数组 `nums`，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。

例子

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]
```

**数据范围**

- `1 <= nums.length <= 10^5`
- `-2^31 <= nums[i] <= 2^31 - 1`
- `0 <= k <= 10^5`

**思路**

```java
class Solution {

    public void reverse(int[] a, int l, int r){ // 反转数组中[l, r]的所有数
        for(int i = l, j = r; i < j; i ++, j --) { // 采用双指针反转
            int t = a[i];
            a[i] = a[j];
            a[j] = t;
        }
    }

    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n; // 细节
        reverse(nums, 0, n - 1);
        reverse(nums, 0, k - 1); reverse(nums, k, n - 1);
    }
}
```

#### （5）[61. 旋转链表](https://leetcode.cn/problems/rotate-list/) （翻转）

**问题**

给你一个链表的头节点 `head` ，旋转链表，将链表每个节点向右移动 `k` 个位置。

例子

<img src="https://assets.leetcode.com/uploads/2020/11/13/rotate1.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,2,3,4,5], k = 2
输出：[4,5,1,2,3]
```

**数据范围**

- 链表中节点的数目在范围 `[0, 500]` 内
- `-100 <= Node.val <= 100`
- `0 <= k <= 2 * 10^9`

**思路**

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

    public ListNode reverse(ListNode p, ListNode q, int k) {
        while(q != null && (k --) != 0) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }
        return p;
    }

    public ListNode rotateRight(ListNode head, int k) {  
        if(head == null || k == 0 || head.next == null) return head;

        int cnt = 1;
        ListNode t = head;
        while((t = t.next) != null) cnt ++;
        if(k % cnt == 0) return head;

        head = reverse(null, head, 0x3f3f3f3f);

        ListNode second = head;
        for(int i = 1; i <= k % cnt; i ++) second = second.next;

        second = reverse(null, second, 0x3f3f3f3f);

        ListNode first = head;
        head = reverse(null, head, k % cnt);
        
        first.next = second;
        return head;
    }
}
```

#### （6）[725. 分隔链表](https://leetcode.cn/problems/split-linked-list-in-parts/) （模拟）

**问题**

给你一个头结点为 `head` 的单链表和一个整数 `k` ，请你设计一个算法将链表分隔为 `k` 个连续的部分。

**每部分的长度应该尽可能的相等：任意两部分的长度差距不能超过 1 。**这可能会导致有些部分为 null 。

这 `k` 个部分应该按照在链表中出现的顺序排列，并且**排在前面的部分的长度应该大于或等于排在后面的长度**。

返回一个由上述 `k` 部分组成的数组。

例子

<img src="https://assets.leetcode.com/uploads/2021/06/13/split1-lc.jpg" alt="img" style="zoom:50%;" />

```
输入：head = [1,2,3], k = 5
输出：[[1],[2],[3],[],[]]
解释：
第一个元素 output[0] 为 output[0].val = 1 ，output[0].next = null 。
最后一个元素 output[4] 为 null ，但它作为 ListNode 的字符串表示是 [] 。
```

**数据范围**

- 链表中节点的数目在范围 `[0, 1000]`
- `0 <= Node.val <= 1000`
- `1 <= k <= 50`

**思路**

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
    public ListNode[] splitListToParts(ListNode head, int k) {
        if(head == null) return new ListNode[k];

        ListNode[] res = new ListNode[k];
        
        int cnt = 1;
        ListNode t = head;
        while((t = t.next) != null) cnt ++;

        if(cnt <= k) {
            int idx = 0;
            for(ListNode i = head; i != null;) {
                res[idx ++] = i;
                ListNode p = i.next;
                i.next = null;
                i = p;
            }
        } else {
            int each = cnt / k;
            ListNode p = null, q = head;
            for(int i = 0; i < k; i ++) {
                if(p != null) p.next = null;
                res[i] = q;
                int n = i < cnt - k * each ? each + 1 : each;
                for(int j = 0; j < n; j ++) {
                    p = q;
                    q = q.next;
                }
            }
        }
        return res;
    }
}

// cnt <= k
// cnt > k
// 每部分的节点个数 [cnt / k] 
// 前cnt - k * [cnt / k]个部分, 每个部分再增加一个节点
```

#### （7）[19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/) 

**问题**

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**数据范围**

- 链表中结点的数目为 `sz`
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

**思路**

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
        ListNode pre = null, i = head, j = head;
        int k = 1;

        while(i != null) {
            if(k > n) { // 当i走过第n个结点时, j开始走
                pre = j;
                j = j.next;
            }
            i = i.next;
            k ++ ;
        }

        if(pre != null) pre.next = j.next;
        else head = head.next; // 删除的是头结点
        return head;
    }
}
```

#### （8）[1171. 从链表中删去总和值为零的连续节点](https://leetcode.cn/problems/remove-zero-sum-consecutive-nodes-from-linked-list/) 

**问题** 

给你一个链表的头节点 `head`，请你编写代码，反复删去链表中由 **总和** 值为 `0` 的连续节点组成的序列，直到不存在这样的序列为止。

删除完毕后，请你返回最终结果链表的头节点。

例子

```
输入：head = [1,2,-3,3,1]
输出：[3,1]
提示：答案 [1,2,1] 也是正确的。
```

**数据范围**

- 给你的链表中可能有 `1` 到 `1000` 个节点。
- 对于链表中的每个节点，节点的值：`-1000 <= node.val <= 1000`.

**思路**

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
    public ListNode removeZeroSumSublists(ListNode head) {
        List<Integer> arr = new ArrayList<>();
        arr.add(0x3f3f3f3f); // 站位
        for(ListNode i = head; i != null; i = i.next) arr.add(i.val); // 链表转换成数组, 下标从1开始

        int n  = arr.size();
        int[] s = new int[n]; // 前缀和, 下标从1开始
        for(int i = 1; i < n; i ++ )
            s[i] = s[i - 1] + arr.get(i);

        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i ++ ) {
            if(map.get(s[i]) == null) { 
                map.put(s[i], i); // 存储s[i]出现的下标i
                continue;
            }
            for(int j = map.get(s[i]) + 1; j < n && j <= i; j ++ ) {
                arr.set(j, 0x3f3f3f3f); // 删除和等于0的子数组
                map.remove(s[j]); // 将map中对应的元素去除
            }
            map.put(s[i], i);
        }

        ListNode h = new ListNode(), p = h; 
        for(int i = 1; i < arr.size(); i ++) {
            if(arr.get(i) == 0x3f3f3f3f) continue;
            p.next = new ListNode(arr.get(i));
            p = p.next;
        }
        return h.next;
    }
}
//   [1,2,-3,3,1]
// [0,1,3, 0,3,4]
```

#### （9）[328. 奇偶链表](https://leetcode.cn/problems/odd-even-linked-list/) 

**问题**

给定单链表的头节点 `head` ，将所有索引为奇数的节点和索引为偶数的节点分别组合在一起，然后返回重新排序的列表。

**第一个**节点的索引被认为是 **奇数** ， **第二个**节点的索引为 **偶数** ，以此类推。

请注意，偶数组和奇数组内部的相对顺序应该与输入时保持一致。

你必须在 `O(1)` 的额外空间复杂度和 `O(n)` 的时间复杂度下解决这个问题。

例子

<img src="https://assets.leetcode.com/uploads/2021/03/10/oddeven-linked-list.jpg" alt="img" style="zoom:80%;" />

```
输入: head = [1,2,3,4,5]
输出: [1,3,5,2,4]
```

**数据范围**

- `n == ` 链表中的节点数
- `0 <= n <= 10^4`
- `-10^6 <= Node.val <= 10^6`

**思路**

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
    public ListNode oddEvenList(ListNode head) {
        if(head == null || head.next == null || head.next.next == null) return head;
        ListNode hodd = head, heven = head.next;
        ListNode t = head.next.next, p = hodd, q = heven;
        
        for(int i = 3; t != null; i ++) {
            if(i % 2 == 1) {
                p.next = t;
                p = p.next;
            } else {
                q.next = t;
                q = q.next;
            }
            t = t.next;
        }
        p.next = heven;
        q.next = null;
        return hodd;
    }
}
```

#### （10）[237. 删除链表中的节点](https://leetcode.cn/problems/delete-node-in-a-linked-list/):star2:

**问题**

有一个单链表的 `head`，我们想删除它其中的一个节点 `node`。

给你一个需要删除的节点 `node` 。**你将 无法访问 第一个节点 `head`。**

链表的所有值都是 **唯一的**，并且保证给定的节点 `node` 不是链表中的最后一个节点。

删除给定的节点。注意，删除节点并不是指从内存中删除它。这里的意思是：

- 给定节点的值不应该存在于链表中。
- 链表中的节点数应该减少 1。
- `node` 前面的所有值顺序相同。
- `node` 后面的所有值顺序相同。

**自定义测试：**

- 对于输入，你应该提供整个链表 `head` 和要给出的节点 `node`。`node` 不应该是链表的最后一个节点，而应该是链表中的一个实际节点。
- 我们将构建链表，并将节点传递给你的函数。
- 输出将是调用你函数后的整个链表。

例子

<img src="https://assets.leetcode.com/uploads/2020/09/01/node1.jpg" alt="img" style="zoom: 50%;" />

```
输入：head = [4,5,1,9], node = 5
输出：[4,1,9]
解释：指定链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9
```

**数据范围**

- 链表中节点的数目范围是 `[2, 1000]`
- `-1000 <= Node.val <= 1000`
- 链表中每个节点的值都是 **唯一** 的
- 需要删除的节点 `node` 是 **链表中的节点** ，且 **不是末尾节点**

**思路**

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
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

#### （11）[147. 对链表进行插入排序](https://leetcode.cn/problems/insertion-sort-list/)

**问题**

给定单个链表的头 `head` ，使用 **插入排序** 对链表进行排序，并返回 *排序后链表的头* 。

**插入排序** 算法的步骤:

1. 插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
2. 每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
3. 重复直到所有输入数据插入完为止。

下面是插入排序算法的一个图形示例。部分排序的列表(黑色)最初只包含列表中的第一个元素。每次迭代时，从输入数据中删除一个元素(红色)，并就地插入已排序的列表中。

对链表进行插入排序。

例子

<img src="https://assets.leetcode.com/uploads/2021/03/04/sort1linked-list.jpg" alt="img" style="zoom:67%;" />

```
输入: head = [-1,5,3,4,0]
输出: [-1,0,3,4,5]
```

**数据范围**

- 列表中的节点数在 `[1, 5000]`范围内
- `-5000 <= Node.val <= 5000`

**思路**

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
    public ListNode insertionSortList(ListNode head) {
        ListNode visualh = new ListNode(-0x3f3f3f3f, null); // 虚拟头结点

        ListNode i = head;
        while(i != null) {
            ListNode j = visualh, t = i.next;
            while(j.next != null && j.next.val < i.val) j = j.next;
            i.next = j.next;
            j.next = i;

            i = t;
        }
        return visualh.next;
    }
}
```

#### （12）[148. 排序链表](https://leetcode.cn/problems/sort-list/)

**问题**

给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 。

例子

<img src="https://assets.leetcode.com/uploads/2020/09/14/sort_list_1.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```

**数据范围**：

- 链表中节点的数目在范围 `[0, 5 * 10^4]` 内
- `-10^5 <= Node.val <= 10^5`

**思路**

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
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) return head; // 区间中只有一个结点，则返回此结点
        ListNode s = head, f = head; // 通过快慢指针找到链表的中点
        while(f.next != null && f.next.next != null) {
            s = s.next; f = f.next.next;
        }
        ListNode mid = s.next; s.next = null;
        ListNode i = sortList(head), j = sortList(mid); // 排序

        ListNode res = new ListNode(), k = res; // 归并
        while(i != null && j != null){
            if(i.val <= j.val) { k = k.next = i; i = i.next; }
            else { k = k.next = j; j = j.next; }
        }

        if(i != null) k.next = i; 
        if(j != null) k.next = j;
        return res.next;
    }
}
```

#### （13）[86. 分隔链表](https://leetcode.cn/problems/partition-list/) 

**问题**

给你一个链表的头节点 `head` 和一个特定值 `x` ，请你对链表进行分隔，使得所有 **小于** `x` 的节点都出现在 **大于或等于** `x` 的节点之前。

你应当 **保留** 两个分区中每个节点的初始相对位置。

例子

<img src="https://assets.leetcode.com/uploads/2021/01/04/partition.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,4,3,2,5,2], x = 3
输出：[1,2,2,4,3,5]
```

**数据范围**

- 链表中节点的数目在范围 `[0, 200]` 内
- `-100 <= Node.val <= 100`
- `-200 <= x <= 200`

**思路**

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
    public ListNode partition(ListNode head, int x) {
        ListNode ph = new ListNode(-0x3f3f3f3f, null); // 指向元素值小于x的节点构成链表
        ListNode qh = new ListNode(-0x3f3f3f3f, null); // 指向元素值大于x的节点构成链表

        ListNode p = ph, q = qh;
        for(ListNode i = head; i != null; i = i.next) {
            if(i.val < x) { // 将i分类到元素值小于x的节点构成链表
                p.next = i;
                p = i;
            } else { // 将i分类到元素值大于或等于x的节点构成链表
                q.next = i;
                q = i;
            }
        }
        p.next = qh.next; // 合并两个链表
        q.next = null; // 清空尾节点的next值
        return ph.next;
    }
}
```

#### （14）[2130. 链表最大孪生和](https://leetcode.cn/problems/maximum-twin-sum-of-a-linked-list/)

**问题**

在一个大小为 `n` 且 `n` 为 **偶数** 的链表中，对于 `0 <= i <= (n / 2) - 1` 的 `i` ，第 `i` 个节点（下标从 **0** 开始）的孪生节点为第 `(n-1-i)` 个节点 。

- 比方说，`n = 4` 那么节点 `0` 是节点 `3` 的孪生节点，节点 `1` 是节点 `2` 的孪生节点。这是长度为 `n = 4` 的链表中所有的孪生节点。

**孪生和** 定义为一个节点和它孪生节点两者值之和。

给你一个长度为偶数的链表的头节点 `head` ，请你返回链表的 **最大孪生和** 。

例子

<img src="https://assets.leetcode.com/uploads/2021/12/03/eg1drawio.png" alt="img" style="zoom:67%;" />

```java
输入：head = [5,4,2,1]
输出：6
解释：
节点 0 和节点 1 分别是节点 3 和 2 的孪生节点。孪生和都为 6 。
链表中没有其他孪生节点。
所以，链表的最大孪生和是 6 。
```

**数据范围**

- 链表的节点数目是 `[2, 10^5]` 中的 **偶数** 。
- `1 <= Node.val <= 10^5`

**思路**

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
    public int pairSum(ListNode head) {
        int cnt = 0;
        for(ListNode i = head; i != null; i = i.next) cnt ++;
        
        ListNode p = null, q = head;
        for(int i = 0; i < cnt / 2; i ++) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }

        int res = -0x3f3f3f3f;
        while(p != null && q != null) {
            res = Math.max(res, p.val + q.val);
            p = p.next;
            q = q.next;
        }
        return res;
    }
}
```

#### （15）[2487. 从链表中移除节点](https://leetcode.cn/problems/remove-nodes-from-linked-list/)（单减栈）

**问题**

给你一个链表的头节点 `head` 。

对于列表中的每个节点 `node` ，如果其右侧存在一个具有 **严格更大** 值的节点，则移除 `node` 。

返回修改后链表的头节点 `head` 。

例子

<img src="https://assets.leetcode.com/uploads/2022/10/02/drawio.png" alt="img" style="zoom: 67%;" />



```
输入：head = [5,2,13,3,8]
输出：[13,8]
解释：需要移除的节点是 5 ，2 和 3 。
- 节点 13 在节点 5 右侧。
- 节点 13 在节点 2 右侧。
- 节点 8 在节点 3 右侧。
```

**数据范围**

- 给定列表中的节点数目在范围 `[1, 10^5]` 内
- `1 <= Node.val <= 10^5`

**思路**

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
    // 单减栈
    public ListNode removeNodes(ListNode head) {
        ArrayDeque<ListNode> stk = new ArrayDeque<>();

        ListNode vh = new ListNode(0x3f3f3f3f, null);
        stk.push(vh);
        
        for(ListNode i = head; i != null; i = i.next) {
            while(stk.peek().val < i.val) stk.pop();
            stk.peek().next = i;
            stk.push(i);
        }
        return vh.next;
    }
}

// 思路：
// 
```

#### （16）[1019. 链表中的下一个更大节点](https://leetcode.cn/problems/next-greater-node-in-linked-list/)（翻转链表+单减栈）

**问题**

给定一个长度为 `n` 的链表 `head`。

对于列表中的每个节点，查找下一个 **更大节点** 的值。也就是说，对于每个节点，找到它旁边的第一个节点的值，这个节点的值 **严格大于** 它的值。

返回一个整数数组 `answer` ，其中 `answer[i]` 是第 `i` 个节点( **从1开始** )的下一个更大的节点的值。如果第 `i` 个节点没有下一个更大的节点，设置 `answer[i] = 0` 。

例子

<img src="https://assets.leetcode.com/uploads/2021/08/05/linkedlistnext2.jpg" alt="img" style="zoom:80%;" />

```
输入：head = [2,7,4,3,5]
输出：[7,0,5,5,0]
```

**数据范围**

- 链表中节点数为 `n`
- `1 <= n <= 10^4`
- `1 <= Node.val <= 10^9`

**思路**

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
    public int[] nextLargerNodes(ListNode head) {
        ListNode p = null , q = head;
        int cnt = 0;
        while(q != null) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
            cnt ++;
        }

        ArrayDeque<ListNode> stk = new ArrayDeque<>();
        ListNode vh = new ListNode(0x3f3f3f3f, null);
        stk.push(vh);

        int idx = cnt - 1; 
        int[] res = new int[cnt];
        for(ListNode i = p; i != null; i = i.next) {
            while(stk.peek().val <= i.val) stk.pop();
            if(stk.peek().val != 0x3f3f3f3f) res[idx --] = stk.peek().val;
            else res[idx --] = 0;
            stk.push(i);
        }
        return res;
    }
}
// 翻转
// 单减栈
```

#### （17）[2058. 找出临界点之间的最小和最大距离](https://leetcode.cn/problems/find-the-minimum-and-maximum-number-of-nodes-between-critical-points/)

**问题**

每行的元素从左到右升序排列，每列的元素从上到下升序排列

如果当前节点的值 **严格大于** 前一个节点和后一个节点，那么这个节点就是一个 **局部极大值点** 。

如果当前节点的值 **严格小于** 前一个节点和后一个节点，那么这个节点就是一个 **局部极小值点** 。

注意：节点只有在同时存在前一个节点和后一个节点的情况下，才能成为一个 **局部极大值点 / 极小值点** 。

给你一个链表 `head` ，返回一个长度为 2 的数组 `[minDistance, maxDistance]` ，其中`minDistance` 是任意两个不同临界点之间的最小距离，`maxDistance` 是任意两个不同临界点之间的最大距离。如果临界点少于两个，则返回 `[-1，-1]` 。

例子

![img](https://assets.leetcode.com/uploads/2021/10/13/a2.png)

```
输入：head = [5,3,1,2,5,1,2]
输出：[1,3]
解释：存在三个临界点：
- [5,3,1,2,5,1,2]：第三个节点是一个局部极小值点，因为 1 比 3 和 2 小。
- [5,3,1,2,5,1,2]：第五个节点是一个局部极大值点，因为 5 比 2 和 1 大。
- [5,3,1,2,5,1,2]：第六个节点是一个局部极小值点，因为 1 比 5 和 2 小。
第五个节点和第六个节点之间距离最小。minDistance = 6 - 5 = 1 。
第三个节点和第六个节点之间距离最大。maxDistance = 6 - 3 = 3 。
```

**数据范围**

- 链表中节点的数量在范围 `[2, 10^5]` 内
- `1 <= Node.val <= 10^5`

**思路**

设指针 $q$ 指向了当前节点，$p$ 指向了$q$ 的前继节点，遍历链表。$idx$从$1$ 开始，记录节点编号，$lastid$ 记录上一个临界点的编号。 $maxv$  是临界点之间的最大距离, $minv$ 是临界点之间的最小距离

-  当前节点 $q$ 满足 $q.val > p.val \ \&\& \  q.val > q.next.val$ 时，$q$ 是局部极大值，更新$maxv = maxv + idx - lastid$。
- 当前节点$q$ 满足 $q.val < p.val \ \& \& \ q.val < q.next.val$ 时，$q$ 是局部极小值，更新 $ minv = Math.min(minv, idx - lastid)$。

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
    public int[] nodesBetweenCriticalPoints(ListNode head) {
        int maxv = 0, minv = 0x3f3f3f3f; // maxv 是临界点之间的最大距离, minv 是临界点之间的最小距离
        int cnt = 0, idx = 1, lastid = -1; // cnt是临界点的个数，idx是链表节点编号从1开始, lastid是上一个临界点编号初始-1
        ListNode p = null, q = head; // p是q的前继节点，q是当前节点
        while(q != null) { // 遍历链表
            if(p != null && q.next != null) {
                if((q.val > p.val && q.val > q.next.val) || (q.val < p.val && q.val < q.next.val)) { // 局部极值点
                    if(lastid != -1) maxv = maxv + idx - lastid;
                    if(lastid != -1) minv = Math.min(minv, idx - lastid);
                    lastid = idx;
                    cnt ++ ;
                } 
            }

            p = q;
            q = q.next;
            idx ++;
        }
        if(cnt < 2) return new int[]{-1, -1};
        return new int[]{minv, maxv};
    }
}
```

#### （18）[2074. 反转偶数长度组的节点](https://leetcode.cn/problems/reverse-nodes-in-even-length-groups/)

**问题**

给你一个链表的头节点 `head` 。

链表中的节点 **按顺序** 划分成若干 **非空** 组，这些非空组的长度构成一个自然数序列（`1, 2, 3, 4, ...`）。一个组的 **长度** 就是组中分配到的节点数目。换句话说：

- 节点 `1` 分配给第一组
- 节点 `2` 和 `3` 分配给第二组
- 节点 `4`、`5` 和 `6` 分配给第三组，以此类推

注意，最后一组的长度可能小于或者等于 `1 + 倒数第二组的长度` 。

**反转** 每个 **偶数** 长度组中的节点，并返回修改后链表的头节点 `head` 。

例子

![img](https://assets.leetcode.com/uploads/2021/10/25/eg1.png)

```
输入：head = [5,2,6,3,9,1,7,3,8,4]
输出：[5,6,2,3,9,1,4,8,3,7]
解释：
- 第一组长度为 1 ，奇数，没有发生反转。
- 第二组长度为 2 ，偶数，节点反转。
- 第三组长度为 3 ，奇数，没有发生反转。
- 最后一组长度为 4 ，偶数，节点反转。
```

**数据范围**

- 链表中节点数目范围是 `[1, 10^5]`
- `0 <= Node.val <= 10^5`

**思路**

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
    
    public ListNode reverse(ListNode h, int k) {
        if(h == null) return null; // 递归结束

        ListNode l = null, r = h; // r表示下一组的起始节点, l表示本组的尾节点
        int cnt = 0; // 数组实际长度
        for(int i = 0; r != null && i < k; i ++) {
            l = r;
            r = r.next;
            cnt ++;
        }

        r = reverse(r, k + 1); // 递归翻转r节点之后的链表
        if(cnt % 2 == 1) { // 本组是奇数长度，不用翻转
            l.next = r;
            return h;
        }
        // 本组是偶数长度，不用翻转
        ListNode p = null, q = h;
        while((cnt --) != 0) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }
        h.next = r;
        return p;
    }

    public ListNode reverseEvenLengthGroups(ListNode head) {
        return reverse(head, 1);
    }
}
```

#### （19）[2095. 删除链表的中间节点](https://leetcode.cn/problems/delete-the-middle-node-of-a-linked-list/)

**问题**

给你一个链表的头节点 `head` 。**删除** 链表的 **中间节点** ，并返回修改后的链表的头节点 `head` 。

长度为 `n` 链表的中间节点是从头数起第 `⌊n / 2⌋` 个节点（下标从 **0** 开始），其中 `⌊x⌋` 表示小于或等于 `x` 的最大整数。

- 对于 `n` = `1`、`2`、`3`、`4` 和 `5` 的情况，中间节点的下标分别是 `0`、`1`、`1`、`2` 和 `2` 。

 例子

<img src="https://assets.leetcode.com/uploads/2021/11/16/eg1drawio.png" alt="img" style="zoom:80%;" />

```
输入：head = [1,3,4,7,1,2,6]
输出：[1,3,4,1,2,6]
解释：
上图表示给出的链表。节点的下标分别标注在每个节点的下方。
由于 n = 7 ，值为 7 的节点 3 是中间节点，用红色标注。
返回结果为移除节点后的新链表。 
```

**数据范围**

- 链表中节点的数目在范围 `[1, 105]` 内
- `1 <= Node.val <= 105`

**思路**

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
    public ListNode deleteMiddle(ListNode head) {
        if(head.next == null) return null; // 特判

        ListNode s = head, f = head, p = null; // s是慢指针，f是快指针, p是s的前继结点
        while(f != null && f.next != null) { // s每次走一步, f每次走两步
            p = s;
            s = s.next;
            f = f.next.next;
        }
        p.next = s.next; // s指向中间节点，删除中间节点
        return head;
    }
}
```

#### （20）[143. 重排链表](https://leetcode.cn/problems/reorder-list/) 

**问题**

给定一个单链表 `L` 的头节点 `head` ，单链表 `L` 表示为：

```
L0 → L1 → … → Ln - 1 → Ln
```

请将其重新排列后变为：

```
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
```

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

<img src="https://pic.leetcode-cn.com/1626420320-YUiulT-image.png" alt="img" style="zoom:50%;" />

```
输入：head = [1,2,3,4,5]
输出：[1,5,2,4,3]
```

**数据范围**

- 链表的长度范围为 `[1, 5 * 10^4]`
- `1 <= node.val <= 1000`

**思路**

- 找链表中点
- 依据链表中点将链表分成前后两段，后半段链表翻转
- 错位合并两段链表

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
    public void reorderList(ListNode head) {
        if(head == null || head.next == null || head.next.next == null) return;
        // 找链表中点
        ListNode s = head, f = head, pre = null;
        while(f != null && f.next != null) {
            pre = s;
            s = s.next;
            f = f.next.next;
        }
        // 依据链表中点将链表分成前后两段，后半段链表翻转
        pre.next = null;
        ListNode l1 = head, l2 = s;
        ListNode p = null, q = l2;
        while(q != null) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }
        l2 = p;
        // 错位合并两段链表
        q = new ListNode();
        while(l1 != null && l2 != null) {
            q.next = l1;
            q = q.next;
            l1 = l1.next;

            q.next = l2;
            q = q.next;
            l2 = l2.next;
        }

        if(l1 != null) q.next = l1;
        if(l2 != null) q.next = l2;
    }
}
```



### 9、字符串

#### （1）521. 最长特殊序列 I:writing_hand:  

**问题**：

给你两个字符串 `a` 和 `b`，请返回 *这两个字符串中 **最长的特殊序列*** 的长度。如果不存在，则返回 `-1` 。

**「最长特殊序列」** 定义如下：该序列为 **某字符串独有的最长子序列（即不能是其他字符串的子序列）** 。

字符串 `s` 的子序列是在从 `s` 中删除任意数量的字符后可以获得的字符串。

- 例如，`"abc"` 是 `"aebdc"` 的子序列，因为删除 "a***e***b***d***c"中斜体加粗的字符可以得到 `"abc"` 。 `"aebdc"` 的子序列还包括 `"aebdc"` 、 `"aeb"` 和 `""` (空字符串)。

**思路**：

```java
class Solution {
    public int findLUSlength(String a, String b) {
        if(a.equals(b)) return -1;
        return Math.max(a.length(), b.length());
    }
}
```

#### （2）522. 最长特殊序列 II:writing_hand: 

**问题**：

给定字符串列表 `strs` ，返回 *它们中 **最长的特殊序列*** 。如果最长特殊序列不存在，返回 `-1` 。

**最长特殊序列** 定义如下：该序列为某字符串 **独有的最长子序列（即不能是其他字符串的子序列）**。

`s` 的 **子序列**可以通过删去字符串 `s` 中的某些字符实现。

- 例如，`"abc"` 是 `"aebdc"` 的子序列，因为您可以删除`"aebdc"`中的下划线字符来得到 `"abc"` 。`"aebdc"`的子序列还包括`"aebdc"`、 `"aeb"` 和 "" (空字符串)。

**思路**：**排序**和**双指针**

- **最长特殊子序列** = 最长子序列(就是其本身)+该序列不能是其他序列的子序列

- 将所有字符串**按长度从大到小排序**
- 用双指针实现判断一个字符串是否是另一个字符串的**子序列**
- 找到**第一个**不是其他字符串的子序列的字串，其长度即为答案
- 否则，答案为`-1`

```java
class Solution {

    public boolean check(String a, String b){ // 判断a是否是b的子序列
        int i, j;
        for(i = 0, j = 0; i < b.length() && j < a.length(); i ++ )
            if(b.charAt(i) == a.charAt(j)) 
                j ++;
        return j == a.length();
    }

    public int findLUSlength(String[] strs) {
        // 最长特殊子序列 = 最长子序列(就是其本身)+该序列不能是其他序列的子序列
        Arrays.sort(strs, (s1, s2) -> {
            if(s1.length() != s2.length()) // 先按长度进行倒序排序
                return s2.length() - s1.length();
            else return s1.compareTo(s2); // 长度相同按字典序进行排序
        });

        for(int i = 0; i < strs.length; i ++){
            if(i != 0 && strs[i].equals(strs[i - 1])) continue; // 此串之前判断过
            boolean isSub = false;
            for(int j = 0; j < strs.length; j ++){
                if(i == j) continue;
                if(check(strs[i], strs[j])){
                    isSub = true;
                    break;
                }
            }
            if(!isSub) return strs[i].length(); // 第一个特殊子序列即为答案
        }
        return -1;
    }
}
```



### 10、栈

#### （1）716. 最大栈

**问题**：

设计一个最大栈数据结构，既支持栈操作，又支持查找栈中最大元素。

实现 `MaxStack` 类：

- `MaxStack()` 初始化栈对象
- `void push(int x)` 将元素 x 压入栈中。
- `int pop()` 移除栈顶元素并返回这个元素。
- `int top()` 返回栈顶元素，无需移除。
- `int peekMax()` 检索并返回栈中最大元素，无需移除。
- `int popMax()` 检索并返回栈中最大元素，并将其移除。如果有多个最大元素，只要移除 **最靠近栈顶** 的那个。

**注意**：调用 `pop`、`top`、`peekMax` 或 `popMax` 时，栈中 **至少存在一个元素**

**思路**：用**两个栈**来实现

- 设置两个栈，一个普通栈，一个最大值栈
- 最大值栈，栈顶元素是当前集合中的最大值

```python
class MaxStack:

    def __init__(self):
        self.stk = [] # 普通的栈
        self.maxStk = [] # 栈顶元素是当前集合中的最大值

    def push(self, x: int) -> None:
        self.stk.append(x)
        if not self.maxStk:
            self.maxStk.append(x)
        else:
            self.maxStk.append(max(self.maxStk[-1], x))

    def pop(self) -> int:
        self.maxStk.pop()
        return self.stk.pop()

    def top(self) -> int:
        return self.stk[-1]

    def peekMax(self) -> int:
        return self.maxStk[-1]

    def popMax(self) -> int:
        temp = []
        x = self.maxStk[-1] # 当前栈中的最大元素
        while x != self.stk[-1]: # 其他元素出栈，直到栈顶为最大元素位置
            temp.append(self.pop())
        self.pop() # 删除最大元素
        
        while temp: # 将临时出栈的元素依次入栈
            self.push(temp.pop())
        return x
```

### 11、进制转换

#### （1）[1073. 负二进制数相加](https://leetcode.cn/problems/adding-two-negabinary-numbers/)

**问题**：

给出基数为 **-2** 的两个数 `arr1` 和 `arr2`，返回两数相加的结果。

数字以 *数组形式* 给出：数组由若干 0 和 1 组成，按最高有效位到最低有效位的顺序排列。例如，`arr = [1,1,0,1]` 表示数字 `(-2)^3 + (-2)^2 + (-2)^0 = -3`。*数组形式* 中的数字 `arr` 也同样不含前导零：即 `arr == [0]` 或 `arr[0] == 1`。

返回相同表示形式的 `arr1` 和 `arr2` 相加的结果。两数的表示形式为：不含前导零、由若干 0 和 1 组成的数组。

**数据范围**

- `1 <= arr1.length, arr2.length <= 1000`
- `arr1[i]` 和 `arr2[i]` 都是 0 或 1
- `arr1` 和 `arr2` 都没有前导0

例：

```
输入：arr1 = [1,1,1,1,1], arr2 = [1,0,1]
输出：[1,0,0,0,0]
解释：arr1 表示 11，arr2 表示 5，输出表示 16 。
```

**思路**：

二进制转换成十进制，十进制相加，再转换为二进制

```java
class Solution {
    public int[] addNegabinary(int[] arr1, int[] arr2) {
        int a = 0;
        int b = 0;
        // 二进制转十进制
        for(int i = 0; i < arr1.length; i ++ )
           if(arr1[i] == 1) a += Math.pow(-2, arr1.length - 1 - i);

        for(int i = 0; i < arr2.length; i ++ )
           if(arr2[i] == 1) b += Math.pow(-2, arr2.length - 1 - i);

        int sum = a + b; 
        
        if(sum == 0) return new int[]{0};
        // 十进制转二进制
        ArrayList<Integer> res = new ArrayList<>();
        while(sum != 0) {
            int t = 0;
            if(sum < 0) {
                t = (sum - 1) / (-2);
            } else {
                t = sum / (-2);
            }
            res.add(sum - (-2 * t));
            sum = t;
        }
        Collections.reverse(res);
        return res.stream().mapToInt(t -> t).toArray();
    }
}
```

考虑数据范围，使用Java的BigInteger类，防止数据溢出：

```java
import java.math.BigInteger;

class Solution {
    
    private BigInteger binConvertToDec(int[] arr) {
        BigInteger x = new BigInteger("0");
        BigInteger base = new BigInteger("-2");
        for(int i = 0; i < arr.length; i ++ ) {
            if(arr[i] == 1) x = x.add(base.pow(arr.length - 1 - i));
        }
        return x;
    }
    
    private int[] decConvertToBin(BigInteger x) {
        BigInteger zero = new BigInteger("0"); 
        BigInteger one = new BigInteger("1");
        BigInteger base = new BigInteger("-2");
        ArrayList<Integer> res = new ArrayList<>();
        while(!x.equals(zero)) {
            BigInteger t = new BigInteger("0");
            if(x.compareTo(zero) < 0) {
                t = x.subtract(one).divide(base);
            } else {
                t = x.divide(base);
            }
            res.add(x.subtract(base.multiply(t)).intValue());
            x = t;
        }
        Collections.reverse(res);
        return res.stream().mapToInt(t -> t).toArray();
    }
    
    public int[] addNegabinary(int[] arr1, int[] arr2) {
        BigInteger a = binConvertToDec(arr1);
        BigInteger b = binConvertToDec(arr2);

        if(a.add(b).equals(new BigInteger("0"))) return new int[]{0};

        return decConvertToBin(a.add(b));
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
