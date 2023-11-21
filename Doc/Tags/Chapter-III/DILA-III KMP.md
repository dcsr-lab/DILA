# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分之KMP





#### 1、[28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

**问题**：

给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。

如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。

例子

```
输入：haystack = "sadbutsad", needle = "sad"
输出：0
解释："sad" 在下标 0 和 6 处匹配。
第一个匹配项的下标是 0 ，所以返回 0 。
```

**数据范围**：

- `1 <= haystack.length, needle.length <= 104`
- `haystack` 和 `needle` 仅由小写英文字符组成

**思路**：模板题

> $KMP$
>
> ​		在 $KMP$ 算法中，有一个很重要的数组，叫做 $Next$ 数组（也有地方叫 $Fail$ 数组或者前缀函数）。$Next$ 数组巧妙地利用了模板串 $P$ 中**以某个位置结尾的后缀的与 $P$ 的前缀匹配的信息**，来加速 $P$ 在 $S$ 中的匹配过程。
>
> $Next$ 数组的含义
>
> ​		$Next[i]$ 表示 $P$ 中**以 $i$ 为终点的后缀**与**从 $1$ 开始前缀相匹配**，且**匹配长度最大是多少**, 即：
> $$
> \operatorname{Next}[i]=\max _{1 \leq j \leq i-1}\{j\} \text { while } \mathrm{P}[1: j]==\mathrm{P}[i-j+1: i]
> $$

$p$ 和 $s$ 数组**必须**都是从下标 $1$ 开始。 

下面是预处理 $next$数组的过程，其与kmp匹配过程一致：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-07-14 23.37.00.png" alt="截屏2023-07-14 23.37.00" style="zoom: 33%;" />



其中，$i-1$ 和 $i$ 是原字符串的下标，$j$ 和 $j + 1$是模板字符串的下标。其中 $p[i-1]==p[j]$及其之前的字符串都是已经匹配好的。当前正在尝试匹配的位置是 $i$ 和 $j + 1$，位置 $i$ 永远是与 $j + 1$去匹配。

下面是kmp匹配过程：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-07-14 23.41.17.png" alt="截屏2023-07-14 23.41.17" style="zoom: 33%;" />

其中，$i-1$ 和 $i$ 是原字符串的下标，$j$ 和 $j + 1$是模板字符串的下标。其中 $s[i-1]==p[j]$及其之前的字符串都是已经匹配好的。当前正在尝试匹配的位置是 $i$ 和 $j + 1$，位置 $s[i]$ 永远是与 $p[j + 1]$去匹配。

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int m = haystack.length(), n = needle.length();
        char[] s = new char[m + 1], p = new char[n + 1];

        for(int i = 1; i <= Math.max(m, n); i ++) { // p 和 s都是下标从1开始
            if(i <= m) s[i] = haystack.charAt(i - 1);
            if(i <= n) p[i] = needle.charAt(i - 1);
        }
        
        int[] ne = new int[n + 1];

        for(int i = 2, j = 0; i <= n; i ++) { // ne[1] = 0, 如果第一个字母匹配失败了，就从头开始匹配
            while(j != 0 && p[i] != p[j + 1]) j = ne[j]; // 匹配失败则回退
            if(p[i] == p[j + 1]) j ++; // 尝试匹配
            ne[i] = j; // 更新ne
        }

        for(int i = 1, j = 0; i <= m; i ++) {
            while(j != 0 && s[i] != p[j + 1]) j = ne[j]; // 匹配失败则回退
            if(s[i] == p[j + 1]) j ++; // 尝试匹配
            if(j == n) { // 匹配成功
                return i - (n - 1) - 1;
            }
        }
        return -1;
    }
}
```





#### 2、[214. 最短回文串](https://leetcode.cn/problems/shortest-palindrome/)



#### 3、[336. 回文对](https://leetcode.cn/problems/palindrome-pairs/)



#### 4、[686. 重复叠加字符串匹配](https://leetcode.cn/problems/repeated-string-match/)

**问题**：

给定两个字符串 `a` 和 `b`，**寻找重复叠加字符串 `a` 的最小次数**，使得字符串 `b` 成为叠加后的字符串 `a` 的子串，如果不存在则返回 `-1`。

**注意：**字符串 `"abc"` 重复叠加 0 次是 `""`，重复叠加 1 次是 `"abc"`，重复叠加 2 次是 `"abcabc"`。

例子

```
输入：a = "abcd", b = "cdabcdab"
输出：3
解释：a 重复叠加三遍后为 "abcdabcdabcd", 此时 b 是其子串。
```

**数据范围**：

- `1 <= a.length <= 10^4`
- `1 <= b.length <= 10^4`
- `a` 和 `b` 由小写英文字母组成

**思路**：模板题

尝试不断的叠加字符串 $a$，每叠加一次都将叠加后的字符串与模板串 $b$ 进行KMP子串匹配。

此题的关键是寻找叠加次数的上界 $up$，一个比较容易想到的上界是 $up=floor((2*m+n)/m)$，其中 $m$ 是原字符串的长度，$n$ 是模板串的长度。

设 `a = "abcd"`,` b = "cdabcdab"`，最大叠加次数是 $4$，叠加后的字符串是 $abcd|abcd|abcd|abcd$。

如果在最大叠加次数，两字符串依然无法匹配，则返回 -1。

```java
class Solution {

    public boolean kmp(char[] s, char[] p, int m, int n) {
        int[] ne = new int[n + 1];
        for(int i = 2, j = 0; i <= n; i ++ ) {
            while(j != 0 && p[i] != p[j + 1]) j = ne[j];
            if(p[i] == p[j + 1]) j ++;
            ne[i] = j;
        }
        for(int i = 1, j = 0; i <= m; i ++) {
            while(j != 0 && s[i] != p[j + 1]) j = ne[j];
            if(s[i] == p[j + 1]) j ++;
            if(j == n) return true;
        }
        return false;
    }

    public int repeatedStringMatch(String a, String b) {
        int m = a.length(), n = b.length();
        int cnt = n > m ? (int)Math.floor((double)(n / m)) + 2 : 2; // cnt计算是重点
        char[] s = new char[m * cnt+ 1];
        char[] p = new char[n + 1];

        for(int i = 1; i <= n; i ++) p[i] = b.charAt(i - 1);

        int idx = 1;
        for(int i = 1; i <= cnt; i ++) {
            for(int j = 0; j < m; j ++ ) s[idx ++] = a.charAt(j);
            if(kmp(s, p, idx - 1, n)) return i;
        }
        return -1;
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
