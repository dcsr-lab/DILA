# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分之滑动窗口算法



#### 1、[1156. 单字符重复子串的最大长度](https://leetcode.cn/problems/swap-for-longest-repeated-character-substring/)

**问题**：

如果字符串中的所有字符都相同，那么这个字符串是单字符重复的字符串。

给你一个字符串 `text`，你只能**交换其中两个字符一次**或者什么都不做，然后得到一些单字符重复的子串。返回其中最长的子串的长度。

例子：

```
输入：text = "aaabbaaa"
输出：4
```

**数据范围**：

- `1 <= text.length <= 20000`
- `text` 仅由小写英文字母组成。

**思路**：**滑动窗口**，时间复杂度 $O(n)$

给定一个字符串，你需要选择两个字符进行交换，这个操作**最多进行一次**，要求使得仅包含相同字符的子串尽可能地长。例如：“bbababaaaa”，可以交换第2（下标从0开始）个字符a与第5个字符b，使得包含相同字符的子串最长为6，即“aaaaaa"。

设 $n$ 为字符串 $text$的长度，下标从0开始，现有一段区间 $[i,j)$（不包括 $j$）由相同的字符 $a$ 构成，并且该区间两边不存在相同的字符 $a$，而整个 $text$ 中 $a$ 出现的次数为 $count[a]$，那么当 $count[a]>j-i$，并且 $i > 0$ 或者 $j < n$ 时，可以将其他地方出现的 $a$ 与 $text[i-1]$或者$text[j]$交换，从而得到更长的一段包含 $a$ 的子串。

交换后，交换过来的 $a$ 可能使得两段连续的 $a$ 拼接在一起，我们假设 $[i,j)$ 是前面的一段，当$text[j+1]=a$时，我们在找到后面的一段 $[j+1, k)$，这两段拼接在一起构成更长的子串。

注意，我们需要重新判断是否有多余的 $a$ 交换到中间来，因此将拼接后的长度 $k-i$ 与 $count[a]$ 取最小值来更新答案。

```java
class Solution {
    public int maxRepOpt1(String text) {
        int[] count = new int[26]; // 记录每个字符出现的次数
        for(int i = 0; i < text.length(); i ++ )
            count[text.charAt(i) - 'a'] ++ ;

        int res = Integer.MIN_VALUE;
        for(int i = 0; i < text.length(); ) {
            // 寻找区间[i,j), 字符串[i,j]重复的字符是text.charAt(i)
            int j = i;
            while(j < text.length() && text.charAt(j) == text.charAt(i)) j ++ ;

            res = Math.max(res, j - i); // 更新答案
            // 可以在子串[i,j)的前(i - 1)或后(j), 交换一次字符
            if(count[text.charAt(i) - 'a'] > j - i) { 
                int k = j + 1; // 寻找区间[j + 1,k), 字符串[j + 1,k]重复的字符是text.charAt(i)
                while(k < text.length() && text.charAt(k) == text.charAt(i)) k ++ ;
                res = Math.max(res, Math.min(k - i, count[text.charAt(i) - 'a']));
            }
            i = j; // 更新i
        }
        return res;
    }
}
```

#### 2、[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/) 

**问题**：

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` **所有字符**的**最小**子串。

如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**例子**：

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

数据范围：

- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 105`
- `s` 和 `t` 由英文字母组成
- 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
- 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**思路**：

窗口扩张和收缩的时机

- 窗口扩张，right右移，纳入目标数字，可以看作主旋律，在扩张的间隙去收缩。
  - 如果纳入了一个正好缺失的数组的数字，count就+1
  - 哈希表中对应的数组的出现次数 +1
  - 如果此时count正好等于数组的个数k，则我们找到了一个可行解，此时再扩张没有意义，反而背离“最小窗口”。
  - 此时应该收缩窗口长度，left右移。
- 窗口收缩，舍弃左指针指向的数字，直到条件不再满足，就不收缩了。
  - 如果哈希表中有数组的出现次数因此变0，此时窗口缺失了目标数字，条件被破坏了。
  - 这之前的窗口长度，是当前可行解下的最优解，记录下来。
- 此时要重新扩张窗口，去寻找下一个可行解，再优化，再优中取优

```java
class Solution {
    public String minWindow(String s, String t) {
        // 记录滑动窗口中出现字符串t中的字符的出现的次数，滑动窗口保证每个出现字符串t中的字符至少出现一次
        int[] map = new int['z' - 'A' + 1];
        int[] cnt = new int['z' - 'A' + 1]; // 记录t的每一个不重复的字符

        for(int i = 0; i < t.length(); i ++ ) cnt[t.charAt(i) - 'A'] ++ ;
            
        int start = 0, end = Integer.MAX_VALUE;
        for(int l = 0, r = 0; r < s.length(); r ++ ) { // l 窗口左边界, r 窗口右边界
            int ch = s.charAt(r) - 'A';
            if(cnt[ch] != 0) map[ch] ++ ;
            
            // 左边界更新策略
            while(l <= r && (cnt[s.charAt(l) - 'A'] == 0|| map[s.charAt(l) - 'A'] > cnt[s.charAt(l) - 'A'])) {
                if(cnt[s.charAt(l) - 'A'] != 0) map[s.charAt(l) - 'A'] --;
                l ++ ;
            }

            boolean flag = true;
            for(int i = 0; i < cnt.length; i ++ )
                if(map[i] < cnt[i]) {
                    flag = false;
                    break;
                }

            if(flag && r - l < end - start) { // 更新最小区间
                start = l;
                end = r;
            }
        }

        return end == Integer.MAX_VALUE ? "" : s.substring(start, end + 1);
    }
}
```

#### （3）[632. 最小区间](https://leetcode.cn/problems/smallest-range-covering-elements-from-k-lists/)

**问题**：

你有 `k` 个 **非递减排列** 的整数列表。找到一个 **最小** 区间，使得 `k` 个列表中的每个列表**至少有一个数**包含在其中。

我们定义如果 `b-a < d-c` 或者在 `b-a == d-c` 时 `a < c`，则区间 `[a,b]` 比 `[c,d]` 小。

例子：

```
输入：nums = [[4,10,15,24,26], [0,9,12,20], [5,18,22,30]]
输出：[20,24]
解释： 
列表 1：[4, 10, 15, 24, 26]，24 在区间 [20,24] 中。
列表 2：[0, 9, 12, 20]，20 在区间 [20,24] 中。
列表 3：[5, 18, 22, 30]，22 在区间 [20,24] 中。
```

**数据范围**：

- `nums.length == k`
- `1 <= k <= 3500`
- `1 <= nums[i].length <= 50`
- `-10^5 <= nums[i][j] <= 10^5`
- `nums[i]` 按非递减顺序排列

**思路**：

- 将k个列表合并到一个列表中，并将所有数从小到大进行排序。因为单个整数列表是非递增排序序列，可以考虑多路归并来提高性能。
- 动窗口要保证$k$个列表中每一个列表至少有一个数出现在窗口中，因此窗口内维护的都是合法情况，在所有合法情况中取最小。

```java
class Solution {
    public int[] smallestRange(List<List<Integer>> nums) {
        int n = nums.size();
        List<int[]> arr = new ArrayList<>();
        for(int i = 0; i < nums.size(); i ++ ) 
            for(Integer x : nums.get(i)) arr.add(new int[]{x, i}); // i表示x所在的列表标号
        Collections.sort(arr, (a, b) -> a[0] - b[0]); // 按数组第一位元素的大小从小到大排序

        int left = 0, right = Integer.MAX_VALUE;

        // 滑动窗口要保证k个列表中每一个列表至少有一个数出现在窗口中，因此窗口内维护的都是合法情况，在所有合法情况中取最小
        Map<Integer, Integer> map = new HashMap<>(); // 记录滑动窗口中每个标号出现的次数，滑动窗口保证每个标号至少出现一次
        for(int l = 0, r = 0; r < arr.size(); r ++ ) { // l 窗口左边界, r 窗口右边界
            int[] x = arr.get(r);

            map.put(x[1], map.getOrDefault(x[1], 0) + 1);
            while(l <= r && map.getOrDefault(arr.get(l)[1], 0) > 1) { // 左边界更新策略
                map.put(arr.get(l)[1], map.get(arr.get(l)[1]) - 1);
                l ++ ;
            }

            if(map.size() == n && x[0] - arr.get(l)[0] < right - left) { // 更新最小区间
                left = arr.get(l)[0];
                right = x[0];
            }
        }
        return new int[]{left, right}; 
    }
}
```



#### （4）[30. 串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)



#### （5）[209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

**问题**：

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其和 `≥ target` 的长度最小的 **连续子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

**例子**：

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```

**数据范围**：

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 105`

**思路**：

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int len = Integer.MAX_VALUE, sum = 0;
        for(int l = 0, r = 0; r < nums.length; r ++ ) {
            sum += nums[r]; // 扩张
            while(l <= r && sum - nums[l] >= target) sum -= nums[l ++]; // 收缩
            if(sum >= target) len = Math.min(len, r - l + 1); // 符合条件才更新len
        }
        return len == Integer.MAX_VALUE ? 0 : len;
    }
}
```



#### （6）[567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)

**问题**：

给你两个字符串 `s1` 和 `s2` ，写一个函数来判断 `s2` 是否包含 `s1` 的排列。如果是，返回 `true` ；否则，返回 `false` 。

换句话说，`s1` 的排列之一是 `s2` 的 **子串** 。

例子：

```
输入：s1 = "ab" s2 = "eidbaooo"
输出：true
解释：s2 包含 s1 的排列之一 ("ba").
```

**数据范围**：

- `1 <= s1.length, s2.length <= 104`
- `s1` 和 `s2` 仅包含小写字母

**思路**：

```java
class Solution {

    public boolean check(int[] a, int[] b) { // 当且仅当，两个数组所有元素都一致时，这个两个数组才相等
        for(int i = 0; i < a.length; i ++ ) 
            if(a[i] != b[i]) return false;
        return true;
    }

    public boolean checkInclusion(String s1, String s2) {
        // 滑动窗口定义：滑动窗口的大小始终等于s1的长度(l,r维护)，窗口内的字符都在s1中出现(map和cnt维护)

        int[] map = new int[26]; // 维护滑动窗口中每个元素出现的次数
        int[] cnt = new int[26]; // 维护s1中每个元素出现的次数

        for(int i = 0; i < s1.length(); i ++) cnt[s1.charAt(i) - 'a'] ++ ; // 初始化cnt

        for(int l = 0, r = 0; r < s2.length(); r ++ ) { // l左边界, r右边界
            char ch = s2.charAt(r);

            if(cnt[ch - 'a'] > 0) map[ch - 'a'] ++ ; // 扩张, 如果ch是s1中出现的字符,此字符进入窗口
            else { // 快速收缩, 如果ch不是s1中出现的字符, 则左边界l跳过窗口中的所有元素, l = r + 1, l指向右边界r的下一个值
                l = r + 1;
                Arrays.fill(map, 0); // 重置map
                continue;
            }
            // 当窗口的长度大于目标字符串s1的长度, 且窗口内的字符都在s1中出现, 则搜索使得窗口的长度等于字符串s1的长度
            while(l <= r && r - l + 1 > s1.length()) map[s2.charAt(l ++) - 'a'] -- ; // 正常收缩

            if(r - l + 1 == s1.length() && check(map, cnt)) return true; // 长度相等且字符出现的次数相同时，找到一个解
        }

        return false;
    }
}
```

#### （7）[2747. 统计没有收到请求的服务器数目](https://leetcode.cn/problems/count-zero-request-servers/) 

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

- `1 <= n <= 10^5`
- `1 <= logs.length <= 10^5`
- `1 <= queries.length <= 10^5`
- `logs[i].length == 2`
- **`1 <= logs[i][0] <= n`**
- `1 <= logs[i][1] <= 10^6`
- `1 <= x <= 10^5`
- `x < queries[i] <= 10^6`

**思路**：

此题的特点是所有的查询区间的长度是固定大小$x$。那么一个容易想到的思路是将所有 $logs$ 按时间从小到大排序，将所有查询区间按左/右边界从小到大排序。所有区间按左边界排序后，**不存在区间包含区间的问题**，可以考虑使用滑动窗口求出每个区间收到请求的服务数量。

维护一个滑动窗口，该滑动窗口从左向右滑动，每完成一轮滑动（右边界扩张和左边界收缩），都可以得到一个区间查询的结果。

```java
class Solution {     
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        Arrays.sort(logs, (a, b) -> (a[1] - b[1])); // 所有logs按时间从小到大排序
        
        HashMap<Integer, Integer> map = new HashMap<>(); // map的值表示queries[i]在原数组中的下标
        for(int i = 0; i < queries.length; i ++) map.put(queries[i], i);
        Arrays.sort(queries);

        int[] res = new int[queries.length];
        // servers维护了滑动窗口内每个服务器的出现次数
        int[] servers = new int[100010];
        int cnt = 0; // cnt维护了滑动窗口内不同编号的服务器的数量（没有重复元素）
        for(int l = 0, r = 0, idx = 0; idx < queries.length; idx ++) {// l窗口左边界, r右边界
            // 窗口一共进行queries.length轮滑动, 每次滑动完成一个区间的查询
            while(r < logs.length && logs[r][1] <= queries[idx]) { // 扩张
                if(servers[logs[r][0]] == 0) cnt ++;
                servers[logs[r ++][0]] ++;
            } 
            while(l <= r && l < logs.length && logs[l][1] < queries[idx] - x) { // 收缩
                servers[logs[l][0]] --;
                if(servers[logs[l ++][0]] == 0) cnt --;
            }
            res[map.get(queries[idx]).intValue()] = n - cnt; // 区间查询结果
        }

        return res;
    }
}
```

精简版代码

```java
class Solution {     
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        Arrays.sort(logs, (a, b) -> (a[1] - b[1])); // 所有logs按时间从小到大排序
        
        var ids = new Integer[queries.length]; // ids的值表示queries[i]在原数组中的下标
        for(int i = 0; i < queries.length; i ++) ids[i] = i;
        Arrays.sort(ids, (a, b) -> queries[a] - queries[b]);

        int[] res = new int[queries.length];
        
        int[] servers = new int[100010]; // servers维护了滑动窗口内每个服务器的出现次数
        int l = 0, r = 0; // l窗口左边界, r右边界
        for(var id : ids) { // 窗口一共进行queries.length轮滑动, 每次滑动完成一个区间的查询
            while(r < logs.length && logs[r][1] <= queries[id])  // 扩张
                if(servers[logs[r ++][0]] ++ == 0) n --;
            while(l <= r && l < logs.length && logs[l][1] < queries[id] - x) // 收缩
                if(-- servers[logs[l ++][0]] == 0) n ++;
            res[id] = n; // 区间查询结果
        }
        return res;
    }
}
```

超时代码问题分析：

下面的代码用数组模拟哈希表，本质上来说数组的赋值和查询要比 `HashMap`更快。但是，下面的代码会超时的主要原因是` int[] map = new int[1000010];`，创建数组的速度太慢（`new`长度为100w的数组花费的时间太长）。

```java
class Solution {     
    public int[] countServers(int n, int[][] logs, int x, int[] queries) {
        Arrays.sort(logs, (a, b) -> (a[1] - b[1])); // 所有logs按时间从小到大排序
        
        int[] map = new int[1000010];
        for(int i = 0; i < queries.length; i ++) { // map的值表示queries[i]在原数组中的下标
            queries[i] -= x;
            map[queries[i]] = i;
        }
        Arrays.sort(queries);
      
        int[] res = new int[queries.length];
        int[] servers = new int[100010]; // servers维护了滑动窗口内每个服务器的出现次数
        int cnt = 0; // cnt维护了滑动窗口内不同编号的服务器的数量（没有重复元素）
      
				// 窗口一共进行queries.length轮滑动, 每次滑动完成一个区间的查询
        for(int l = 0, r = 0, idx = 0; idx < queries.length; idx ++) { // l窗口左边界, r右边界
            while(r < logs.length && logs[r][1] <= queries[idx] + x) { // 扩张
                if(servers[logs[r][0]] == 0) cnt ++;
                servers[logs[r ++][0]] ++;
            } 
                
            while(l <= r && l < logs.length && logs[l][1] < queries[idx]) { // 收缩
                servers[logs[l][0]] --;
                if(servers[logs[l ++][0]] == 0) cnt --;
            }
            res[map[queries[idx]]] = n - cnt; // 区间查询结果
        }

        return res;
    }
}
```



#### （8）[LCP 68. 美观的花束](https://leetcode.cn/problems/1GxJYY/)

**问题**：

力扣嘉年华的花店中从左至右摆放了一排鲜花，记录于整型一维矩阵 `flowers` 中每个数字表示该位置所种鲜花的品种编号。你可以选择一段区间的鲜花做成插花，且不能丢弃。在你选择的插花中，如果每一品种的鲜花数量都不超过 `cnt` 朵，那么我们认为这束插花是 「美观的」。

> - 例如：`[5,5,5,6,6]` 中品种为 `5` 的花有 `3` 朵， 品种为 `6` 的花有 `2` 朵，**每一品种** 的数量均不超过 `3`

请返回在这一排鲜花中，共有多少种可选择的区间，使得插花是「美观的」。

**注意：**

- 答案需要以 `1e9 + 7 (1000000007)` 为底取模，如：计算初始结果为：`1000000008`，请返回 `1`

例子：

```
输入：flowers = [1,2,3,2], cnt = 1

输出：8

解释：相同的鲜花不超过 1 朵，共有 8 种花束是美观的；
长度为 1 的区间 [1]、[2]、[3]、[2] 均满足条件，共 4 种可选择区间
长度为 2 的区间 [1,2]、[2,3]、[3,2] 均满足条件，共 3 种可选择区间
长度为 3 的区间 [1,2,3] 满足条件，共 1 种可选择区间。
区间 [2,3,2],[1,2,3,2] 都包含了 2 朵鲜花 2 ，不满足条件。
返回总数 4+3+1 = 8
```

**数据范围**：

- `1 <= flowers.length <= 10^5`
- `1 <= flowers[i] <= 10^5`
- `1 <= cnt <= 10^5`

**思路**：

用一个滑动窗口来表示一个区间$[l,r]$，要求区间$[l,r]$中每种类别的花出现的次数不超过$cnt$。其中，$l$表示滑动窗口的左边界，$r$表示滑动出口的有边界。

我们可以使用`hashmap`来维护滑动窗口中每种类型的花的出现次数。

每次滑动窗口右边界向右扩张一个单位$r+=1$，元素$flowers[r]$加入滑动窗口。

当新加入窗口的元素 $flowers[r]$出现次数超过 $cnt$时，窗口的左边界不断收缩，直到 $flowers[r]$出现次数不超过 $cnt$。

统计当前窗口与上次窗口中不重叠的区间个数。

```java
class Solution {
    public int beautifulBouquet(int[] flowers, int cnt) {
        int n = flowers.length;
        int mod = (int)1e9+7;

        var map = new HashMap<Integer, Integer>(); // 维护窗口内每束花出现的次数
        int res = 0;
        for(int l = 0, r = 0; r < n; r ++ ) { // l 窗口的左边界, r 窗口的右边界
            int lastl = l, lastr = r - 1; // 记录上一次窗口的左右边界
            map.put(flowers[r], map.getOrDefault(flowers[r], 0) + 1); // 扩张            
            while(l <= r && map.get(flowers[r]) > cnt) // 收缩
                map.put(flowers[l], map.get(flowers[l ++]) - 1);
            // a是当前窗口的大小, b是上一次窗口与当前窗口交集的大小
            int a = r - l + 1, b = (l <= lastr ? lastr - l + 1: 0); 
            res = (res + a * (a + 1) / 2 - b * (b + 1) / 2) % mod; // 加上本次与上次窗口中不重叠的区间个数
        }
        return res;
    }
}
```





#### （9）[最小窗口子序列](https://leetcode.cn/problems/minimum-window-subsequence/)



#### （10）485. 最大连续 1 的个数

**问题**：

给定一个二进制数组 `nums` ， 计算其中最大连续 `1` 的个数。

**思路**：

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int sum = 0, n = nums.length; // sum表示窗口中元素的和
        int l, r; // l,r分别表示窗口的左右边界
        for(l = 0, r = 0; r < n; r ++){
            sum += nums[r]; // 窗口没有0时, 窗口扩张

            if(r - l - 0 >= sum){ // 窗口中有1个及1个以上的0时, 保持窗口大小(l++, r ++), 右移窗口
                sum -= nums[l];
                l ++;
            }
        }
        return r - l; // 最后返回l-r的差值
    }
}
```

#### （11）487. 最大连续 1 的个数 II

**问题**：

给定一个二进制数组 `nums` ，如果最多可以翻转一个 `0` ，则返回数组中连续 `1` 的最大个数。

**思路**：

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int sum = 0, n = nums.length; // sum表示窗口中元素的和
        int l, r; // l,r分别表示窗口的左右边界
        for(l = 0, r = 0; r < n; r ++){ 
            sum += nums[r]; // 窗口中不超过一个0时, 窗口扩张

            if(r - l - 1>= sum){ // 窗口中超过2个及2个以上的0时时, 保持窗口大小(l++,r++), 不断右移窗口
                sum -= nums[l];
                l ++;
            }
        }
        return r - l; // 最后返回l-r的差值
    }
}
```

#### （12）1004. 最大连续 1 的个数 III:writing_hand: 

**问题**：

给定一个二进制数组 `nums` 和一个整数 `k` ，如果可以翻转最多`k` 个 `0` ，则返回 *数组中连续 `1` 的最大个数* 。

**思路**：

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int sum = 0, n = nums.length; // sum表示窗口中元素的和
        int l, r; // l,r分别表示窗口的左右边界
        for(l = 0, r = 0; r < n; r ++){
            sum += nums[r]; // 窗口中只有不超过k个0时, 窗口扩张
            
            if(r - l - k >= sum){ // 窗口中超过k+1个及k+1个以上的0时时, 保持窗口大小(l++,r++), 不断右移窗口
                sum -= nums[l];
                l ++;
            }
        }
        return r - l; // 最后返回l-r的差值
    }
}
```



#### （13）[6900. 统计完全子数组的数目](https://leetcode.cn/problems/count-complete-subarrays-in-an-array/)

**问题**

给你一个由 **正** 整数组成的数组 `nums` 。

如果数组中的某个子数组满足下述条件，则称之为 **完全子数组** ：

- 子数组中 **不同** 元素的数目等于整个数组不同元素的数目。

返回数组中 **完全子数组** 的数目。

**子数组** 是数组中的一个连续非空序列。

例子

```
输入：nums = [1,3,1,2,2]
输出：4
解释：完全子数组有：[1,3,1,2]、[1,3,1,2,2]、[3,1,2] 和 [3,1,2,2] 。
```

**数据范围**

- `1 <= nums.length <= 1000`
- `1 <= nums[i] <= 2000`

**思路**

设指针 $l$ 和 $r$ 分别指向窗口的左右边界，$set$ 集合维护了滑动窗口的内容。

每次向右扩展一步窗口的右边界 $r$，将 $nums[r]$添加到集合 $set$ 中

始终保持窗口内的不同元素的数目等于整个数组不同元素的数目。在此条件下，收缩窗口的左边界，包含窗口内元素的完全子数组个数为 $(n - 1 - r + 1)$。例如 $nums = [1,3,1,2,2]$，此时滑动窗口为 $window=[3,1,2]$，那么包含窗口内元素的完全子数组为$\{[3,1,2],[3,1,2,2]\}$。

```java
class Solution {
    public int countCompleteSubarrays(int[] nums) {
        int n = nums.length;
        
        Set<Integer> set = new HashSet<>();
        for(int i = 0; i < n; i ++) set.add(nums[i]);
        int cnt = set.size(); // 数组nums中不同元素的个数
        
        set.clear();
        int[] st = new int[2010]; // st[i]是元素nums[i]出现的次数
        int res = 0;
        for(int l = 0, r = 0; r < n; r ++) { // l, r分别表示窗口的左右边界
            set.add(nums[r]); // 扩展窗口的右边界
            st[nums[r]] ++; // 计数
            while(set.size() == cnt && l <= r) { // 始终保持窗口的不同元素的数目等于整个数组不同元素的数目
                res += (n - 1 - r + 1); // 统计完全子数组的个数
                st[nums[l]] --; // 收缩
                if(st[nums[l]] == 0) set.remove(nums[l]); // 更新st
                l ++;
            }
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
