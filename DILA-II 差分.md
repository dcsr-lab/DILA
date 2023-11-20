# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之差分



### 1、差分

#### （1）370. 区间加法

**问题**：

假设你有一个长度为 **n** 的数组，初始情况下所有的数字均为 **0**，你将会被给出 **k** 个更新的操作。

其中，每个操作会被表示为一个三元组：**[startIndex, endIndex, inc]**，你需要将子数组 **A[startIndex ... endIndex]**（包括 startIndex 和 endIndex）增加 **inc**。

请你返回 **k** 次操作后的数组。

**思路**：**差分**

```python
class Solution:

    def __init__(self):
        self.a = [] # 原数组, 下标从1开始
        self.b = [] # 差分数组, 下标从1开始
        self.n = 0

    def insert(self, l, r, c):
        self.b[l] += c
        self.b[r + 1] -= c

    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
        self.n = length
        self.a = [0 for i in range(self.n + 10)] # 元组组所有数字为0
        self.b = [0 for i in range(self.n + 10)] # 差分数组所有数字也为0

        for i in range(len(updates)):
            l, r, c = updates[i][0], updates[i][1], updates[i][2]
            self.insert(l + 1, r + 1, c)

        for i in range(1, self.n + 1):
            self.a[i] = self.a[i - 1] + self.b[i]
        
        return self.a[1:self.n + 1] # 返回[1, n + 1)
```



#### （2）[2251. 花期内花的数目](https://leetcode.cn/problems/number-of-flowers-in-full-bloom/)

**问题**

给你一个下标从 **0** 开始的二维整数数组 `flowers` ，其中 `flowers[i] = [starti, endi]` 表示第 `i` 朵花的 **花期** 从 `starti` 到 `endi` （都 **包含**）。

给你一个下标从 **0** 开始大小为 `n` 的整数数组 `people` ，`people[i]` 是第 `i` 个人来看花的时间。

请你返回一个大小为 `n` 的整数数组 `answer` ，其中 `answer[i]`是第 `i` 个人到达时在花期内花的 **数目** 。

例子

<img src="https://assets.leetcode.com/uploads/2022/03/02/ex1new.jpg" alt="img" style="zoom:67%;" />

```
输入：flowers = [[1,6],[3,7],[9,12],[4,13]], people = [2,3,7,11]
输出：[1,2,2,2]
解释：上图展示了每朵花的花期时间，和每个人的到达时间。
对每个人，我们返回他们到达时在花期内花的数目。
```

**数据范围**

- `1 <= flowers.length <= 5 * 104`
- `flowers[i].length == 2`
- `1 <= starti <= endi <= 109`
- `1 <= people.length <= 5 * 104`
- `1 <= people[i] <= 109`

**思路**：离散化+差分

```java
class Solution {

    public void insert(int l, int r, int c, int[] b) {
        b[l] += c;
        b[r + 1] -= c;
    }

    public int[] fullBloomFlowers(int[][] flowers, int[] people) {
        // 离散化
        Set<Integer> set = new HashSet<>(); // 去重
        for(int i = 0; i < flowers.length; i ++ ) {
            int a = flowers[i][0], b = flowers[i][1];
            set.add(a); set.add(b);
        }
        for(int i = 0; i < people.length; i ++) 
            set.add(people[i]);

        ArrayList<Integer> arr = new ArrayList<>(set);
        Collections.sort(arr); // 排序

        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < arr.size(); i ++) 
            map.put(arr.get(i), i + 1);
        
        // 差分
        int n = arr.size();
        int[] b = new int[n + 10];

        for(int i = 0; i < flowers.length; i ++) {
            int x = flowers[i][0], y = flowers[i][1];
            insert(map.get(x), map.get(y), 1, b);
        }

        int[] a = new int[n + 10];
        for(int i = 1; i < a.length; i ++ )
            a[i] = a[i - 1] + b[i];
        
        int[] res = new int[people.length];
        for(int i = 0; i < people.length; i ++)
            res[i] = a[map.get(people[i])];

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
