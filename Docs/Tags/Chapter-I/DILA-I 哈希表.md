# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之哈希表



### 1、哈希表

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
