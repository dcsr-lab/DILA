# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之前缀和



#### 1、2100. 适合打劫银行的日子:writing_hand: （前缀和+后缀和）

**问题**：

你和一群强盗准备打劫银行。给你一个下标从 **0** 开始的整数数组 `security` ，其中 `security[i]` 是第 `i` 天执勤警卫的数量。日子从 `0` 开始编号。同时给你一个整数 `time` 。如果第 `i` 天满足以下所有条件，我们称它为一个适合打劫银行的日子：

- 第 `i` 天前和后都分别至少有 `time` 天
- 第 `i` 天前连续 `time` 天警卫数目都是**非递增的**
- 第 `i` 天后连续 `time` 天警卫数目都是**非递减的**

更正式的，第 `i` 天是一个合适打劫银行的日子当且仅当：`security[i - time] >= security[i - time + 1] >= ... >=security[i] <= ... <= security[i + time - 1] <= security[i + time]`.

请你返回一个数组，包含 **所有** 适合打劫银行的日子（下标从 **0** 开始）。返回的日子可以 **任意** 顺序排列。

**思路**：**前缀和**与**后缀和**，$O(n)$ 

- `down` 数组，前缀和, `down[i]`表示以`i`为终点的连续非递增序列的最大长度
- `up`数组，后缀和, `up[i]` 表示以`i`为起点的连续非递减序列的最大长度 
- 当`i`同时满足`down[i] >= k + 1 && up[i] >= k + 1`条件时，即为一个解

```java
class Solution {
    public List<Integer> goodDaysToRobBank(int[] security, int time) {
        int n = security.length, k = time;
        int[] a = security;

        int[] down = new int[n]; // 前缀和, down[i]表示以i为终点的连续非递增序列的最大长度
        int[] up = new int[n]; // 后缀和, up[i] 表示以i为起点的连续非递减序列的最大长度 

        down[0] = up[n - 1] = 1; // 初始化前缀和数组和后缀和数组
        for(int i = 1; i < n; i ++){
            down[i] = a[i] <= a[i - 1] ? down[i - 1] + 1 : 1; // 用down[i - 1]更新down[i]
            up[n - i - 1] = a[n - i - 1] <= a[n - i] ? up[n - i] + 1 : 1; // 同理
        }

        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < n; i ++)
            if(down[i] >= k + 1 && up[i] >= k + 1) // 找出所有满足条件的 i
                res.add(i);   
        return res;
    }
}
```

#### 2、5203. 统计可以提取的工件:writing_hand: （二维前缀和）

**问题**：

存在一个 `n x n` 大小、下标从 **0** 开始的网格，网格中埋着一些工件。给你一个整数 `n` 和一个下标从 **0** 开始的二维整数数组 `artifacts` ，`artifacts` 描述了矩形工件的位置，其中 `artifacts[i] = [r1i, c1i, r2i, c2i]` 表示第 `i` 个工件在子网格中的填埋情况：

- `(r1i, c1i)` 是第 `i` 个工件 **左上** 单元格的坐标，且
- `(r2i, c2i)` 是第 `i` 个工件 **右下** 单元格的坐标。

你将会挖掘网格中的一些单元格，并清除其中的填埋物。如果单元格中埋着工件的一部分，那么该工件这一部分将会裸露出来。如果一个工件的所有部分都都裸露出来，你就可以提取该工件。

给你一个下标从 **0** 开始的二维整数数组 `dig` ，其中 `dig[i] = [ri, ci]`表示你将会挖掘单元格 `(ri, ci)` ，返回你可以提取的工件数目。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202203140858112.jpeg" alt="img" width="230px" />

**注意**：生成的测试用例满足：

- 不存在重叠的两个工件。
- 每个工件最多只覆盖 `4` 个单元格。
- `dig` 中的元素互不相同。

**思路**：时间复杂度 $O(n^2)$ 

- 先遍历 `dig` 数组给 `n * n` 的矩阵左上标记，不妨标记为 `1`。

- 接下来遍历 `artifacts`，问题转化为求子矩阵 `[r1,c1, r2,c2]` 是否全是 `1` 即可。可以使用二维前缀和加速这个过程。

```java
class Solution {

    int[][] a, s; // a 表示原数组, s 是前缀和数组

    public int digArtifacts(int n, int[][] artifacts, int[][] dig) {
        a = new int[n + 1][n + 1]; // a和s都是从下标从1开始
        s = new int[n + 1][n + 1];

        for(int i = 0; i < dig.length; i ++) { // 构建元素组
            int x = dig[i][0] + 1, y = dig[i][1] + 1;
            a[x][y] = 1;
        }

        for(int i = 1; i <= n; i ++ ) // 求前缀和数组
            for(int j = 1; j <= n; j ++)
                s[i][j] = a[i][j] + s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1];
        
        int res = 0;
        for(int i = 0; i < artifacts.length; i ++ ) {
            int x1 = artifacts[i][0] + 1, y1 = artifacts[i][1] + 1;
            int x2 = artifacts[i][2] + 1, y2 = artifacts[i][3] + 1;

            int sum = s[x2][y2] - s[x2][y1 - 1] -s[x1 - 1][y2] + s[x1 - 1][y1 - 1]; // 求区域和
            if(sum == (x2 - x1 + 1) * (y2 - y1 + 1)) // 可以挖出一个工件
                res ++ ;
        }
        return res;
    }
}
```

#### 3、1413. 逐步求和得到正数的最小值

**问题**

给你一个整数数组 `nums` 。你可以选定任意的 **正数** startValue 作为初始值。

你需要从左到右遍历 `nums` 数组，并将 startValue 依次累加上 `nums` 数组中的值。

请你在确保**累加和**始终大于等于 1 的前提下，选出一个最小的 **正数** 作为 startValue 。

**思路**

该题等价于选择一个最小正整数 `startValue` 与数组 `nums` 凑成新的数组 `[startValue, ...nums]`，并且这个数组的**前缀和**都要大于等于 1。

那么就是 **startValue + nums 的前缀和中最小的 >= 1**。当然还需要注意 nums 的前缀和可能全都大于 0，这种情况结果是 1。

```java
class Solution {
    public int minStartValue(int[] nums) {
        int n = nums.length;
        int[] s = new int[n + 1]; // 前缀和数组

        boolean flag = true; // true 表示nums数组的前缀和都大于等于1
        int res = 0x3f3f3f3f; // 前缀和的最小值
        for(int i = 1; i <= n; i ++ ) {
            s[i] = s[i - 1] + nums[i - 1];
            if(s[i] <= 0) flag = false;
            res = Math.min(res, s[i]);
        }
            
        if(!flag) return Math.abs(res) + 1;
        return 1; // nums 的前缀和可能全都大于 0，这种情况结果是 1
    }
}
```

#### 4、[1093. 大样本统计](https://leetcode.cn/problems/statistics-from-a-large-sample/)（前缀和+二分）

**问题**：

我们对 `0` 到 `255` 之间的整数进行采样，并将结果存储在数组 `count` 中：`count[k]` 就是整数 `k` 在样本中出现的次数。

计算以下统计数据:

- `minimum` ：样本中的最小元素。
- `maximum` ：样品中的最大元素。
- `mean` ：样本的平均值，计算为所有元素的总和除以元素总数。
- `median` ：如果样本的元素个数是奇数，那么一旦样本排序后，中位数 `median` 就是中间的元素。如果样本中有偶数个元素，那么中位数`median` 就是样本排序后中间两个元素的平均值。
- `mode` ：样本中出现次数最多的数字。保众数是 **唯一** 的。

以浮点数数组的形式返回样本的统计信息 `[minimum, maximum, mean, median, mode]` 。与真实答案误差在 `10^-5` 内的答案都可以通过。

例子：

```
输入：count = [0,1,3,4,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
输出：[1.00000,3.00000,2.37500,2.50000,3.00000]
解释：用count表示的样本为[1,2,2,2,3,3,3,3]。
最小值和最大值分别为1和3。
均值是(1+2+2+2+3+3+3+3) / 8 = 19 / 8 = 2.375。
因为样本的大小是偶数，所以中位数是中间两个元素2和3的平均值，也就是2.5。
众数为3，因为它在样本中出现的次数最多。
```

**数据范围**：

- `count.length == 256`
- `0 <= count[i] <= 10^9`
- `1 <= sum(count) <= 10^9`
- `count` 的众数是 **唯一** 的

思路：

在直方图上面求中位数

- 方法1：用双指针
- 方法2：前缀和计数数组，中位数使用二分查找方法

```java
class Solution {

    public int search(int[] s, int l, int r, double x) { // 二分搜索左边界
        while(l < r) {
            int mid = l + r >> 1;
            if(s[mid] >= x) r = mid;
            else l = mid + 1;
        }
        return l;
    }

    public double[] sampleStats(int[] count) {
        int n = count.length;

        int minimum = Integer.MAX_VALUE, maximum = Integer.MIN_VALUE; // 最小值和最大值
        int mean = 0, mode = 0; // 均值和众数

        long sum = 0, cnt = 0;
        for(int i = 0; i < n; i ++ ) {
            cnt += count[i];
            sum += (long)i * count[i]; // 防止乘法溢出
            if(count[i] != 0) {
                minimum = Math.min(minimum, i);
                maximum = Math.max(maximum, i);
            }
            if(count[mode] < count[i]) mode = i;
        }
        // 在直方图上面求中位数
        // 方法1：用双指针
        // 方法2：前缀和计数数组，中位数使用二分查找方法
        int[] s = new int[n + 10]; // 从下标1开始
        for(int i = 1; i <= n; i ++ ) {
            int x = count[i - 1];
            s[i] = s[i - 1] + x;
        }

        int l = search(s, 1, n, Math.ceil(cnt / 2.0));
        
        double median = l - 1;
        if(cnt % 2 == 0) {
            median += search(s, 1, n, Math.ceil(cnt / 2.0) + 1) - 1;
            median = median / 2.0;
        }

        return new double[]{ minimum, maximum, sum / (double)cnt, median, mode };
    }
}
```

#### 5、[2559. 统计范围内的元音字符串数](https://leetcode.cn/problems/count-vowel-strings-in-ranges/) （前缀和）

**问题**：

给你一个下标从 **0** 开始的字符串数组 `words` 以及一个二维整数数组 `queries` 。

每个查询 `queries[i] = [li, ri]` 会要求我们统计在 `words` 中下标在 `li` 到 `ri` 范围内**包含** 这两个值）并且以元音开头和结尾的字符串的数目。

返回一个整数数组，其中数组的第 `i` 个元素对应第 `i` 个查询的答案。

**注意：**元音字母是 `'a'`、`'e'`、`'i'`、`'o'` 和 `'u'` 。

例子：

```
输入：words = ["aba","bcb","ece","aa","e"], queries = [[0,2],[1,4],[1,1]]
输出：[2,3,0]
解释：以元音开头和结尾的字符串是 "aba"、"ece"、"aa" 和 "e" 。
查询 [0,2] 结果为 2（字符串 "aba" 和 "ece"）。
查询 [1,4] 结果为 3（字符串 "ece"、"aa"、"e"）。
查询 [1,1] 结果为 0 。
返回结果 [2,3,0] 。
```

**数据范围**：

- `1 <= words.length <= 10^5`
- `1 <= words[i].length <= 40`
- `words[i]` 仅由小写英文字母组成
- `sum(words[i].length) <= 3 * 10^5`
- `1 <= queries.length <= 10^5`
- `0 <= queries[j][0] <= queries[j][1] < words.length`

**思路**：前缀和，时间复杂度为 $O(n)$

```java
class Solution {
    public boolean check(char ch) { // 判断ch是否是元音字母
        if(ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch == 'u') return true;
        return false;
    }

    public int[] vowelStrings(String[] words, int[][] queries) {
        int n = words.length, m = queries.length;
        int[] num = new int[n + 10];

        for(int i = 0; i < n; i ++ ) { // 将字符串数组转换成整数数组
            int len = words[i].length();
            char start = words[i].charAt(0), end = words[i].charAt(len - 1);
            if(check(start) && check(end)) num[i + 1] = 1;
        }

        int[] s = new int[n + 10]; // 计算前缀和数组
        for(int i = 1; i <= n; i ++ )
            s[i] = s[i - 1] + num[i];

        int[] res = new int[m];
        for(int i = 0; i < m ; i ++ ) { // 区间查询
            int l = queries[i][0] + 1, r = queries[i][1] + 1;
            res[i] = s[r] - s[l - 1];
        }

        return res;
    }
}
```

#### 6、[1171. 从链表中删去总和值为零的连续节点](https://leetcode.cn/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)（前缀和+哈希表）

**问题**：

给你一个链表的头节点 `head`，请你编写代码，反复删去链表中由 **总和** 值为 `0` 的连续节点组成的序列，直到不存在这样的序列为止。

删除完毕后，请你返回最终结果链表的头节点。

你可以返回任何满足题目要求的答案。

（注意，下面示例中的所有序列，都是对 `ListNode` 对象序列化的表示。）

例子：

```
输入：head = [1,2,-3,3,1]
输出：[3,1]
提示：答案 [1,2,1] 也是正确的。
```

**数据范围**：

- 给你的链表中可能有 `1` 到 `1000` 个节点。
- 对于链表中的每个节点，节点的值：`-1000 <= node.val <= 1000`.

**思路**：

- 将链表转换成数组（下标从1开始）
- 求数组的前缀和数组（下标从1开始）
- 对于前缀和数组的每一个元素$s[i]$，查找其左边第一个与其相等的元素$s[k]$，则子数组$sum(s[k+1],...,s[i])=0$应该被删除。
- 如何快速查找$s[k]$？用哈希表维护$i$之前每一个前缀和数组元素出现的位置 $(s[k],k)$。
- 当子数组${s[k+1],...,s[i]}$被删除时，哈希表中对应的键值对元素也应该被删除。同时将$(s[i],i)$加入哈希表
- 将所有为0的子数组删除后的数组转换成链表。

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

#### 7、[1177. 构建回文串检测](https://leetcode.cn/problems/can-make-palindrome-from-substring/) （前缀和）

**问题**：

给你一个字符串 `s`，请你对 `s` 的子串进行检测。

每次检测，待检子串都可以表示为 `queries[i] = [left, right, k]`。我们可以 **重新排列** 子串 `s[left], ..., s[right]`，并从中选择 **最多** `k` 项替换成任何小写英文字母。 

如果在上述检测过程中，子串可以变成回文形式的字符串，那么检测结果为 `true`，否则结果为 `false`。

返回答案数组 `answer[]`，其中 `answer[i]` 是第 `i` 个待检子串 `queries[i]` 的检测结果。

注意：在替换时，子串中的每个字母都必须作为 **独立的** 项进行计数，也就是说，如果 `s[left..right] = "aaa"` 且 `k = 2`，我们只能替换其中的两个字母。（另外，任何检测都不会修改原始字符串 `s`，可以认为每次检测都是独立的）

例子：

```
输入：s = "abcda", queries = [[3,3,0],[1,2,0],[0,3,1],[0,3,2],[0,4,1]]
输出：[true,false,false,true,true]
解释：
queries[0] : 子串 = "d"，回文。
queries[1] : 子串 = "bc"，不是回文。
queries[2] : 子串 = "abcd"，只替换 1 个字符是变不成回文串的。
queries[3] : 子串 = "abcd"，可以变成回文的 "abba"。 也可以变成 "baab"，先重新排序变成 "bacd"，然后把 "cd" 替换为 "ab"。
queries[4] : 子串 = "abcda"，可以变成回文的 "abcba"。
```

**数据范围**：

- `1 <= s.length, queries.length <= 10^5`
- `0 <= queries[i][0] <= queries[i][1] < s.length`
- `0 <= queries[i][2] <= s.length`
- `s` 中只有小写英文字母

**思路**：

- 针对 `s` 中的每一个字母统计其前缀和。
- 针对每一个查询$(l,r,k)$，统计$[l,r]$的子串中出现次数为奇数的字母的个数 $odds$。
- 如果 $odds / 2 <= k$ ，那么子串$[l,r]$可以通过重排和替换变成回文串。

```java
class Solution {
    public List<Boolean> canMakePaliQueries(String s, int[][] queries) {
        int n = s.length(), m = queries.length;
        int[][] pre = new int[26][n + 1]; // 针对s中每一个字母计算其前缀和数组

        for(int i = 1; i <= n; i ++ ) {
            int ch = s.charAt(i - 1) - 'a';
            for(int j = 0; j < 26; j ++) 
                pre[j][i] = (j == ch ? pre[ch][i - 1] + 1 : pre[j][i - 1]); // 构造前缀和数组
        }

        List<Boolean> arr = new ArrayList<>();
        for(int i = 0; i < m; i ++) { // m个查询
            int l = queries[i][0] + 1, r = queries[i][1] + 1, k = queries[i][2];
            int odds = 0;
            for(int j = 0; j < 26; j ++ ) { // 统计[l,r]的子串中出现次数为奇数的字母的个数
                int cnt = pre[j][r] - pre[j][l - 1];
                if((cnt & 1) == 1) odds ++;
            }
            if(odds / 2 <= k) arr.add(true); // 子串[l,r]可以通过重排和替换变成回文串
            else arr.add(false);
        }
        return arr;
    }
}
```

#### 8、[238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/) 

**问题**

给你一个整数数组 `nums`，返回 *数组 `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积* 。

题目数据 **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内。

请**不要使用除法，**且在 `O(*n*)` 时间复杂度内完成此题。

例子

```
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

**数据范围**

- `2 <= nums.length <= 10^5`
- `-30 <= nums[i] <= 30`
- **保证** 数组 `nums`之中任意元素的全部前缀元素和后缀的乘积都在 **32 位** 整数范围内

**思路**

**方案一**：时间复杂度$O(n)$，空间复杂度 $O(n)$

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] prefix = new int[n + 1], postfix = new int[n + 1]; // prefix[i-1]和postfix[i+1]分别表示nums[i]的前缀乘积和后缀乘积

        prefix[0] = postfix[n] = 1;
        for(int i = 1, j = n - 1; i <= n && j >= 0; i ++, j -- ) {
            prefix[i] = prefix[i - 1] * nums[i - 1];
            postfix[j] = postfix[j + 1] * nums[j];
        }

        int[] res = new int[n];
        for(int i = 0; i < n; i ++)
            res[i] = prefix[i - 1 + 1] * postfix[i + 1];
            
        return res;
    }
}
```

**方案二**：时间复杂度$O(n)$，空间复杂度 $O(1)$

设置两个变量$prefix$ 和 $postfix$。对于当前枚举的位置 $i$，$prefix$ 表示$nums[0]$~$nums[i-1]$之间所有数的乘积，$postfix$表示$nums[n-1-i]$ ~ $nums[n-1]$所有数的乘积，$res[i]$ 表示除 $nums[i]$数组所有数的乘积。

在当前枚举的位置 $i$，$res[i]$先乘以其前缀$prefix$，$res[n-1-i]$乘以其后缀$postfix$，再更新前缀 $prefix *= nums[i]$，

更新其后缀 $postfix*=nums[n-1-i]$。

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int prefix = 1, postfix = 1;
        int[] res = new int[n];
        Arrays.fill(res, 1);
        for(int i = 0; i < n; i ++) {
            res[i] *= prefix;  // 乘以nums[i]左边的前缀
            prefix *= nums[i]; // 更新前缀
            res[n - 1 - i] *= postfix; // 乘以nums[j]右边的后缀
            postfix *= nums[n - 1 - i]; // 更新后缀
        }
        return res;
    }
}
```

#### 9、[523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)（前缀和+哈希）

**问题**

给你一个整数数组 `nums` 和一个整数 `k` ，编写一个函数来判断该数组是否含有同时满足下述条件的连续子数组：

- 子数组大小 **至少为 2** ，且
- 子数组元素总和为 `k` 的倍数。

如果存在，返回 `true` ；否则，返回 `false` 。

如果存在一个整数 `n` ，令整数 `x` 符合 `x = n * k` ，则称 `x` 是 `k` 的一个倍数。`0` 始终视为 `k` 的一个倍数。

**数据范围**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^9`
- `0 <= sum(nums[i]) <= 2^31 - 1`
- `1 <= k <= 2^31 - 1`

**思路**

设前缀和数组为 `int[] s`。则 子数组 $(i,j]$的和为 $s[j]-s[i]$。

子数组元素 $(i,j]$ 总和为 `k` 的倍数，即 $(s[j]-s[i])\%k=0$ ，等价于 $(s[j]\%k=s[i]\%k)$。

```java
class Solution {

    public int getMod(int a, int b){
        return a % b;
    }

    public boolean checkSubarraySum(int[] nums, int k) {
        if(nums.length < 2) return false;
        int n = nums.length;
        HashMap<Integer, Integer> hash = new HashMap<>();
        int[] s = new int[n + 10];

        hash.put(getMod(s[0], k), 0);
        for(int i = 1; i <= n; i ++){
            s[i] = s[i - 1] + nums[i - 1];

            int mod = getMod(s[i], k);
            if(hash.containsKey(mod)){
                if(i - hash.get(mod) >= 2) return true;
            } else hash.put(mod, i);
        }
        return false;
    }
}
```

#### 10、[525. 连续数组](https://leetcode.cn/problems/contiguous-array/)（前缀和+哈希）

**问题**

给定一个二进制数组 `nums` , 找到含有相同数量的 `0` 和 `1` 的最长连续子数组，并返回该子数组的长度。

**数据范围**

- `1 <= nums.length <= 10^5`
- `nums[i]` 不是 `0` 就是 `1`

**思路**

把 $0$ 视作$-1$，把$1$是作 $1$。

找到含有相同数量的 `0` 和 `1` 的子数组。就等价于：找到一个和为 $0$ 的连续子数组。注意这种子数组的长度一定是偶数，因为$0$和$1$的个数相等。

设前缀和数组为 `int[] s`。则 子数组 $(i,j]$的和为 $s[j]-s[i]$。

找到含有相同数量的 `0` 和 `1` 的连续子数组$(i,j]$，即 $(s[j]-s[i])=0$ ，等价于 $(s[j]=s[i])$。

```java
class Solution {
    public int findMaxLength(int[] nums) {
        int n = nums.length;

        int res = 0;

        int[] s = new int[n + 1];
        Map<Integer, Integer> map = new HashMap<>(); // value是key出现的最早的下标

        map.put(0, 0); // 加入一个哨兵

        for(int i = 1; i <= n; i ++) {
            int x = (nums[i - 1] == 0 ? -1 : 1);
            s[i] = s[i - 1] + x;

            if(map.containsKey(s[i])) {
                int t = map.get(s[i]);
                res = Math.max(res, i - t);
            }
            else map.put(s[i], i);
        }

        return res;
    }
}
```

#### 11、[1590. 使数组和能被 P 整除](https://leetcode.cn/problems/make-sum-divisible-by-p/)





#### 12、[974. 和可被 K 整除的子数组](https://leetcode.cn/problems/subarray-sums-divisible-by-k/)



#### 13、[560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)



#### 14、[930. 和相同的二元子数组](https://leetcode.cn/problems/binary-subarrays-with-sum/)



#### 15、[面试题 17.05. 字母与数字](https://leetcode.cn/problems/find-longest-subarray-lcci/)



#### 16、[930. 和相同的二元子数组](https://leetcode.cn/problems/binary-subarrays-with-sum/)



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
