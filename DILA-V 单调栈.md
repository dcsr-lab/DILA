# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之单调栈



Reference: [一招吃遍力扣四道题，妈妈再也不用担心我被套路啦～]([一招吃遍力扣四道题，妈妈再也不用担心我被套路啦～ - 移掉 K 位数字 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-k-digits/solution/yi-zhao-chi-bian-li-kou-si-dao-ti-ma-ma-zai-ye-b-5/)) 

#### 1、[402. 移掉 K 位数字](https://leetcode.cn/problems/remove-k-digits/)（+贪心）

**问题**：

给你一个以字符串表示的非负整数 `num` 和一个整数 `k` ，移除这个数中的 `k` 位数字，使得剩下的数字最小。请你以字符串形式返回这个最小的数字。

例子：

```
输入：num = "1432219", k = 3
输出："1219"
解释：移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219 。

// 特例
// 112
// 1

// 10001
// 4
```

**数据范围**：

- `1 <= k <= num.length <= 105`
- `num` 仅由若干位数字（0 - 9）组成
- 除了 **0** 本身之外，`num` 不含任何前导零

**思路**：**单增栈+贪心**

如果即将入栈的元素比栈顶的元素大，那么该元素入栈; 如果入栈元素比栈顶元素小，那么替换栈顶元素，因为我们希望得到尽可能小的数。题目要求删除k个元素, 等价于保留 n - k个元素。

- 单增栈存储下标；
- 从左向右扫描序列, 当前要入栈的值为x：
  - 如果栈为空或者x大于等于栈顶元素, x入栈；
  - 否则，栈定元素出栈，直到栈为空或者栈顶元素小于x；
- 保持栈的大小 + 数组剩余为被考虑的元素个数 > n - k(被保留的元素个数)；
- res = 栈内元素 + 数组剩余为被考虑的元素；
- 如果res.length > n - k, 取前k个元素; 否则, res；
- 去除前导0；
- 不能打乱字符的相对顺序，贪心的思想是尽可能保证一个数高位尽可能地小，也就是一个字符串的前面几位尽可能地小。

```java
class Solution {
    public String removeKdigits(String num, int k) {

        if(num.length() == k) return "0";

        int n = num.length();
        int[] stk = new int[n + 10]; // 栈
        int tt = 0; // tt栈顶表示栈顶指针, 下标0表示栈底, 栈从小标1开始存储 
    
        // string to array
        int nums[] = new int[n];
        for (int i = 0; i < n ; i ++ ) nums[i] = num.charAt(i) - '0'; 

        // 单增栈
        int size = 0, remain = n;
        for(int i = 0; i < n; i ++ ) {
            if( size + remain <= n - k) break; // 保证至少有 n - k个元素

            while(tt != 0 && nums[stk[tt]] > nums[i] && size + remain > n - k) {
                tt -- ;
                size -- ;
            }

            stk[ ++ tt ] = i;
            size ++ ;
            remain -- ;
        }

        // 剩余的字符
        StringBuilder builder = new StringBuilder();
        for (int i = 1; i <= tt; i ++ )   builder.append(nums[stk[i]]);
        for (int i = n - remain; i < n; i ++ )  builder.append(nums[i]);

        // 取前 n - k 位
        String res = builder.toString();
        res = res.length() > n - k ? res.substring(0, n - k) : res; 

        // 去除前导零
        while (res.length() != 0 && res.charAt(0) == '0') {
            res = res.substring(1, res.length());
        }

        return res.equals("") ? "0" : res;
    }
}
```

#### 2、[316. 去除重复字母](https://leetcode.cn/problems/remove-duplicate-letters/)（+贪心）

**问题**：

给你一个字符串 `s` ，请你去除字符串中重复的字母，使得每个字母只出现一次。需保证 **返回结果的字典序最小**（要求不能打乱其他字符的相对位置）。

例子：

```
输入：s = "bcabc"
输出："abc"

// 特例
// "abacb"
// abc

// "adacd"
// acd
```

**数据范围**：

- `1 <= s.length <= 104`
- `s` 由小写英文字母组成

 **思路**：**单增栈 + 贪心**

如果要去除重复的字母，首先我们得知道哪些字母是重复的，因此需要一个hashmap计数。如要要使得返回结果字典序最小，在保证相对顺序不变的前提下，字典序小的字母尽可能地排在前面。

hashmap对字母出现的频率计数。

 单增栈：

- 单调栈中的每一个元素都不重复；
- **ch在栈中已经出现过了, ch不入栈**, 跳过ch, 计算器-1；
- 栈空,字符ch直接入栈；
- 字符ch的字典序比栈顶字符的字典序大, ch在栈中没有出现过, ch入栈；
- 字符ch的字典序比栈顶字符的字典序小:
  - 如果栈顶字符计数器不为1, 则栈顶字符出栈, 计算器减1, ch入栈；
  - 如果栈顶字符计数器为1, ch入栈；



```java
class Solution {
    public String removeDuplicateLetters(String s) {
        int n = s.length();
        // 计数
        HashMap<Character, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i ++ ) {
            char ch = s.charAt(i);
            map.put(ch, map.getOrDefault(ch, 0) + 1);
        }
        
        // 判重数组
        boolean[] st = new boolean[26];

        // 栈
        int[] stk = new int[n + 10];
        int tt = 0;

        // 单增栈
        for(int i = 0; i < n; i ++ ) {
            // ch在栈中已经出现过了, ch不入栈, 跳过ch, 计算器-1。
            // 如果ch比栈顶元素小, 且栈顶元素在ch之后还出现过
            if(st[s.charAt(i) - 'a']) { // 这步难点
                map.put(s.charAt(i), map.get(s.charAt(i)) - 1);
                continue;
            }

            while(tt != 0 && s.charAt(stk[tt]) - s.charAt(i) >= 0) {
                char ch = s.charAt(stk[tt]);
                if(map.get(ch) == 1) break;

                map.put(ch, map.get(ch) - 1);
                tt --;
                st[ch - 'a'] = false;
            }

            stk[++ tt] = i;
            st[s.charAt(i) - 'a'] = true;
        }
        StringBuilder builder = new StringBuilder();
        for (int i = 1; i <= tt; i ++ ) builder.append(s.charAt(stk[i]));

        return builder.toString();
    }
}
```

#### 2、[496. 下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/)

**问题**

`nums1` 中数字 `x` 的 **下一个更大元素** 是指 `x` 在 `nums2` 中对应位置 **右侧** 的 **第一个** 比 `x` 大的元素。

给你两个 **没有重复元素** 的数组 `nums1` 和 `nums2` ，下标从 **0** 开始计数，其中`nums1` 是 `nums2` 的子集。

对于每个 `0 <= i < nums1.length` ，找出满足 `nums1[i] == nums2[j]` 的下标 `j` ，并且在 `nums2` 确定 `nums2[j]` 的 **下一个更大元素** 。如果不存在下一个更大元素，那么本次查询的答案是 `-1` 。

返回一个长度为 `nums1.length` 的数组 `ans` 作为答案，满足 `ans[i]` 是如上所述的 **下一个更大元素** 。

例子

```
输入：nums1 = [4,1,2], nums2 = [1,3,4,2].
输出：[-1,3,-1]
解释：nums1 中每个值的下一个更大元素如下所述：
- 4 ，用加粗斜体标识，nums2 = [1,3,4,2]。不存在下一个更大元素，所以答案是 -1 。
- 1 ，用加粗斜体标识，nums2 = [1,3,4,2]。下一个更大元素是 3 。
- 2 ，用加粗斜体标识，nums2 = [1,3,4,2]。不存在下一个更大元素，所以答案是 -1 。
```

**数据范围**

- `1 <= nums1.length <= nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 10^4`
- `nums1`和`nums2`中所有整数 **互不相同**
- `nums1` 中的所有整数同样出现在 `nums2` 中

**思路**：单调栈+哈希表

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int n = nums1.length, m = nums2.length;

        Map<Integer, Integer> next = new HashMap<>(); // next.get(x) 表示你在nums2中有边第一个比x大的数
        int[] stk = new int[m + 10]; // 单减栈
        int tt = 0;
        for(int i = m - 1; i >= 0; i --) {
            while(tt != 0 && stk[tt] <= nums2[i]) tt --; // 所有小于等于nums2[i]的数都出栈
            if(tt != 0) next.put(nums2[i], stk[tt]);
            else  next.put(nums2[i], -1);

            stk[++ tt] = nums2[i];
        }

        int[] res = new int[n];
        for(int i = 0; i < n; i ++)
            res[i] = next.get(nums1[i]);
        return res;
    }
}
```

#### 4、[503. 下一个更大元素 II](https://leetcode.cn/problems/next-greater-element-ii/)

**问题**

给定一个循环数组 `nums` （ `nums[nums.length - 1]` 的下一个元素是 `nums[0]` ），返回 *`nums` 中每个元素的 **下一个更大元素*** 。

数字 `x` 的 **下一个更大的元素** 是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 `-1` 。

例子

```
输入: nums = [1,2,1]
输出: [2,-1,2]
解释: 第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```

**数据范围**

- `1 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`

**思路**：对问题进行转换, 将循环数组问题转换成$2$个$nums$数组顺序拼接，对顺序拼接后的数组，执行单减栈。

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] arr = new int[2 * n];
        int[] res = new int[2 * n];

        for(int i = 0; i < n; i ++) { // 对问题进行转换, 将循环数组问题转换成2个nums数组顺序拼接
            arr[i] = nums[i];
            arr[i + n] = nums[i];
        }
        // 朴素单调栈
        int[] stk = new int[2*n + 10]; // 单减栈
        int tt = 0;
        for(int i = 2 * n - 1; i >= 0; i --) {
            while(tt != 0 && stk[tt] <= arr[i]) tt --; // 所有小于等于nums[i]的数都出栈
            if(tt != 0) res[i] = stk[tt];
            else res[i] = -1;

            stk[++ tt] = arr[i];
        }
        return Arrays.copyOfRange(res, 0, n); // 只取数组的前n个元素
    }
}
```

#### 5、[739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

**问题**

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。

如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

例子

```
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

**数据范围**

- `1 <= temperatures.length <= 10^5`
- `30 <= temperatures[i] <= 100`

**思路**：单调栈模版题

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] res = new int[n];

        int[] stk = new int[n + 10]; // 单减栈，存储下标
        int tt = 0;
        for(int i = n - 1; i >= 0; i --) {
            while(tt != 0 && temperatures[stk[tt]] <= temperatures[i]) tt --; // // 所有小于等于temperatures[i]的数都出栈
            if(tt != 0) res[i] = stk[tt] - i;
            else res[i] = 0;

            stk[++ tt] = i; 
        }

        return res;
    }
}
```

#### 6、[2454. 下一个更大元素 IV](https://leetcode.cn/problems/next-greater-element-iv/) （双单调栈）:star2:

**问题**

给你一个下标从 **0** 开始的非负整数数组 `nums` 。对于 `nums` 中每一个整数，你必须找到对应元素的 **第二大** 整数。

如果 `nums[j]` 满足以下条件，那么我们称它为 `nums[i]` 的 **第二大** 整数：

- `j > i`
- `nums[j] > nums[i]`
- 恰好存在 **一个** `k` 满足 `i < k < j` 且 `nums[k] > nums[i]` 。

如果不存在 `nums[j]` ，那么第二大整数为 `-1` 。

- 比方说，数组 `[1, 2, 4, 3]` 中，`1` 的第二大整数是 `4` ，`2` 的第二大整数是 `3` ，`3` 和 `4` 的第二大整数是 `-1` 。

请你返回一个整数数组 `answer` ，其中 `answer[i]`是 `nums[i]` 的第二大整数。

例子

```java
输入：nums = [2,4,0,9,6]
输出：[9,6,6,-1,-1]
解释：
下标为 0 处：2 的右边，4 是大于 2 的第一个整数，9 是第二个大于 2 的整数。
下标为 1 处：4 的右边，9 是大于 4 的第一个整数，6 是第二个大于 4 的整数。
下标为 2 处：0 的右边，9 是大于 0 的第一个整数，6 是第二个大于 0 的整数。
下标为 3 处：右边不存在大于 9 的整数，所以第二大整数为 -1 。
下标为 4 处：右边不存在大于 6 的整数，所以第二大整数为 -1 。
所以我们返回 [9,6,6,-1,-1] 。
```

**数据范围**

- `1 <= nums.length <= 105`
- `0 <= nums[i] <= 109`

**思路**

从左往右遍历 $nums$，用（递减）单调栈 $stk1$ 记录元素，如果 $x=nums[i]$ 比 $stk2$ 的栈顶大，则 $x$ 是栈顶的下个更大元素，弹出栈顶元素。最后把 $x$ 入栈（实际入栈的是下标 $i$）。

把弹出的元素加到另一个栈 $stk2$ 中（注意保持原始顺序），后续循环时，如果 $y=nums[j]$ 比 $y$ 的栈顶大，则 $y$ 是栈顶的下下个更大元素，记录答案，弹出栈顶。

```java
class Solution {
    public int[] secondGreaterElement(int[] nums) {
        int n = nums.length;

        int[] res = new int[n];
        Arrays.fill(res, -1); // 初始化

        int[] stk1 = new int[n + 10], stk2 = new int[n + 10]; // 两个单减栈
        int tt1 = 0, tt2 = 0;
        for(int i = 0; i < n; i ++) {
            while(tt2 != 0 && nums[stk2[tt2]] < nums[i])
                res[stk2[tt2 --]] = nums[i]; // nums[i]是第二大的数

            int t = tt1;
            while(tt1 != 0 && nums[stk1[tt1]] < nums[i]) tt1 --; // nums[i]是第一大的数
            // stk1弹出栈的所有数保持其在stk1顺序不变，进入stk2中, 这样可以保证stk2也一定是单减栈
            System.arraycopy(stk1, tt1 + 1, stk2, tt2 + 1, t - tt1);
            tt2 += (t - tt1);
            stk1[++ tt1] = i;
        }

        return res;
    }
}
```

#### 7、[2865. 美丽塔 I](https://leetcode.cn/problems/beautiful-towers-i/) （双单调栈）:star2:

**问题**

给你一个长度为 `n` 下标从 **0** 开始的整数数组 `maxHeights` 。

你的任务是在坐标轴上建 `n` 座塔。第 `i` 座塔的下标为 `i` ，高度为 `heights[i]` 。

如果以下条件满足，我们称这些塔是 **美丽** 的：

1. `1 <= heights[i] <= maxHeights[i]`
2. `heights` 是一个 **山状** 数组。

如果存在下标 `i` 满足以下条件，那么我们称数组 `heights` 是一个 **山状** 数组：

- 对于所有 `0 < j <= i` ，都有 `heights[j - 1] <= heights[j]`
- 对于所有 `i <= k < n - 1` ，都有 `heights[k + 1] <= heights[k]`

请你返回满足 **美丽塔** 要求的方案中，**高度和的最大值** 。

例子：

```
输入：maxHeights = [6,5,3,9,2,7]
输出：22
解释： 和最大的美丽塔方案为 heights = [3,3,3,9,2,2] ，这是一个美丽塔方案，因为：
- 1 <= heights[i] <= maxHeights[i]
- heights 是个山状数组，峰值在 i = 3 处。
22 是所有美丽塔方案中的最大高度和。
```

**数据范围**

- `1 <= n == maxHeights <= 10^3`
- `1 <= maxHeights[i] <= 10^9`

**思路**

枚举「山峰」的下标 $i$。既然 $i$ 是最高的塔，为了让高度和最大，当然要取 $heights[i] = maxHeights[i]$。**接下来考虑「山峰」左边的塔的高度什么时候才能再满足 $heights = maxHeights$**。

<img src="https://pic.leetcode.cn/1695527738-sTCgMF-graph.png" alt="graph.png" style="zoom:75%;" />

从上图可以看到，高度和塔 $i$ 一样的塔有 $(i−j)$ 座。维护 $left[i]$ 表示当 $heights[i] = maxHeights[i]$ 时，塔 $0$ 到塔 $i$ 的最大高度和。则递推方程为：

```java
left[i] = left[j] + heights[i] * (i - j)
```

「左边最近的更小值」是一个经典问题，可以用单调栈解决，详见 [leetcode 496. 下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/)。

当然，上面的式子只考虑了塔 $0$ 到塔 $i$ 的高度和。相似地，可以维护 $right[i] $表示当 $heights[i] = maxHeights[i]$ 时，塔 $i$ 到塔 $(n−1)$ 的最大高度和。则答案为 $left[i] = left[j] + heights[i] * Math.abs(i - j)$。

复杂度 $\mathcal{O}(n)$。

```java
class Solution {
    public long maximumSumOfHeights(List<Integer> maxHeights) {
        int n = maxHeights.size();
        maxHeights.add(0, -1);
        // 单调栈
        int[] stk = new int[n + 10]; // stk存储是下标
        int tt = 0;

        // left[i] 表示以i为山峰构造山状数组, i左边高度和最大值; right[i] 表示i右边高度和最大值
        long[] left = new long[n + 10], right = new long[n + 10];
        stk[0] = 0; // 哨兵
        // 从左向右
        for(int i = 1; i <= n; i ++ ) {
            while(tt != 0 && maxHeights.get(stk[tt]) > maxHeights.get(i)) 
                tt --; // 栈顶元素出栈
            int j = stk[tt];
            left[i] = left[j] + (long)maxHeights.get(i) * (i - j);
            stk[++ tt] = i;
        }

        // 从右向左
        stk = new int[n + 10];
        tt = 0;
        stk[0] = n + 1; // 哨兵
        for(int i = n; i > 0; i --) {
            while(tt != 0 && maxHeights.get(stk[tt]) > maxHeights.get(i))
                tt --;
            int j = stk[tt];
            right[i] = right[j] + (long)maxHeights.get(i) * Math.abs(i - j);
            stk[++ tt] = i;
        }

        long res = 0;
        for(int i = 1; i <= n; i ++) 
            res = Math.max(res, left[i] + right[i] - maxHeights.get(i));
        return res;
    }
}
```

#### 8、[2866. 美丽塔 II](https://leetcode.cn/problems/beautiful-towers-ii/)

**问题**

给你一个长度为 `n` 下标从 **0** 开始的整数数组 `maxHeights` 。

你的任务是在坐标轴上建 `n` 座塔。第 `i` 座塔的下标为 `i` ，高度为 `heights[i]` 。

如果以下条件满足，我们称这些塔是 **美丽** 的：

1. `1 <= heights[i] <= maxHeights[i]`
2. `heights` 是一个 **山状** 数组。

如果存在下标 `i` 满足以下条件，那么我们称数组 `heights` 是一个 **山状** 数组：

- 对于所有 `0 < j <= i` ，都有 `heights[j - 1] <= heights[j]`
- 对于所有 `i <= k < n - 1` ，都有 `heights[k + 1] <= heights[k]`

请你返回满足 **美丽塔** 要求的方案中，**高度和的最大值** 。

例子：

```
输入：maxHeights = [6,5,3,9,2,7]
输出：22
解释： 和最大的美丽塔方案为 heights = [3,3,3,9,2,2] ，这是一个美丽塔方案，因为：
- 1 <= heights[i] <= maxHeights[i]
- heights 是个山状数组，峰值在 i = 3 处。
22 是所有美丽塔方案中的最大高度和。
```

**数据范围**

- **`1 <= n == maxHeights <= 10^5`**
- `1 <= maxHeights[i] <= 10^9`

**思路**

```java
class Solution {
    public long maximumSumOfHeights(List<Integer> maxHeights) {
        int n = maxHeights.size();
        maxHeights.add(0, -1);
        // 单调栈
        int[] stk = new int[n + 10]; // stk存储是下标
        int tt = 0;

        // left[i] 表示以i为山峰构造山状数组, i左边高度和最大值; right[i] 表示i右边高度和最大值
        long[] left = new long[n + 10], right = new long[n + 10];
        stk[0] = 0; // 哨兵
        // 从左向右
        for(int i = 1; i <= n; i ++ ) {
            while(tt != 0 && maxHeights.get(stk[tt]) > maxHeights.get(i)) 
                tt --; // 栈顶元素出栈
            int j = stk[tt];
            left[i] = left[j] + (long)maxHeights.get(i) * (i - j);
            stk[++ tt] = i;
        }

        // 从右向左
        stk = new int[n + 10];
        tt = 0;
        stk[0] = n + 1; // 哨兵
        for(int i = n; i > 0; i --) {
            while(tt != 0 && maxHeights.get(stk[tt]) > maxHeights.get(i))
                tt --;
            int j = stk[tt];
            right[i] = right[j] + (long)maxHeights.get(i) * Math.abs(i - j);
            stk[++ tt] = i;
        }

        long res = 0;
        for(int i = 1; i <= n; i ++) 
            res = Math.max(res, left[i] + right[i] - maxHeights.get(i));
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
