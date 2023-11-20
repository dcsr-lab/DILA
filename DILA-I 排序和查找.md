# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之排序与查找



### 1、排序与查找

#### （1）5217. 将杂乱无章的数字排序

问题：

给你一个下标从 **0** 开始的整数数组 `mapping` ，它表示一个十进制数的映射规则，`mapping[i] = j` 表示这个规则下将数位 `i` 映射为数位 `j` 。

一个整数 **映射后的值** 为将原数字每一个数位 `i` （`0 <= i <= 9`）映射为 `mapping[i]` 。

另外给你一个整数数组 `nums` ，请你将数组 `nums` 中每个数按照它们映射后对应数字非递减顺序排序后返回。

**注意：**

- 如果两个数字映射后对应的数字大小相同，则将它们按照输入中的 **相对顺序** 排序。
- `nums` 中的元素只有在排序的时候需要按照映射后的值进行比较，返回的值应该是输入的元素本身。
- `mapping[i]` 的值 **互不相同** 。

**示例**：

```
输入：mapping = [8,9,4,0,2,1,3,5,7,6], nums = [991,338,38]
输出：[338,38,991]
```

**思路**：

- 采用稳定的排序方式，归并排序
- 两个数据比较大小时，采用映射后的值
- 排序是对原数组进行排序

```java
class Solution {
    
    public int get(int x, int[] mapping){ // 得到x在mapping映射规则下映射后的数
        if(x == 0) return mapping[x]; // 特判
        Deque<Integer> stk = new ArrayDeque<>();
        
        while(x != 0){
            stk.push(mapping[x % 10]);
            x /= 10;
        }
        
        int res = 0;
        while(!stk.isEmpty())
            res = res * 10 + stk.pop();
        return res;
    }
    
    public void mergeSort(int[] a, int[] q, int l, int r){ // 采用a进行数据的大小比较排序, 同时将对应q也进行排序
        if(l == r) return ; // 区间中只有一个数
        
        int mid = l + r >> 1;
        mergeSort(a, q, l, mid); mergeSort(a, q, mid + 1, r); // 分治
        
        int i = l, j = mid + 1, k = 0;
        int[] tmpa = new int[r - l + 10];
        int[] tmpq = new int[r - l + 10];
        
        while(i <= mid && j <= r){ // 二路归并
            if(a[i] <= a[j]){ tmpa[k] = a[i]; tmpq[k ++] = q[i ++];  }
            else { tmpa[k] = a[j]; tmpq[k ++] = q[j ++]; }
        }
        
        while(i <= mid) { tmpa[k] = a[i]; tmpq[k ++] = q[i ++]; }
        while(j <= r) { tmpa[k] = a[j]; tmpq[k ++] = q[j ++]; }
        
        for(int t = 0; t < k; t ++) { q[l + t] = tmpq[t]; a[l + t] = tmpa[t]; }
    }
    
    public int[] sortJumbled(int[] mapping, int[] nums) {
        int[] a = new int[nums.length];
        
        for(int i = 0; i < nums.length; i ++ )
            a[i] = get(nums[i], mapping);
        
        mergeSort(a, nums, 0, nums.length - 1);
        return nums;
    }
}
```

#### （2）[27. 移除元素](https://leetcode.cn/problems/remove-element/)

**问题**：

给你一个数组 `nums` 和一个值 `val`，你需要 **[原地](https://baike.baidu.com/item/原地算法)** 移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 `O(1)` 额外空间并 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组**。元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

例子

```java
输入：nums = [0,1,2,2,3,0,4,2], val = 2
输出：5, nums = [0,1,4,0,3]
解释：函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。注意这五个元素可为任意顺序。你不需要考虑数组中超出新长度后面的元素。
```

**数据范围**：

- `0 <= nums.length <= 100`
- `0 <= nums[i] <= 50`
- `0 <= val <= 100`

**思路**：

- 将所有的数从小到大进行排序
- **二分搜索**val的左边界和右边界
- 采用**双指针**算法，将val移到数组的末尾

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if(nums.length == 0) return 0;

        int n = nums.length;
        Arrays.sort(nums); // 排序

        int start = 0, end = 0;
        int l = 0, r = n - 1;
        while(l < r) { // 左边界
            int mid = l + r >> 1;
            if(nums[mid] >= val) r = mid;
            else l = mid + 1;
        }

        if(nums[l] != val) return n;
        start = l;
        r = n - 1;

        while(l < r) { // 右边界
            int mid = l + r  + 1>> 1;
            if(nums[mid] <= val) l = mid;
            else r = mid - 1;
        }
        end = r;

        for(int i = start, j = n - 1; i <= end; i ++, j --) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
        return n - (end - start + 1);
    }
}
```

#### （3）[35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

**问题**：

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 `O(log n)` 的算法。

例子：

```
输入: nums = [1,3,5,6], target = 2
输出: 1
```

**数据范围**：

- `1 <= nums.length <= 104`
- `-104 <= nums[i] <= 104`
- `nums` 为 **无重复元素** 的 **升序** 排列数组
- `-104 <= target <= 104`

思路：

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        if(target > nums[nums.length - 1]) return nums.length; // 特判
        
        int l = 0, r = nums.length - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        return l;
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
