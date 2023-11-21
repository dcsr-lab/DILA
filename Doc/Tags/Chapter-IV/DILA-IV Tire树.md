# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第四部分之Tire树





#### 1、14. 最长公共前缀

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
