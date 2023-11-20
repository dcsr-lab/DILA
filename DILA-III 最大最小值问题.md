# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第三部分之最大最小值

### 1、最大化最小值（二分+贪心）

[题单](https://leetcode.cn/problems/minimum-time-to-repair-cars/solutions/2177199/er-fen-da-an-pythonjavacgo-by-endlessche-keqf/?envType=daily-question&envId=2023-09-07) 

#### （1）[2517. 礼盒的最大甜蜜度](https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/)

**问题**：

给你一个正整数数组 `price` ，其中 `price[i]` 表示第 `i` 类糖果的价格，另给你一个正整数 `k` 。

商店组合 `k` 类 **不同** 糖果打包成礼盒出售。礼盒的 **甜蜜度** 是礼盒中任意两种糖果 **价格** 绝对差的最小值。

返回礼盒的 **最大** 甜蜜度*。*

例子：

```
输入：price = [13,5,1,8,21,2], k = 3
输出：8
解释：选出价格分别为 [13,5,21] 的三类糖果。
礼盒的甜蜜度为 min(|13 - 5|, |13 - 21|, |5 - 21|) = min(8, 8, 16) = 8 。
可以证明能够取得的最大甜蜜度就是 8 。
```

**数据范围**：

- `1 <= price.length <= 10^5`
- `1 <= price[i] <= 10^9`
- `2 <= k <= price.length`

**思路**：**最大化最小值问题**

二份答案, 设 $D=max(price)-min(price), O(logD)$

答案的区间一定位于$ [0, max(price)-min(price)]$ 中

二分的中值是$m$什么？ 是否可以找到$k$个数，它们两两之差的绝对值的最小值至少是$m$

- 如果可以找到，那么 $l = mid$
- 如果找不到 $r = mid - 1$;

那么，如何快速找到$k$个数，它们两两之差的绝对值至少是$m$？

对于一个已经排好序的数组$price$, 我们迭代这个数组

对于$price[i]$, 我们计算$price[i]$与上一个已经取走的candy之间的差值, 如果差值大于等于$m$, 我们就取走这个candy。如果最后找不到k个这样的糖果，那么就不存在这样一个方案。

总的时间复杂度$O(n * (logn + logD)$。

```java
class Solution {

    public boolean check(int[] price, int x, int k) {
        int last = price[0];
        k -= 1 ;
        for(int i = 1; i < price.length && k > 0; i ++) { // 对于一个已经排好序的数组price, 我们迭代这个数组
            if(price[i] - last >= x) { // 计算price[i]与上一个已经取走的candy之间的差值, 
                last = price[i]; // 如果差值大于等于x, 我们就取走这个candy
                k -- ;
            }
        }
        return k <= 0; // 找不到k个这样的糖果，那么就不存在这样一个方案
    }

    public int maximumTastiness(int[] price, int k) {
        int n = price.length;
        Arrays.sort(price);

        int l = 0, r = price[n - 1] - price[0]; // 答案一定位于 [0, max(price)-min(price)]区间中

        while(l < r) { // 在区间[l,r]中二份查找答案
            int mid = (l + r + 1) >> 1;
            if(check(price, mid, k)) l = mid; // 可以找到k个数，它们两两之差的绝对值的最小值至少是mid
            else r = mid - 1; // 找不到
        }

        return l;
    }
}
```

#### （2）[1552. 两球之间的磁力](https://leetcode.cn/problems/magnetic-force-between-two-balls/)

**问题**：

在代号为 C-137 的地球上，Rick 发现如果他将两个球放在他新发明的篮子里，它们之间会形成特殊形式的磁力。Rick 有 `n` 个空的篮子，第 `i` 个篮子的位置在 `position[i]` ，Morty 想把 `m` 个球放到这些篮子里，使得任意两球间 **最小磁力** 最大。

已知两个球如果分别位于 `x` 和 `y` ，那么它们之间的磁力为 `|x - y|` 。

给你一个整数数组 `position` 和一个整数 `m` ，请你返回最大化的最小磁力。

例子：

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/08/16/q3v1.jpg" alt="img" style="zoom:75%;" />

```
输入：position = [1,2,3,4,7], m = 3
输出：3
解释：将 3 个球分别放入位于 1，4 和 7 的三个篮子，两球间的磁力分别为 [3, 3, 6]。最小磁力为 3 。我们没办法让最小磁力大于 3 。
```

**数据范围**：

- `n == position.length`
- `2 <= n <= 10^5`

- `1 <= position[i] <= 10^9`
- 所有 `position` 中的整数 **互不相同** 。
- `2 <= m <= position.length`

**思路**：思路同上题一样**二分+贪心**，最大化最小值问题。

```java
class Solution {

    public boolean check(int[] position, int x, int m) {
        int last = position[0];
        m -- ;
        for(int i = 1; i < position.length && m > 0; i ++ )
            if(position[i] - last >= x) {
                last = position[i];
                m -- ;
            }
        return m <= 0;
    }

    public int maxDistance(int[] position, int m) {
        Arrays.sort(position);
        
        int n = position.length;
        int l = 0, r = position[n - 1] - position[0]; // 最小最大值一定位于此区间
        while(l < r) {
            int mid = (l + r + 1) >> 1;
            if(check(position, mid, m)) l = mid;
            else r = mid - 1;
        }
        return l;
    }
}
```

#### （3）[2528. 最大化城市的最小供电站数目](https://leetcode.cn/problems/maximize-the-minimum-powered-city/)



### 2、最小化最大值（二分+贪心）

#### （1）[2594. 修车的最少时间](https://leetcode.cn/problems/minimum-time-to-repair-cars/)



#### （2）[2560. 打家劫舍 IV](https://leetcode.cn/problems/house-robber-iv/)



#### （3）[2439. 最小化数组中的最大值](https://leetcode.cn/problems/minimize-maximum-of-array/)



#### （4）[2616. 最小化数对的最大差值](https://leetcode.cn/problems/minimize-the-maximum-difference-of-pairs/)



#### （5）[2513. 最小化两个数组中的最大值](https://leetcode.cn/problems/minimize-the-maximum-of-two-arrays/)





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
