# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之字符串

### 1、字符串

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
