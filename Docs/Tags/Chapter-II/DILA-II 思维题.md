# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之思维题

### 11、思维题

#### （1）[2481. 分割圆的最少切割次数](https://leetcode.cn/problems/minimum-cuts-to-divide-a-circle/)

**问题**：

圆内一个 **有效切割** ，符合以下二者之一：

- 该切割是两个端点在圆上的线段，且该线段经过圆心。
- 该切割是一端在圆心另一端在圆上的线段。

一些有效和无效的切割如下图所示。

<img src="https://assets.leetcode.com/uploads/2022/10/29/alldrawio.png" alt="img" style="zoom:80%;" />

给你一个整数 `n` ，请你返回将圆切割成相等的 `n` 等分的 **最少** 切割次数。

例子：

<img src="https://assets.leetcode.com/uploads/2022/10/24/11drawio.png" alt="img" style="zoom: 60%;" />



```
输入：n = 4
输出：2
解释：
上图展示了切割圆 2 次，得到四等分。
```

<img src="https://assets.leetcode.com/uploads/2022/10/24/22drawio.png" alt="img" style="zoom:60%;" />

```
输入：n = 3
输出：3
解释：
最少需要切割 3 次，将圆切成三等分。
少于 3 次切割无法将圆切成大小相等面积相同的 3 等分。
同时可以观察到，第一次切割无法将圆切割开。
```

**数据范围**：

- `1 <= n <= 100`

**思路**：找规律

n:  1 2 3 4 5 6 7 8

cut:0 1 3 2 5 3 7 4

```java
class Solution {
    public int numberOfCuts(int n) {
        if(n == 1) return 0;
        if((n & 1) == 1) return n;
        else return n / 2;
    }
}
```



#### （2）[1675. 数组的最小偏移量](https://leetcode.cn/problems/minimize-deviation-in-array/) 

**问题**：

给你一个由 `n` 个正整数组成的数组 `nums` 。

你可以对数组的任意元素执行任意次数的两类操作：

- 如果元素是 **偶数** ，**除以**2
  - 例如，如果数组是 `[1,2,3,4]` ，那么你可以对最后一个元素执行此操作，使其变成 `[1,2,3,2]`
- 如果元素是 **奇数** ，**乘上**2
  - 例如，如果数组是 `[1,2,3,4]` ，那么你可以对第一个元素执行此操作，使其变成 `[**2**,2,3,4]`

数组的 **偏移量** 是数组中任意两个元素之间的 **最大差值** 。

返回数组在执行某些操作之后可以拥有的 **最小偏移量** 。

例子：

```
输入：nums = [1,2,3,4]
输出：1
解释：你可以将数组转换为 [1,2,3,2]，然后转换成 [2,2,3,2]，偏移量是 3 - 2 = 1
```

**数据范围**：

- `n == nums.length`
- `2 <= n <= 5 * 104`
- `1 <= nums[i] <= 109`

**思路**：

一经思考，发现要使偏移最小，就要不停的使最小值变大，最大值变小，要么找到最小奇数×2，要么找到最大偶数➗2，但是这样做不仅代码十分复杂，而且超时的离谱。

思考：既然做不到两个任务，是否可以进行恰当的转换，观察题目既有×2又有÷2，是否可以统一来做？发现偶数除以2可能即时奇数又是偶数，而奇数×2 = 偶数，而偶数是可以÷2得到它原来的数的！所以将所有奇数×2并不会影响最终的结果，而将问题转化为全偶问题，问题由此而解。

于是不停循环将最大值除以2，用最大值减去维护的最小值，中止条件是当最大值为奇数时，此时最大值无法缩小，考虑最小值(奇数的话)是否可以扩大，思考发现，一开始全是偶数，如果最小值是奇数，那么它一定是由上一个偶数除以2变过来的，我们在上一个状态已经计算过这个偶数了，因此没必要扩大他，直接返回结果

```java
class Solution {
    public int minimumDeviation(int[] nums) {
        PriorityQueue<Integer> heap = new PriorityQueue<>((a, b)->-a.compareTo(b));
        int minmium = Integer.MAX_VALUE;
        for(int i = 0; i < nums.length; i ++ ) {
            if(nums[i] % 2 == 1) nums[i] *= 2;
            if(nums[i] < minmium ) minmium = nums[i];
            heap.offer(nums[i]);
        }
        
        int res = Integer.MAX_VALUE;
        while(heap.peek() % 2 == 0) {
            res = Math.min(res, heap.peek() - minmium);
            int head = heap.poll();
            head = head / 2;
            if(head < minmium) minmium = head;
            heap.offer(head);
        }
        res = Math.min(res, heap.peek() - minmium);
        return res;
    }
}
```

#### （3）[1375. 二进制字符串前缀一致的次数](https://leetcode.cn/problems/number-of-times-binary-string-is-prefix-aligned/)

**问题**：

给你一个长度为 `n` 、下标从 **1** 开始的二进制字符串，所有位最开始都是 `0` 。我们会按步翻转该二进制字符串的所有位（即，将 `0` 变为 `1`）。

给你一个下标从 **1** 开始的整数数组 `flips` ，其中 `flips[i]` 表示对应下标 `i` 的位将会在第 `i` 步翻转。

二进制字符串 **前缀一致** 需满足：在第 `i` 步之后，在 **闭** 区间 `[1, i]` 内的所有位都是 1 ，而其他位都是 0 。

返回二进制字符串在翻转过程中 **前缀一致** 的次数。

例子：

```
输入：flips = [3,2,4,1,5]
输出：2
解释：二进制字符串最开始是 "00000" 。
执行第 1 步：字符串变为 "00100" ，不属于前缀一致的情况。
执行第 2 步：字符串变为 "01100" ，不属于前缀一致的情况。
执行第 3 步：字符串变为 "01110" ，不属于前缀一致的情况。
执行第 4 步：字符串变为 "11110" ，属于前缀一致的情况。
执行第 5 步：字符串变为 "11111" ，属于前缀一致的情况。
在翻转过程中，前缀一致的次数为 2 ，所以返回 2 。
```

**数据范围**：

- `n == flips.length`
- `1 <= n <= 5 * 10^4`
- `flips` 是范围 `[1, n]` 中所有整数构成的一个排列

**思路**：

翻转最大位置remote应该等于当前的位置i时，才满足在 闭区间 [1, i] 内的所有位都是 1 ，而其他位都是 0。

否则，翻转最大值位置remote大于当前位置i时，意味着当前位置i之前还有比特位没有被翻转

```java
class Solution {
    public int numTimesAllBlue(int[] flips) {
        int cnt  = 0, remote = 0;
        for(int i = 0; i < flips.length; i ++ ) {
            remote = Math.max(remote, flips[i]);
            if(i + 1 == remote) cnt ++;
        }
        return cnt;
    }
}
```

#### （4）899. 有序队列:writing_hand: 

**问题**

给定一个字符串 $s$ 和一个整数 $k$ 。你可以从 $s$ 的前 $k$ 个字母中选择一个，并把它加到字符串的末尾。

返回 在应用上述步骤的任意数量的移动后，字典上最小的字符串 。

**思路** 

首先考虑当 $k = 1$ 的情况：我们只能将字符串第一个元素放到字符串最后，也就是轮转整个字符串，取字典序最小即可；

当 $k = 2$ 时:

- 我们将字符串第一个位置 $s[0]$ 拎出来;
- 剩下的所有位置看作一个轮盘，通过不断将字符串第二个位置 $s[1]$ 放到字符串末尾，即可实现将轮盘转动起来；
- 那么可以在适当的时候将 $s[0]$ 插入轮盘，也可以在适当的时候将 $s[1]$ 拎出来放到位置 $s[0]$ 处，通过这么考虑我们即可实现对 $s$ 字符串的排序。

*即当 $k = 2$ 时的操作，**类似冒泡排序**，不断取前两个数中较小的那个数放到最后，每走一圈都会将一个剩余最大值移到最后，最后可以将整个字符序列从小到大排序*

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202208081725226.jpeg" alt="899.有序队列.jpg" width="600px" />

当 $k > 2$ 时同样可以看作  $k = 2$ 的情况实现对字符串 `s` 的排序

**代码**

```java
class Solution {
    public String orderlyQueue(String s, int k) {
        String res = s;
        if(k == 1) { // 每次将第一个字符移到字符串的末尾，找到字典序最小的字符
            for(int i = 0; i < s.length(); i ++ ) {
                s = s.substring(1, s.length()) +  s.charAt(0);                
                res = s.compareTo(res) < 0 ? s : res;
            }
        } 
        else {
            char[] chs = s.toCharArray(); 
            Arrays.sort(chs); // 所有字符按字典序从小到大排序
            res = new String(chs);
        }
        return res;
    }
}
```



#### （5）[2938. 区分黑球与白球](https://leetcode.cn/problems/separate-black-and-white-balls/)

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
