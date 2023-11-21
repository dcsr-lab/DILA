# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之小根堆



### 1、最小优先队列（小根堆）

#### （1）[1851. 包含每个查询的最小区间](https://leetcode.cn/problems/minimum-interval-to-include-each-query/)

**问题**：

给你一个二维整数数组 `intervals` ，其中 `intervals[i] = [lefti, righti]` 表示第 `i` 个区间开始于 `lefti` 、结束于 `righti`（包含两侧取值，**闭区间**）。区间的 **长度** 定义为区间中包含的整数数目，更正式地表达是 `righti - lefti + 1` 。

再给你一个整数数组 `queries` 。第 `j` 个查询的答案是满足 `lefti <= queries[j] <= righti` 的 **长度最小区间 `i` 的长度** 。如果不存在这样的区间，那么答案是 `-1` 。

以数组形式返回对应查询的所有答案。

例子

```
输入：intervals = [[1,4],[2,4],[3,6],[4,4]], queries = [2,3,4,5]
输出：[3,3,1,4]
解释：查询处理如下：
- Query = 2 ：区间 [2,4] 是包含 2 的最小区间，答案为 4 - 2 + 1 = 3 。
- Query = 3 ：区间 [2,4] 是包含 3 的最小区间，答案为 4 - 2 + 1 = 3 。
- Query = 4 ：区间 [4,4] 是包含 4 的最小区间，答案为 4 - 4 + 1 = 1 。
- Query = 5 ：区间 [3,6] 是包含 5 的最小区间，答案为 6 - 3 + 1 = 4 。
```

**数据范围**：

- `1 <= intervals.length <= 10^5`
- `1 <= queries.length <= 10^5`
- `intervals[i].length == 2`
- `1 <= lefti <= righti <= 10^7`
- `1 <= queries[j] <= 10^7`

**思路**：自定义排序+优先队列

题目答案限制条件是目标区间 $val[j]$，区间左端点$val[j][0]$不大于$que[i]$(右端点不小于$que[i]$)。

可以先将区间集合 $val$ 按**左端点**递增排序，每轮搜索所有左端点不大于$que[i]$的区间，排除右端点小于$que[i]$的区间

但是还需在**合格区间中找到最短**的，可以利用用**最小优先队列**自定义按区间长度**维护最短合法区间**。

- 将左端点$<=que[i]$区间全加入队列，判断队首右端点是否$>=que[i]$，若不是，则未覆盖，直接弹出；否则作为答案记录。若队弹空，说明没有包含$qeu[i]$的区间，$ans[i]$为$-1$。
- $que$查询在默认乱序下每次都要搜索整个$val$耗时过高，因此可以采用离线查询思想保存$que$原下标后按递增排序，这样后方查询下标就可以服用前方的信息，因为排序后后方下标必不小于前方，所以前方排出的区间后方也不会再用到，可以永久删除，达到查询范围越来越小的效果。最终遍历完$que$后，按照保存的原始下标赋给$ans$即可。

```java
class Solution {
    public int[] minInterval(int[][] intervals, int[] queries) {
        int n = intervals.length, m = queries.length;
        Map<Integer, ArrayList<Integer>> map = new HashMap<>();

        // 小根堆，按区间长度存储
        PriorityQueue<int[]> heap = new PriorityQueue<>((a, b)->(a[1] - a[0] - (b[1] - b[0])));

        for(int i = 0; i < m; i ++) {
            var arr = map.getOrDefault(queries[i], new ArrayList<Integer>());
            arr.add(i);
            map.put(queries[i], arr);
        }
        Arrays.sort(queries); // O(NlogN)
        Arrays.sort(intervals, (a, b) -> (a[0] != b[0]? a[0] - b[0] : a[1] - b[1])); //按区间左端点排序

        int[] res = new int[m];
        for(int i = 0, j = 0; i < m; i ++) { // O(NlogN)
            int x = queries[i];
            while(j < n && intervals[j][0] <= x) heap.offer(intervals[j ++]); // 左端点在x左边的所有区间入堆
            while(heap.size() != 0 && heap.peek()[1] < x) heap.poll(); // 将右端点也在x左边的区间出堆
            var t = (heap.size() == 0 ?  -1 : heap.peek()[1] - heap.peek()[0] + 1);
            var arr = map.get(x);
            res[arr.get(arr.size() - 1)] = t;
            arr.remove(arr.size() - 1);
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
