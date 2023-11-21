# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>

> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  





# 第三部分之TopK问题



### 1、TopK+多路归并/堆/二分/双指针

#### （1）[378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/)（多路归并）

**问题**：

给你一个 `n x n` 矩阵 `matrix` ，其中每行和每列元素均按升序排序，找到矩阵中第 `k` 小的元素。

请注意，它是 **排序后** 的第 `k` 小元素，而不是第 `k` 个 **不同** 的元素。

你必须找到一个内存复杂度优于 `O(n^2)` 的解决方案。

例子：

```
输入：matrix = [[1,5,9],[10,11,13],[12,13,15]], k = 8
输出：13
解释：矩阵中的元素为 [1,5,9,10,11,12,13,13,15]，第 8 小元素是 13
```

**数据范围**：

- `n == matrix.length`
- `n == matrix[i].length`
- `1 <= n <= 300`
- `-10^9 <= matrix[i][j] <= 10^9`
- 题目数据 **保证** `matrix` 中的所有行和列都按 **非递减顺序** 排列
- `1 <= k <= n^2`

**思路**：

多路归并：核心思想是采用分治，将多路归并转换为二路归并。时间复杂度是 $O(nlogn)$

- 采用归并排序中的分治思想，将$n$个有序序列分成两份序列。
- 对左右两份序列进行递归的划分，直到每一份中只有一个序列。
- 将左右两个任务结果进行二路归并

```java
class Solution {
    // nlogn
    public int[] merger(int[][] matrix, int l, int r) {
        if(l >= r) return matrix[l];

        int mid = l + r >> 1;
        int[] a = merger(matrix, l, mid), b = merger(matrix, mid + 1, r);

        int n = a.length, m = b.length;
        int[] temp = new int[n + m];
        int i = 0, j = 0, k = 0;
        
        while(i < n && j < m) {
            if(a[i] < b[j]) temp[k ++] = a[i ++];
            else temp[k ++] = b[j ++];
        }
        while(i < n) temp[k ++] = a[i ++];
        while(j < m) temp[k ++] = b[j ++];

        return temp;
    }   

    public int kthSmallest(int[][] matrix, int k) {
        int[] arr = merger(matrix, 0, matrix.length - 1);
        return arr[k - 1];
    }
}
```

你能否用一个恒定的内存(即 $O(1)$ 内存复杂度)来解决这个问题?
你能在 $O(n)$ 的时间复杂度下解决这个问题吗?这个方法对于面试来说可能太超前了，但是你会发现阅读这篇文章（ [this paper](http://www.cse.yorku.ca/~andy/pubs/X+Y.pdf) ）很有趣。

#### （2）[373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/) (加法，二维，堆)

**问题**：

给定两个以 **升序排列** 的整数数组 `nums1` 和 `nums2` , 以及一个整数 `k` 。

定义一对值 `(u,v)`，其中第一个元素来自 `nums1`，第二个元素来自 `nums2` 。

请找到和最小的 `k` 个数对 `(u1,v1)`, ` (u2,v2)` ...  `(uk,vk)` 。

例子：

```
输入: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
输出: [1,2],[1,4],[1,6]
解释: 返回序列中的前 3 对数：
     [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```

**数据范围**：

- `1 <= nums1.length, nums2.length <= 10^5`

- `-10^9 <= nums1[i], nums2[i] <= 10^9`
- `nums1` 和 `nums2` 均为升序排列
- `1 <= k <= 1000`

**思路**：[讲清楚为什么一开始要把 (i,0) 都入堆！]([讲清楚为什么一开始要把 (i,0) 都入堆！附题单！ - 查找和最小的 K 对数字 - 力扣（LeetCode）](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/solution/jiang-qing-chu-wei-shi-yao-yi-kai-shi-ya-i0dj/)) 

初步思路：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-05-28 20.56.11.png" alt="截屏2023-05-28 20.56.11" style="zoom:50%;" />

一个复杂的算法：

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-05-28 20.58.41.png" alt="截屏2023-05-28 20.58.41" style="zoom:50%;" />

![学生登录用例图 (10).png](https://pic.leetcode-cn.com/1642133430-NIIiwX-%E5%AD%A6%E7%94%9F%E7%99%BB%E5%BD%95%E7%94%A8%E4%BE%8B%E5%9B%BE%20(10).png)

上图中的（i,j）表示的是nums1下标i与nums2下标j，箭头表示的是若选择数对（i,j），那么就需要把数对（i,j+1）与（i+1,j）加入到候选中。
由图可以发现，若（0,1）和（1,0）均被选过，那么（1,1）数对就会在候选中出现2次，也就是造成了重复。

```java
class Solution {

    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        
        int n = nums1.length, m = nums2.length;
        HashMap<List<Integer>, Boolean> map = new HashMap<>(); // 判重
        List<List<Integer>> list = new ArrayList<>(); // 存储结果

        PriorityQueue<List<Integer>> heap = new PriorityQueue<>((a, b) -> { // 小根堆
            return a.get(2) + a.get(3) - (b.get(2) + b.get(3));
        });

        heap.offer(Arrays.asList(new Integer[]{0, 0, nums1[0], nums2[0]})); // 初始化小根堆
        map.put(Arrays.asList(new Integer[]{0, 0, nums2[0], nums2[0]}), true); // 初始化map

        while(list.size() < k && heap.size() != 0) { // 取出最小的k对数字
            List<Integer> pair = heap.poll();
            int x = pair.get(0), y = pair.get(1);

            list.add(Arrays.asList(new Integer[]{nums1[x], nums2[y]}));

            List<Integer> a = null, b = null; // 比(x, y) 大的数字对有(x + 1, y)和(x, y + 1)
            if(x + 1 < n && y < m) a = Arrays.asList(new Integer[]{x + 1, y, nums1[x + 1], nums2[y]}); // 判断不能数组越界
            if(x  < n && y + 1 < m) b = Arrays.asList(new Integer[]{x, y + 1, nums1[x], nums2[y + 1]});

            if(a != null && map.get(a) == null) { // 判重
                heap.offer(a);
                map.put(a, true);
            }
            if(b != null && map.get(b) == null) {
                heap.offer(b);
                map.put(b, true);
            }
        }

        return list;
    }
}
// (0, 0) -> (1, 0), (0, 1)
// (i, j) ->(i + 1, j), (i, j + 1)
// (1, 0) - > (1, 1), (2, 0)
// (0, 1) - > (1, 1), (2, 0)
```

优化后的算法： 

<img src="/Users/yjzhai/Library/Application Support/typora-user-images/截屏2023-05-28 21.00.14.png" alt="截屏2023-05-28 21.00.14" style="zoom:50%;" />

那么如何解决重复问题呢？
（0,1）数对值>=（1,1）数对值
（1,0）数对值>=（1,1）数对值
这说明了当（1,1）作为答案前，（0,1）与（1,0）均已被作为答案。
那么我们只需要保留（0,1）->（1,1）与（1,0）->（1,1）其中一个箭头即可。

<img src="https://pic.leetcode-cn.com/1642134228-zyNpBD-image.png" alt="image.png" style="zoom: 67%;" />

时间复杂度是 $O(klogk）$ 

```java
class Solution {

    public List<List<Integer>> getTopk(int[] a, int[] b, int k) {
        int n = a.length, m = b.length;

        List<List<Integer>> topk = new ArrayList<>(); // 存储结果
        PriorityQueue<int[]> heap = new PriorityQueue<>((x, y) -> x[2] - y[2]);
        for(int i = 0; i < n; i ++ ) heap.offer(new int[]{i, 0, a[i] + b[0]});

        for(int i = 0; i < k && heap.size() != 0; i ++) {
            int[] t = heap.poll();
            int tx = t[0], ty = t[1];
            topk.add(Arrays.asList(new Integer[]{a[tx], b[ty]}));

            if(tx < n && ty + 1 < m) heap.offer(new int[]{ tx, ty + 1, a[tx] + b[ty + 1]});
        }
        return topk;
    }

    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        return getTopk(nums1, nums2, k);
    }
}
```

#### （3）[1439. 有序矩阵中的第 k 个最小数组和](https://leetcode.cn/problems/find-the-kth-smallest-sum-of-a-matrix-with-sorted-rows/)（加法，多维，分治+堆）

**问题**：

给你一个 `m * n` 的矩阵 `mat`，以及一个整数 `k` ，矩阵中的每一行都以非递减的顺序排列。你可以从每一行中选出 1 个元素形成一个数组。返回所有可能数组中的第 k 个 **最小** 数组和。

例子：

```
输入：mat = [[1,3,11],[2,4,6]], k = 5
输出：7
解释：从每一行中选出一个元素，前 k 个和最小的数组分别是：
[1,2], [1,4], [3,2], [3,4], [1,6]。其中第 5 个的和是 7 。
```

**数据范围**：

- `m == mat.length`
- `n == mat.length[i]`
- `1 <= m, n <= 40`
- `1 <= k <= min(200, n ^ m)`
- `1 <= mat[i][j] <= 5000`
- `mat[i]` 是一个非递减数组

**思路**：

朴素算法：堆 + 哈希表

与[373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)的求解思路一致，只不过扩展成成多个维度

```java
class Solution {
    public int kthSmallest(int[][] mat, int k) {
        int n = mat.length, m = mat[0].length;
        
        List<Integer> res = new ArrayList<>();
        HashMap<List<Integer>, Boolean> map = new HashMap<>();
        PriorityQueue<List<Integer>> heap = new PriorityQueue<>((a, b) -> a.get(a.size()-1) - b.get(b.size()-1));

        int sum = 0;
        List<Integer> arr = new ArrayList<>();
        for(int i = 0; i < n; i ++) {
            sum += mat[i][0];
            arr.add(0);
        }
        arr.add(sum);
        map.put(arr, true);
        heap.offer(arr);

        while(res.size() < k) {
            List<Integer> t = heap.poll();
            res.add(t.get(t.size() - 1));

            for(int i = 0; i < n; i ++ ) {
                List<Integer> list = new ArrayList<>(t);
                int a = list.get(i), s = list.get(list.size() - 1);
                
                if(a + 1 >= m) continue;
                list.set(i, a + 1);
                list.set(list.size() - 1, s - mat[i][a] + mat[i][a + 1]);

                if(map.get(list) == null) {
                    heap.offer(list);
                    map.put(list, true);
                }
            }
        }
        return res.get(res.size() - 1);
    }
}
```

优化后的算法：分治+堆

[378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/) 和 [373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/) 两者思想的结合

时间复杂度是 $O(k \times logk \times n)$其中 $n$ 是 $mat$ 矩阵的行数。

```java
class Solution {

    public int[] getTopk(int[] a, int[] b, int k) {
        int n = a.length, m = b.length;

        PriorityQueue<int[]> heap = new PriorityQueue<>((x, y) -> x[2] - y[2]);
        for(int i = 0; i < n; i ++ ) heap.offer(new int[]{i, 0, a[i] + b[0]});

        int[] topk = new int[k];
        int len = 0;
        for(int i = 0; i < k && heap.size() != 0; i ++) {
            int[] t = heap.poll();
            int tx = t[0], ty = t[1];
            topk[i] = t[2];
            len ++ ;
            if(tx < n && ty + 1 < m) heap.offer(new int[]{ tx, ty + 1, a[tx] + b[ty + 1]});
        }
        return Arrays.copyOf(topk, len);
    }

    public int kthSmallest(int[][] mat, int k) {
        int[] topk = mat[0];
        for(int i = 1; i < mat.length; i ++ ) 
            topk = getTopk(topk, mat[i], k);
        return topk[k - 1];
    }
}
```

#### （4）[786. 第 K 个最小的素数分数](https://leetcode-cn.com/problems/k-th-smallest-prime-fraction/)（除法，堆）

**问题**：

给你一个**按递增顺序排序**的数组 `arr` 和一个整数 `k` 。数组 `arr` 由 `1` 和若干 **素数** 组成，且其中所有整数互不相同。

对于每对满足 `0 <= i < j < arr.length` 的 `i` 和 `j` ，可以得到分数 `arr[i] / arr[j]` 。

那么第 `k` 个最小的分数是多少呢? 以长度为 `2` 的整数数组返回你的答案, 这里 `answer[0] == arr[i]` 且 `answer[1] == arr[j]` 。

例子：

```java
输入：arr = [1,2,3,5], k = 3
输出：[2,5]
解释：已构造好的分数,排序后如下所示: 
1/5, 1/3, 2/5, 1/2, 3/5, 2/3
很明显第三个最小的分数是 2/5
```

**数据范围**：

- `2 <= arr.length <= 1000`
- `1 <= arr[i] <= 3 * 104`
- `arr[0] == 1`
- `arr[i]` 是一个 **素数** ，`i > 0`
- `arr` 中的所有数字 **互不相同** ，且按 **严格递增** 排序
- `1 <= k <= arr.length * (arr.length - 1) / 2`

**思路**：与 373. 查找和最小的 K 对数字 的思想一致

朴素算法：堆+哈希表

时间复杂度是 $O(klogk)$

```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        PriorityQueue<double[]> heap = new PriorityQueue<>((a, b) -> (int)Math.signum(a[2] - b[2]));
        boolean st[][] = new boolean[n][n]; // 判重

        heap.offer(new double[]{0, n - 1, (double)arr[0]/arr[n - 1]});
        st[0][n - 1] = true;

        List<int[]> topk = new ArrayList<>();
        while(topk.size() < k) {
            double[] t = heap.poll();
            int tx = (int)t[0], ty = (int)t[1];
            topk.add(new int[]{arr[tx], arr[ty]});

            if(tx + 1 < ty && !st[tx + 1][ty]) {
                heap.offer(new double[]{tx + 1, ty, (double)arr[tx + 1]/arr[ty]});
                st[tx + 1][ty] = true;
            }
            if(tx < ty - 1 && !st[tx][ty - 1]) {
                heap.offer(new double[]{tx, ty - 1, (double)arr[tx]/arr[ty - 1]});
                st[tx][ty - 1] = true;
            }
        }
        return topk.get(k - 1);
    }
}
```

优化后的算法：堆

数组 $arr$ 是一个**按递增顺序排序**的数组。那么最小的分数一定是 $arr[0]/arr[n - 1]$，$n$ 是数组的长度。我们采用优先队列来维护当前的最小的分数。正如上面朴素算法所示，当元素对 $(i,j)$出堆时，$(i+1,j)$和$(i,j-1)$会入堆，因为这个两个元素对是比$(i,j)$次大的元素对。

但是，对于 $[0,1,2,3]$，当$(1,3)$出堆时，$(1,2)$会入堆。当$(0,2)$出堆时，$(1,2)$也会入堆。由于**存在重复元素**对，朴素算法采用了`HashMap`来判重。

使用额外`HashMap`来判重，无疑增大了内存消耗。有没有什么方法可以降低内存占用。

与之前**373. 查找和最小的 K 对数字**的做法一致，我们预先将 $(i,n-1),i=0,...,n-2$元素对入堆。然后，当$(i,j)$出堆时只执行$(i,j-1)$入堆操作。

时间复杂度是 $O(klogk)$

```java
class Solution {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        PriorityQueue<double[]> heap = new PriorityQueue<>((a, b) -> (int)Math.signum(a[2] - b[2]));

        for(int i = 0; i < n - 1; i ++)
            heap.offer(new double[]{i, n - 1, (double)arr[i]/arr[n - 1]});

        List<int[]> topk = new ArrayList<>();
        while(topk.size() < k) {
            double[] t = heap.poll();
            int tx = (int)t[0], ty = (int)t[1];
            topk.add(new int[]{arr[tx], arr[ty]});

            if(tx < ty - 1) heap.offer(new double[]{tx, ty - 1, (double)arr[tx]/arr[ty - 1]});
        }
        return topk.get(k - 1);
    }
}
```

#### （5）[719. 找出第 k 小的距离对](https://leetcode-cn.com/problems/find-k-th-smallest-pair-distance/)（减法，数据范围大，二分嵌套二分/双指针）

**问题**：

数对 `(a,b)` 由整数 `a` 和 `b` 组成，其数对距离定义为 `a` 和 `b` 的绝对差值。给你一个整数数组 `nums` 和一个整数 `k` ，数对由 `nums[i]` 和 `nums[j]` 组成且满足 `0 <= i < j < nums.length` 。返回 **所有数对距离中** 第 `k` 小的数对距离。

例子：

```java
输入：nums = [1,3,1], k = 1
输出：0
解释：数对和对应的距离如下：
(1,3) -> 2
(1,1) -> 0
(3,1) -> 2
距离第 1 小的数对是 (1,1) ，距离为 0 。
```

**数据范围**：

- `n == nums.length`
- `2 <= n <= 10^4`
- `0 <= nums[i] <= 10^6`
- `1 <= k <= n * (n - 1) / 2`

**思路**：

**方法一：堆**

我们首先将$nums$数组从小到大排序，这样 $nums$ 是一个按递增顺序排序的数组。那么最小的**距离对**一定是 $|arr[0]-arr[1]|$，设$n$ 是数组的长度。我们采用**优先队列**来维护当前的最小的距离对。当元素对 $(i,j), i<j且i≠j$出堆时，让$(i+1,j+1)$和$(i,j+1)$会入堆，因为这个两个元素对是比$(i,j)$**次大的距离对**。

但是，对于 $[0,1,2,3]$，当$(1,2)$出堆时，$(1,3)$会入堆。当$(0,2)$出堆时，$(1,3)$也会入堆。由于存在重复元素对，**朴素算法**可以采用了`HashMap`来判重。

使用额外`HashMap`来判重，无疑增大了内存消耗。有没有什么方法可以降低内存占用。

与之前**373. 查找和最小的 K 对数字**的做法一致，我们预先将 $(i,i+1),i=0,...,n-2$元素对入堆。然后，当$(i,j)$出堆时**只执行**$(i,j+1)$入堆操作。

优化后的算法的时间复杂度是$O(klogk)$，**考虑到数据范围非常大**，$k=10^8$，时下面的算法依然会超时和超出内存限制。

```java
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        int n = nums.length;
        Arrays.sort(nums);

        PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        for(int i = 0; i < n - 1; i ++) 
            heap.offer(new int[]{i, i + 1, Math.abs(nums[i] - nums[i + 1])});

        int res = 0;
        while(k > 0 && heap.size() != 0) {
            int[] t = heap.poll();
            int tx = t[0], ty = t[1];
            res = t[2];
            k -- ;

            if(ty + 1 < n) heap.offer(new int[]{tx, ty + 1, Math.abs(nums[tx] - nums[ty + 1])});
        }

        return res;
    }
}
```

上述算法的时间复杂度与$k$有关，而$k$与数据的规模$n$有关。**我们能不能设计一个时间复杂度与$k$无关的算法**。

**方法二：排序+二分查找+嵌套二分查找**

先将数组 $nums$ 从小到大进行排序，那么所有数对中第 $k$ 小的数对距离一定出现在在区间$[0,(max(nums)-min(nums))]$中。令$left=0,right=(max(nums)-min(nums))$，我们可以在$[left, right]$区间上采用**二分查找**枚举第 $k$ 小的数对距离。

对于当前搜索的距离 $mid$，查找小于等于距离 $mid$ 的数对的个数 $cnt$。如果 $cnt >= k$，令$right = mid - 1$; 如果 $cnt < k$，令$left = mid + 1$。直到 $left > right$，我们找到了第$k$小的距离$left$。

那么对于给定一个距离 $mid$， 如何查找小于等于距离 $mid$的数对的个数 $cnt$ 呢？

对于方法二，我们采用嵌套的二分查找来解决。对于方法三，我们采用双指针算法来解决。

枚举每一个右端点$j$，在已排序的数组$nums$中，二分查找大于$nums[j]-mid$的**最小**的$i$。那么，以$j$ 为右端点的小于等于$mid$距离对的个数为$j - i$。统计所有的$j-i$之和，即距离小于等于$mid$的数对的个数$cnt$。

时间复杂度分析：排序的时间复杂度为$O(nlogn)$，设$D=max(nums)-min(nums)$，外层二分的时间复杂度为$O(logD)$，内层二分的时间复杂度为 $O(nlogn)$，总的时间复杂度为$O(nlogn+nlogn \times logD)=>O(nlogn \times logD)$

```java
class Solution {
    public int bsearch(int[]nums, int x) { // 在升序数组nums中查找距离小于等于x的数对的个数cnt
        int cnt = 0;
        for(int j = 0; j < nums.length; j ++) { // 枚举每一个数对的右端点j
            int l = 0, r = j; 
            while(l < r) { // 在区间[0, j]找到一个最小的mid使得[mid,j]区间的任意下标与的数对距离都小于等于x
                int mid = l + r >> 1;
                if(nums[j] - nums[mid] <= x) r = mid;
                else l = mid + 1;
            }
            cnt += (j - l); // (j-l)表示以j为右边界的数对的个数(该数对距离小于等于x)
        }
        return cnt;
    }

    public int check(int[] nums, int mid) {
        return bsearch(nums, mid);
    }

    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums); // 从小到大排序
        int n = nums.length;
        
        int l = 0, r = nums[n - 1] - nums[0]; // 二分区间，[0,(max(nums)-min(nums))],第k小的数对距离一定在此区间
        while(l < r) {
            int mid = l + r >> 1;
            if(check(nums, mid) >= k) r = mid;
            else l = mid + 1; 
        }

        return l;
    }
}
```

**方法三：排序+二分查找+双指针**

对于已排序的数组$nums$，初始化左指针$i=0$，从小到大枚举所有数对的右端点$j$，移动左端点直到$nums[j]-[i]<=mid$，那么右端点为$j$且数对的距离小于等于$mid$的数对个数$j-i$，计算这些数目之和。

时间复杂度分析：排序的时间复杂度为$O(nlogn)$，设$D=max(nums)-min(nums)$，二分的时间复杂度为$O(logD)$，双指针算法的时间复杂度为$O(n)$。总的时间复杂度为$O(n(logn+logD))$

```java
class Solution {

    public int bpoint(int[] nums, int mid) {
        int cnt = 0;
        for(int i = 0, j = 0; j < nums.length; j ++ ) {
            while(nums[j] - nums[i] > mid) i ++ ;
            cnt += j - i; // 加上区间[i,j]中所有以j为右边界的数对的个数
        }
            
        return cnt;
    }

    public int check(int[] nums, int mid) {
        return bpoint(nums, mid);
    }

    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums); // 从小到大排序
        int n = nums.length;
        
        int l = 0, r = nums[n - 1] - nums[0]; // 二分区间，[0,(max(nums)-min(nums))],第k小的数对距离一定在此区间
        while(l < r) {
            int mid = l + r >> 1;
            if(check(nums, mid) >= k) r = mid;
            else l = mid + 1; 
        }

        return l;
    }
}
```



#### （6）2040. 两个有序数组的第 K 小乘积（乘法，数据范围大，不适用O(klogk)算法）



#### （7）2386. 找出数组的第 K 大和（子序列和，问题转换，堆）

**问题**：

给你一个整数数组 `nums` 和一个 **正** 整数 `k` 。你可以选择数组的任一 **子序列** 并且对其全部元素求和。

数组的 **第 k 大和** 定义为：可以获得的第 `k` 个 **最大** 子序列和（子序列和允许出现重复）

返回数组的 **第 k 大和** 。

子序列是一个可以由其他数组删除某些或不删除元素排生而来的数组，且派生过程不改变剩余元素的顺序。

**注意：**空子序列的和视作 `0` 。

例子：

```java
输入：nums = [2,4,-2], k = 5
输出：2
解释：所有可能获得的子序列和列出如下，按递减顺序排列：
- 6、4、4、2、2、0、0、-2
数组的第 5 大和是 2 。
```

**数据范围**：

- `n == nums.length`
- `1 <= n <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `1 <= k <= min(2000, 2n)`

**思路**：

此题的$n<=10^5$，显然我们无法暴力穷举所有的子序列。但是，此题的$k<=2000$，我们可以选择时间复杂度为$O(k)$的算法。

**从简化问题开始**  

首先考虑本题的简化问题，给定$n$个**非负**数$a_1,a_2,...,a_n$，求第$k$个**最小**子序列的和。

这是一个经典的问题。我们先把所有数**从小到大排序**，记$(s,i)$表示一个总和为$s$，且最后一个元素是第$i$个元素的子序列。

我们用一个小根堆维护$(s,i)$，一开始堆中只有一个元素$(a_1,1)$。当我们取出堆顶元素$(s,i)$时，我们可以进行以下操作：

- 把$a_{i+1}$接到这个子序列的后面形成新的子序列，也就是将$(s+a_{i+1},i+1)$放入堆中。
- 把子序列中的$a_i$直接替换成$a_{i+1}$，也就是将$(s-a_i+a_{i+1},i+1)$放入堆中。

第$(k-1)$次取出的$(s,i)$中的$s$就是答案($k=1$时答案为空集之和，也就是0)。

这个做法的正确性基于以下事实：

- 这种方法能**不重不漏**地生成所有子序列。
- 每次放进去的数不小于拿出来的数。

**最小和变最大和**

实际上，求第$k$个最大的子序列和，与求第$k$个最小的子序列和是一样的。我们求出$k$小子序列后取反（选择不在答案中的所有元素作为新的答案），就能得到$k$大子序列。因此，所有元素之和减去$k$小子序列和，就能得到$k$大子序列和。

**引入负数**

接下来回到原问题，考虑**给定的数中有负数**的情况。

首先计算 $m$表示所有负数的和，然后将所有负数变成他们的绝对值（这样就回到了全是非负数的情况）。答案就是$m$加上$k$大子序列的和。

为什么这样是对的？（实际上，所有数取绝对值之后，求第$k$大子序列的和。对所有的子序列和将去一个数$m$，**其相对顺序是不变的**。减去$m$前是第$k$子序列，减去$m$后依然是第$k$大子序列。）

考虑到由此得到的$k$大子序列，它实际上唯一对应了一个原有的子序列。我们举个例子：

- 一开始给定整数 ${-3,-2,-1,4,5,6}$;
- 经过转换之后，我们得到$k$大子序列 ${2,1,5,6}$;
- 对于所有在该子序列中的非负数，令它成为答案的一部分，也就是说5和6是答案的一部分；
- 对于所有不在该子序列中的负数，令它成为答案的一部分，也就是说-3是答案的一部分；
- 最后得到的真实答案${-3,5,6}$。

时间复杂度分析，排序的时间复杂度是$O(nlogn)$，k个数出入堆的时间复杂度是$O(klogk)$，总的时间复杂度为$O(nlogn+klogk)$

```java
class Solution {
    public long kSum(int[] nums, int k) {
        int n = nums.length;

        long m = 0, sum = 0; // m表示所有负数之和,sum表示所有数的绝对值之和
        for(int i = 0; i < n; i ++ ) { // 将负数取绝对值 
            if(nums[i] < 0) {
                m += nums[i];
                nums[i] = -nums[i];
            }
            sum += nums[i];
        }

        Arrays.sort(nums); // 从小到大排序

        // 小根堆求绝对值数组k小子序列之和
        PriorityQueue<long[]> heap = new PriorityQueue<long[]>((a, b)-> (int)(a[0]-b[0]) );
        heap.offer(new long[]{nums[0], 0});

        long res = 0; // k = 1时视为空集之和，也就是0
        for(int i = 0; i < k - 1; i ++ ) {
            long[] t = heap.poll();
            long s = t[0];
            int pos = (int)t[1];
            res = s;
            // 两个操作
            if(pos + 1 < n) heap.offer(new long[]{s + nums[pos + 1], pos + 1});
            if(pos + 1 < n) heap.offer(new long[]{s - nums[pos] + nums[pos + 1], pos + 1});
        }

        // 最小和变最大和
        res = sum - res; // 绝对值数组的k大子序列之和
        // 引入负数
        return res + m;
    }
}
```



#### （8）[2583. 二叉树中的第 K 大层和](https://leetcode.cn/problems/kth-largest-sum-in-a-binary-tree/)（分层的层序遍历+堆）

**问题**：

给你一棵二叉树的根节点 `root` 和一个正整数 `k` 。

树中的 **层和** 是指 **同一层** 上节点值的总和。

返回树中**第 `k` 大的层和**（不一定不同）。如果树少于 `k` 层，则返回 `-1` 。

**注意**，如果两个节点与根节点的距离相同，则认为它们在同一层。

例子：

<img src="https://assets.leetcode.com/uploads/2022/12/14/binaryytreeedrawio-2.png" alt="img" style="zoom:80%;" />



```
输入：root = [5,8,9,2,1,3,7,4,6], k = 2
输出：13
解释：树中每一层的层和分别是：
- Level 1: 5
- Level 2: 8 + 9 = 17
- Level 3: 2 + 1 + 3 + 7 = 13
- Level 4: 4 + 6 = 10
第 2 大的层和等于 13 。
```

**数据范围**：

- 树中的节点数为 `n`
- `2 <= n <= 10^5`
- `1 <= Node.val <= 10^6`
- `1 <= k <= n`

思路：

树的层序遍历+大根堆。时间复杂度为$O(nlogn)$，当树的形状是一个长度为$n$的当链表时，可以达到这个最坏的时间复杂度。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {

    public long bfs(TreeNode u, int k) {
        int hh = 0, tt = 0;
        TreeNode[] q = new TreeNode[100010];
        q[0] = u; // 根节点如队列

        PriorityQueue<Long> heap = new PriorityQueue<Long>((a, b)-> -a.compareTo(b));
        while(hh <= tt) { // 当tt < hh时, 队列为空
            int head = hh, tail = tt;
            long sum = 0; // 记录当前层所有元素之和
            while(hh <= tail) { // 遍历树的每一层节点
                TreeNode t = q[hh ++];
                sum += t.val;
                if(t.left != null) q[++ tt] = t.left;
                if(t.right != null) q[++ tt] = t.right;
            }
            heap.offer(sum);
        }
        if(heap.size() < k) return -1;
        while(-- k != 0) heap.poll();
        return heap.poll();
    }

    public long kthLargestLevelSum(TreeNode root, int k) {
        return bfs(root, k);
    }
}
```



#### （9）[1508. 子数组和排序后的区间和](https://leetcode.cn/problems/range-sum-of-sorted-subarray-sums/) （子序列和，二分嵌套双指针+前缀和）

**问题**：

给你一个数组 `nums` ，它包含 `n` 个正整数。你需要计算所有非空连续子数组的和，并将它们按升序排序，得到一个新的包含 `n * (n + 1) / 2` 个数字的数组。

请你返回在新数组中下标为 `left` 到 `right` **（下标从 1 开始）**的所有数字和（包括左右端点）。由于答案可能很大，请你将它对 10^9 + 7 取模后返回。

例子：

```
输入：nums = [1,2,3,4], n = 4, left = 1, right = 5
输出：13 
解释：所有的子数组和为 1, 3, 6, 10, 2, 5, 9, 3, 7, 4 。将它们升序排序后，我们得到新的数组 [1, 2, 3, 3, 4, 5, 6, 7, 9, 10] 。下标从 le = 1 到 ri = 5 的和为 1 + 2 + 3 + 3 + 4 = 13 。
```

**数据范围**:

- `1 <= nums.length <= 10^3`
- `nums.length == n`
- `1 <= nums[i] <= 100`
- `1 <= left <= right <= n * (n + 1) / 2`

**思路**：二分+(双指针+前缀和)+(双指针+前缀和的前缀和)

具体思路：[子数组和排序后的区间和 - 子数组和排序后的区间和 - 力扣（LeetCode）](https://leetcode.cn/problems/range-sum-of-sorted-subarray-sums/solution/zi-shu-zu-he-pai-xu-hou-de-qu-jian-he-by-leetcode-/) 

- 将所有的子数组的和从小到大排序，记前$left - 1$个子数组的和为 $a$，前$right$个子数组的和为$b$
- 那么下标为 $left$ 到 $right$ （下标从 1 开始）的所有数字和为 $b - a$. （有点前缀和求区间和的思想在里面）

要实现此思路，需要解决一下四个子问题：

**问题一**：**给定一个数组，如何计算该数组中第k小的子数组的和？** （**类似题目：2386.找出数组的第K大和**）

采用二分来实现，其时间复杂度是$O(log(D)))$ ，其中 $D=(sum(nums))$

- 确定二分区间：该数组中第 $k$ 小的子数组的和一定位于$[0, sum(nums)]$之间
- 对于二分的中点$mid$，我们都需要找到子数组和小于$mid$的个数$cnt$
- 如果 $cnt >= k$, 那么 $r = mid$
- 如果 $cnt < k$, 那么 $l = mid + 1$

**问题二**：**对于给定的数组 $nums$，如何快速统计出有多少个子数组的和小于等于$mid$？**

预处理出前缀和数组 $s$ ，并采用双指针来求解，其时间复杂度是 $O(n)$

因为要计算子数组的和，就等于要计算区间和，可以了利用前缀和数组，计算区间和的时间复杂度是 $O(1)$

此问题就转换为了，对于前缀和数组(有序)，有多少个数对的距离小于等于mid？(**类似题目:719.找出第k小的距离对**）

双指针算法, 该双指针算法尝试对于区间$[i + 1, j]$, 对于**任意以下标$j$结尾**的长度不超过$j - i$的子数组, 其元素之和都不超过目标值$x$。

```java
int cnt = 0;
for(int i = 0; j = 0; j < s.length; j ++ ) // 前缀和数组下标是从1开始，但是这里枚举需要从0开始
    while(s[j] - s[i] > mid) i ++ ; // 因为子数组的元素个数也可以是1
    cnt += j - i;
```

到此为止，我们就可以计算出原数组中第k小的子数组和是多少了

**问题三**：**已知第$k$个子数组的和是 $x$, 我们如何快速计算前$k$个子数组的和$sum$，以及它们的个数$count$？**

此问题可以转换为，已知前缀和数组，计算有多少个数对的距离小于等于 $x$, 计算其个数$count$和距离之和$sum$？

双指针算法，该双指针算法尝试枚举对于区间$[i + 1, j]$, 对于**任意从下标$i+1$开始**长度不超过$j - i$的子数组, 其元素之和都不超过目标值$x$。

```java
int cnt = 0, sum = 0;
for(int i = 0; j = 1; i < s.length; i ++ ) // 前缀和数组下标是从1开始，但是这里枚举需要从0开始
    while(j < s.length && s[j] - s[i] < x) j ++ ; // 第一步计算所有严格小于x的子数组的和的个数以及它们的和
    j --;
		cnt += j - i;
    // 如何快速计算区间[i,j]中所有以i为左端点的子数组和的之和？要O(1)而不是O(n)
    // s[j] - s[i] 表示区间[i,j]中所有元素之和
    // sum(s[t] - s[i]), t = i, ..., j
    // <=> (s[i + 1] - s[i]) + (s[i + 2] - s[i]) + ... + (s[j] - s[i])
    // <=> (s[i + 1] + s[i + 2] + ... + s[j]) - s[i] * (j - i)
    // <=> (sp[j] - sp[i]) - s[i] * (j - i) // sp是前缀和的区间和
    sum += (sp[j] - sp[i]) - s[i] * (j - i);
sum = (sum + x * (k - cnt)) % MOD;
```

**问题四**: **已知前$left-1$个子数组的和为$a$，前$right$个子数组的和为$b$，计算下标为$left$到$right$(下标从1开始)的所有数字和**

前缀和求区间和的思想: $b - a$

```java
class Solution {
    final int MOD = (int)1e9 + 7; // 1000000007

    public int rangeSum(int[] nums, int n, int left, int right) {
        int[] s = new int[n + 1];
        int[] sp = new int[n + 1];
        for(int i = 1; i <= n; i ++ ) {
            s[i] = s[i - 1] + nums[i - 1];
            sp[i] = (sp[i - 1] + s[i]);
        }
        return getsum(s, sp, right) - getsum(s, sp, left - 1);
    }

    public int getsum(int[] s, int[] sp, int k) {
        int x = findk(s, k);
        int cnt = 0, sum = 0;
        for(int i = 0, j = 1; i < s.length; i ++ ) { // 第一步计算所有严格小于x的子数组的和的个数以及它们的和
            while(j < s.length && s[j] - s[i] < x) j ++ ;
            j --;
            cnt += (j - i);
          	// 对于区间[i + 1, j], 对于任意从下标i+1开始长度不超过(j - i)的子数组, 其元素之和都不超过目标值x
            sum = (sum + sp[j] - sp[i] - s[i] * (j - i)) % MOD;
        }
        sum = (sum + x * (k - cnt)) % MOD; // 第二步计算所有严格等于x的子数组的和的个数以及它们的和
        return sum;
    }

    public int findk(int[] s, int k) { // 给定一个数组，如何计算该数组中第k小的子数组的和？
        int l = 0, r = s[s.length - 1]; 
        while(l < r) {
            int mid = (l + r) >> 1;
            if(check(s, mid) >= k) r = mid;
            else l = mid + 1;
        }
        return l;
    }

    public int check(int[] s, int x) { // 计算 对于给定的数组 nums，如何快速统计出有多少个子数组的和小于等于x？
        int cnt = 0;
        for(int i = 0, j = 0; j < s.length; j ++ ) {
            while(i < s.length && s[j] - s[i] > x) i ++ ;
          	// 对于区间[i + 1, j], 对于任意以下标j结尾的长度不超过(j - i)的子数组, 其元素之和都不超过目标值x
            if (i < s.length) cnt += (j - i);
        }
        return cnt;
    }
}
```













[1982. 从子集的和还原数组](https://leetcode.cn/problems/find-array-given-subset-sums/)



[1675. 数组的最小偏移量](https://leetcode-cn.com/problems/minimize-deviation-in-array/)



[6455. 使所有字符相等的最小成本](https://leetcode.cn/problems/minimum-cost-to-make-all-characters-equal/)

6456. 矩阵中严格递增的单元格数

[6394. 字符串中的额外字符](https://leetcode.cn/problems/extra-characters-in-a-string/)







### :mag_right: 参考文献 

[1] https://leetcode.cn/

[2] https://www.acwing.com/

### :closed_lock_with_key: License

本文内容不是将网上的资料随意拼凑而来，除了少部分引用书上和技术文档的原文（这部分内容都在末尾的参考文献中加了出处），其余都是作者的原创。在您引用本文内容或者对内容进行修改演绎时，请署名并以相同方式共享，谢谢。

转载文章请在开头明显处标明该页面地址，公众号等其它转载请联系 [1246550576@qq.com](mailto:1246550576@qq.com)

### 📝排版申明

笔记内容按照一定标准进行排版，以保证内容的可读性。

不使用 `![]()` 这种方式来引用图片，而是用 `<img>` 标签。为了能够控制图片以合适的大小显示，使用 `<div align="center"> <img src=""/> </div>` 达到居中的效果。

排版美化工具：[Typora-Purple](https://theme.typora.io/theme/Purple/)

