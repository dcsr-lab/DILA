# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之双指针

### 1、双指针

#### （1）228. 汇总区间

**问题**：

给定一个  **无重复元素** 的 **有序** 整数数组 `nums` 。

返回 **恰好覆盖数组中所有数字** 的 **最小有序** 区间范围列表 。也就是说，`nums` 的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个范围但不属于 `nums` 的数字 `x` 。

列表中的每个区间范围 `[a,b]` 应该按如下格式输出：

- `"a->b"` ，如果 `a != b`
- `"a"` ，如果 `a == b`

**示例**：

```python
输入：nums = [0,1,2,4,5,7]
输出：["0->2","4->5","7"]
```

**思路**：**双指针**

- 设置两个指针`i`, `j`，`[i,j)`是要找的连续区间

```python
class Solution:
    def summaryRanges(self, nums: List[int]) -> List[str]:
        n, res = len(nums), []

        i = 0 #  [i, j)表示连续区间, i指向连续区间的开始
        while i < n:
            j = i + 1 # j指针指向连续区间的结束的下一个位置
            while j < n and nums[j] - nums[i] == j - i: # j右移
                j += 1
            # 找到一个连续区间
            st, ed = nums[i], nums[j - 1]
            res.append(str(st) if st == ed else "%d->%d" % (st, ed))
            i = j # 更新i
        return res
```

#### （2）163. 缺失的区间

**问题**：

给定一个排序的整数数组 **nums** ，其中元素的范围在 **闭区间** **[lower, upper]** 当中，返回不包含在数组中的缺失区间。

**示例：**

```
输入: nums = [0, 1, 3, 50, 75], lower = 0 和 upper = 99,
输出: ["2", "4->49", "51->74", "76->99"]
```

**思路**：**双指针**

```python
class Solution:
    def findMissingRanges(self, nums: List[int], lower: int, upper: int) -> List[str]:
        nums = [lower - 1] + nums + [upper + 1] # 加入左右边界
        n, res = len(nums), []

        i = 0 # (i, j) 表示缺失区间
        while i < n:
            j = i + 1
            while j < n and (nums[j] == nums[i] + 1 or nums[j] == nums[i]): # 同步右移 i, j
                i, j = i + 1, j + 1
            
            if j < n: # 找到一个缺失区间
                st, ed = nums[i] + 1, nums[j] - 1
                res.append(str(st) if st == ed else "%d->%d" % (st, ed))
            i = j # 更新 i
        return res
```

#### （3）189. 轮转数组:writing_hand:  

**问题**：

给你一个数组，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。

**示例**：

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
```

**思路**：**三次反转数组**

- 使用**双指针**实现数组反转

- 

- 第一次反转数组 `[0, n -1]`
- 第二次反转数组 `[0, k - 1]`
- 第三次反转数组 `[k, n - 1]`

```java
class Solution {

    public void reverse(int[] a, int l, int r){ // 反转数组中[l, r]的所有数
        for(int i = l, j = r; i < j; i ++, j --) { // 采用双指针反转
            int t = a[i];
            a[i] = a[j];
            a[j] = t;
        }
    }

    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n; // 细节
        reverse(nums, 0, n - 1);
        reverse(nums, 0, k - 1); reverse(nums, k, n - 1);
    }
}
```

#### （4）[1. 两数之和](https://leetcode.cn/problems/two-sum/)（双指针/哈希表）

**问题**：

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并**返回它们的数组下标**。

你可以假设**每种输入只会对应一个答案**。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

例子：

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**数据范围**：

- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **只会存在一个有效答案**

**思路**：

双指针法：$O(nlogn)$

- 构建哈希表，键位$nums[i]$，值为数组下标$i$;
- 对数组$num$从小到大排序；
- 设置两个指针，一个指针$i$指向数组的左端点，另一个指针$j$指向数组的右端点。指针移动的规则为:
  - 如果$nums[i] + nums[j] < target$ ，则 $i ++ $；
  - 如果$nums[i] + nums[j] > target$，则 $j ++$;
  - 如果$nums[i] + nums[j] == target$，迭代停止，找到了两个整数。依据之前构建的哈希表，找到其值对应的下标

```java
public int[] twoSum(int[] nums, int target) {
    HashMap<Integer, Queue<Integer>> mp = new HashMap<>(); // 构建映射表, 存储每个数存现的下标位置

    for(int i = 0; i < nums.length; i ++) {
        if(mp.containsKey(nums[i])) mp.get(nums[i]).offer(i);
        else {
            Queue<Integer> q = new ArrayDeque<>();
            q.offer(i);
            mp.put(nums[i], q);
        }
    }

    Arrays.sort(nums); // 所有数从小到大排序

    for(int i = 0, j = nums.length - 1; i < j;){ // 双指针算法
        if(nums[i] + nums[j] == target){
            int[] res = {mp.get(nums[i]).poll(), mp.get(nums[j]).poll()};
            return res;
        }

        if(nums[i] + nums[j] < target) i ++;
        else if(nums[i] + nums[j] > target) j --;
    }
    return new int[0];
}
```

哈希表法：$O(n)$

```java
public int[] twoSum(int[] nums, int target) {
    HashMap<Integer,Integer> map = new HashMap<>();
    for(int i = 0; i < nums.length; i++){
        if(map.containsKey(nums[i])){
            return new int[]{map.get(nums[i]), i};
        }
        map.put(target - nums[i], i);
    }
    return null;
}
```

#### （5）[653. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/two-sum-iv-input-is-a-bst/)（双指针）

**问题**：

给定一个二叉搜索树 `root` 和一个目标结果 `k`，如果二叉搜索树中存在两个元素且它们的和等于给定的目标结果，则返回 `true`。

例子：

<img src="https://assets.leetcode.com/uploads/2020/09/21/sum_tree_1.jpg" alt="img" style="zoom:67%;" />

```
输入: root = [5,3,6,2,4,null,7], k = 9
输出: true
```

**数据范围**：

- 二叉树的节点个数的范围是 `[1, 10^4]`.
- `-10^4 <= Node.val <= 10^4`
- 题目数据保证，输入的 `root` 是一棵 **有效** 的二叉搜索树
- `-10^5 <= k <= 10^5`

**思路**：$O(n)$

- 二叉搜索树的**中序遍历**的结点序列是一个有序序列;
- 设置两个指针，一个指针$i$指向数组的左端点，另一个指针$j$指向数组的右端点。指针移动的规则为:
  - 如果$nums[i] + nums[j] < target$ ，则 $i ++ $；
  - 如果$nums[i] + nums[j] > target$，则 $j ++$;
  - 如果$nums[i] + nums[j] == target$，迭代停止，找到了两个整数

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
    List<Integer> inorder = new ArrayList<>();

    public void dfs(TreeNode u) {

        if(u.left != null) dfs(u.left);
        inorder.add(u.val);
        if(u.right != null) dfs(u.right);
    }

    public boolean findTarget(TreeNode root, int k) {
        if(root == null) return false;
        dfs(root);

        for(int i = 0, j = inorder.size() - 1; i < j;) {
            int a = inorder.get(i), b = inorder.get(j);
            if(a + b < k) i ++ ;
            else if(a + b > k) j --;
            else return true;
        }
        return false;
    }
}
```

#### （6）[167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)（双指针）

**问题**：

给你一个下标从 **1** 开始的整数数组 `numbers` ，该数组已按 **非递减顺序排列** ，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。给你一个下标从 **1** 开始的整数数组 `numbers` ，该数组已按 **非递减顺序排列** ，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。

以长度为 2 的整数数组 `[index1, index2]` 的形式返回这两个整数的下标 `index1` 和 `index2`。

你可以假设每个输入 **只对应唯一的答案** ，而且你 **不可以** 重复使用相同的元素。

你所设计的解决方案必须**只使用常量级的额外空间**。

例子：

```
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。返回 [1, 2] 。
```

**数据范围**：

- `2 <= numbers.length <= 3 * 10^4`
- `-1000 <= numbers[i] <= 1000`
- `numbers` 按 **非递减顺序** 排列
- `-1000 <= target <= 1000`
- **仅存在一个有效答案**

**思路**：$O(n)$

设置两个指针，一个指针$i$指向数组的左端点，另一个指针$j$指向数组的右端点。指针移动的规则为:

- 如果$nums[i] + nums[j] < target$ ，则 $i ++ $；
- 如果$nums[i] + nums[j] > target$，则 $j ++$;
- 如果$nums[i] + nums[j] == target$，迭代停止，找到了两个整数

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        for(int i = 0, j = numbers.length - 1; i < j; ) {
            if(numbers[i] + numbers[j] == target)
                return new int[]{i + 1 ,j + 1};
            else if(numbers[i] + numbers[j] > target) j --;
            else i ++ ;
        }
        return new int[0];
    }
}
```

#### （7）[15. 三数之和](https://leetcode.cn/problems/3sum/)（双指针）

**问题**：

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且`j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

例子：

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**数据范围**：

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

**思路**：

双指针法：$O((1+n)n/2))$

- 将$nums$数组从小到大排序，$nums$数组中可能存在重复元素，排序的好处是可以使得重复的元素聚集在一起，方便去重。
- 将$nums[i],i=0,...,nums.length-1$视为$target$，从$i + 1, ...,nums,length-1$中寻找两数之和等于$target$。可能存在多对数之和等于$target$

```java
for(int l = i + 1, r = n - 1; l < r; ) { // l 从i + 1开始，因为之前的情况已经被讨论过了
    int sum = nums[l] + nums[r] + nums[i];
    if(sum == 0) {
        list.add(Arrays.asList(new Integer[]{ nums[i], nums[l], nums[r]}));
    
        while(l + 1 < r && nums[l] == nums[l + 1]) l ++ ; // 去除重复的元素
        while(l < r - 1 && nums[r] == nums[r - 1]) r --; // 去重重复的元素
        l ++ ; r -- ; // 同时更新l, r
    }
    else if(sum > 0) r -- ;
    else l ++ ;
}
```

- 对于$nums$中的每一个元素都执行上述第二步的算法流程。

完整的代码如下：

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    int n = nums.length;

    Arrays.sort(nums); // 从小到大排序
    for(int i = 0; i < n - 2; i ++ ) {
        if(i > 0 && nums[i] == nums[i - 1]) continue; // 去重, nums[i]已经处理过了
        if(nums[i] > 0) break; // 因为nums经过从小到大排序, i 之后的元素都大于0, 不可能存在有三个数之和等于0的情况

        for(int l = i + 1, r = n - 1; l < r; ) { // l 从i + 1开始，因为之前的情况已经被讨论过了
            int sum = nums[l] + nums[r] + nums[i];
            if(sum == 0) {
                list.add(Arrays.asList(new Integer[]{ nums[i], nums[l], nums[r]}));
            
                while(l + 1 < r && nums[l] == nums[l + 1]) l ++ ; // 去除重复的元素
                while(l < r - 1 && nums[r] == nums[r - 1]) r --; // 去重重复的元素
                l ++ ; r -- ; // 同时更新l, r
            }
            else if(sum > 0) r -- ;
            else l ++ ;
        }
    }
    return list;
}
```

哈希表法：$O(n^2)$，将三数之后转换成两数之和

- 对于任意一个数 $nums[k],k=0,...,nums.length-1$，如果存在另外两个数 $nums[i]$和$nums[j]$，使得$nums[i] + nums[j] + nums[k] == 0$成立。
- 那么如何找到两个数 $nums[i]$和$nums[j]$，使得他们之和等于 $-nums[k]$。这就是两数之和问题。
- 遍历所有的 $k=0,1,...,nums.length-1$，将使$nums[i] + nums[j] + nums[k] == 0$成立的集合$[nums[i],nums[j],nums[k]]$收集到集合`List`中并去重。
- 采用`HashSet`去重，`HashSet<List<Integer>> res = new HashSet<List<Integer>>()`。

```java
class Solution {
    HashSet<List<Integer>> res = new HashSet<List<Integer>>();
  
    // 两数之和O(n):给定一个整数数组 nums 和一个整数目标值 target，在该数组中找出 和为目标值 target 的那两个整数
    public void search(int[] nums, int index, int target) { 
        HashMap<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i < nums.length; i ++ ) {
            if(i == index) continue;
            if(map.containsKey(nums[i])) {
                Integer[] arr = new Integer[]{nums[i], nums[map.get(nums[i])], -target};
                Arrays.sort(arr);
                res.add(Arrays.asList(arr));
            } else map.put(target - nums[i], i);
        }
    }

    public List<List<Integer>> threeSum(int[] nums) {
        HashMap<Integer, Boolean> map = new HashMap<>(); // 去重
        for(int i = 0; i < nums.length; i ++ )
            if(!map.getOrDefault(nums[i], false)) { // 判断是否重复处理过
                search(nums, i, -nums[i]);
                map.put(nums[i], true);
            }
        return new ArrayList<>(res);
    }
}
```

#### （8）[18. 四数之和](https://leetcode.cn/problems/4sum/)（双指针）

**问题**：

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。

请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` 若两个四元组元素一一对应，则认为两个四元组重复）：

- `0 <= a, b, c, d < n`
- `a`、`b`、`c` 和 `d` **互不相同**
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

你可以按 **任意顺序** 返回答案 。

**例子**：

```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**数据范围**：

- `1 <= nums.length <= 200`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`

思路：

双指针法：$O(n^3)$

```javascript
public List<List<Integer>> fourSum(int[] nums, int target) {
    List<List<Integer>> res = new ArrayList<>();
    int n = nums.length;

    Arrays.sort(nums);

    for(int i = 0; i < n - 3; i ++ ) {
        if(i > 0 && nums[i] == nums[i - 1]) continue;
        if(nums[i] > target / 4) break;

        for(int j = i + 1; j < n - 2; j ++ ) {
            if(j > i + 1 && nums[j] == nums[j - 1]) continue;
            if(nums[i] > ((long)target - nums[i]) / 3) break;

            for(int l = j + 1, r = n - 1; l < r;) {
                long sum = nums[i] + nums[j] + nums[l] + nums[r];
                if(sum == target) {
                    res.add(Arrays.asList(new Integer[]{nums[i], nums[j], nums[l], nums[r]}));
                    while(l + 1 < r && nums[l] == nums[l + 1]) l ++ ;
                    while(l < r - 1 && nums[r] == nums[r - 1]) r --;
                    l ++ ; r -- ;
                } else if(sum > target) r --;
                else l ++ ;
            }
        }
    }

    return res;
}
```

哈希表法：

```java
class Solution {
    HashSet<List<Integer>> res = new HashSet<List<Integer>>();

    // 两数之和O(n):给定一个整数数组 nums 和一个整数目标值 target，在该数组中找出 和为目标值 target 的那两个整数
    public void twoSum(int[] nums, int index1, int index2, long target) { 
        HashMap<Long, Integer> map = new HashMap<>();

        for(int i = 0; i < nums.length; i ++ ) {
            if(i == index1 || i == index2) continue;
            if(map.containsKey((long)nums[i])) {
                Integer[] arr = new Integer[]{nums[i], nums[map.get((long)nums[i])], nums[index1], nums[index2]};
                Arrays.sort(arr);
                res.add(Arrays.asList(arr));
            } else map.put(target - nums[i], i);
        }
    }

    public void threeSum(int[] nums, int index, long target ) {
        HashMap<Integer, Boolean> map = new HashMap<>(); // 去重
        for(int i = 0; i < nums.length; i ++ ) {
            if(i == index) continue;
            if(!map.getOrDefault(nums[i], false)) { // 判断是否重复处理过
                twoSum(nums, i, index, target - nums[i]);
                map.put(nums[i], true);
            }
        }
    }

    public List<List<Integer>> fourSum(int[] nums, int target) {
        HashMap<Integer, Boolean> map = new HashMap<>(); // 去重
        for(int i = 0; i < nums.length; i ++ )
            if(!map.getOrDefault(nums[i], false)) { // 判断是否重复处理过
                threeSum(nums, i, (long)target - nums[i]);
                map.put(nums[i], true);
            }
        return new ArrayList<>(res);
    }
}
```



#### （9）[454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)（哈希表）

**问题**：

给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：

- `0 <= i, j, k, l < n`
- `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

例子：

```
输入：nums1 = [1,2], nums2 = [-2,-1], nums3 = [-1,2], nums4 = [0,2]
输出：2
解释：
两个元组如下：
1. (0, 0, 0, 1) -> nums1[0] + nums2[0] + nums3[0] + nums4[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> nums1[1] + nums2[1] + nums3[0] + nums4[0] = 2 + (-1) + (-1) + 0 = 0
```

 **数据范围**：

- `n == nums1.length`
- `n == nums2.length`
- `n == nums3.length`
- `n == nums4.length`
- `1 <= n <= 200`
- `-228 <= nums1[i], nums2[i], nums3[i], nums4[i] <= 228`

**思路**：哈希表计数 $O(n^2)$

- 上来就是4重循环，$O(n^4)$，复杂度太高了
- 用 map 缓存两个数组的和$O(n^2)$，两重循环
- 最后要计算元组个数，map 的 key 为两个数组的和，val 为和出现的次数

```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        Arrays.sort(nums1); Arrays.sort(nums2);
        Arrays.sort(nums3); Arrays.sort(nums4);
        
        Map<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i < nums1.length; i ++ ) {
            for(int j = 0; j < nums2.length; j ++ ) {
                int k = -(nums1[i] + nums2[j]);
                map.put(k, map.getOrDefault(k, 0) + 1);
            }
        }

        int res = 0;
        for(int i = 0; i < nums3.length; i ++ )
            for(int j = 0; j < nums4.length; j ++ ) {
                int k = (nums3[i] + nums4[j]);
                res += map.getOrDefault(k, 0);
            }
                
        return res;
    }
}
```



#### （10）[16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)（排序+双指针）

**问题**：

给你一个长度为 `n` 的整数数组 `nums` 和 一个目标值 `target`。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。

返回这三个数的和。

假定每组输入只存在恰好一个解。

例子：

```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

**数据范围**：

- `3 <= nums.length <= 1000`
- `-1000 <= nums[i] <= 1000`
- `-104 <= target <= 104`

**思路**：**排序**和**双指针** $O(n^2)$

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int n = nums.length;
        Arrays.sort(nums);

        int res = 0x3f3f3f3f;
        for(int i = 0; i < n - 2; i ++ ) {
            if(i > 0 && nums[i] == nums[ i - 1]) continue; // 去重

            for(int l = i + 1, r = n - 1; l < r;) {
                int sum = nums[i] + nums[l] + nums[r];
                if(Math.abs(sum - target) < Math.abs(res - target)) res = sum;

                if(sum == target) return sum ;
                else if(sum > target) r --;
                else l ++ ;
            }
        }
        return res;
    }
}
```

#### （11）[287. 寻找重复数](https://leetcode.cn/problems/find-the-duplicate-number/) （快慢指针）

**问题**：

给定一个包含 `n + 1` 个整数的数组 `nums` ，其数字都在 `[1, n]` 范围内（包括 `1` 和 `n`），可知至少存在一个重复的整数。

假设 `nums` 只有 **一个重复的整数** ，返回 **这个重复的数** 。

你设计的解决方案必须 **不修改** 数组 `nums` 且只用常量级 `O(1)` 的额外空间。

例子：

```
输入：nums = [1,3,4,2,2]
输出：2
```

**数据范围**：

- `1 <= n <= 105`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`
- `nums` 中 **只有一个整数** 出现 **两次或多次** ，其余整数均只出现 **一次**

**思路**：

如何证明 `nums` 中至少存在一个重复的数字?

依据**鸽洞原理**，有 $n$ 个鸽子洞，有 $n$ + 1 只鸽子，必然有一个鸽子洞中有两只鸽子。同理，长度为 $n+1$的数组，其数字范围都在 $[1,n]$范围内，至少存在一个数是重复的。

$nums$中**至少存在一个重复**的数字，假设$nums$中**只有一个重复**的整数，且**这个重复的整数出现两次或两次以上**，其余整数均只出现一次。**将该数组转换成链表，如何证明链表中一定存在环**？

以 $[1,3,4,2,2]$为例，按照一下映射规则将数组转换成链表：

$0->1$

$1->3$

$2->4$

$3->2$

$4->2$

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/%E6%88%AA%E5%B1%8F2023-06-19%2015.14.40.png" alt="截屏2023-06-19 15.14.40" style="zoom:50%;" />

链表中的重复节点必然至少有一条出边，两条入边。该节点必然是构成环的入口节点。

因此，可以采用**快慢指针**来判断链表是否有环，并确定环的入口节点。

**判断链表是否有环**

- 设置快慢指针分别为`fast`和`slow`，初始时都指向链表头`head`
- `slow`每次走一步`slow=slow.next`；`fast`每次走两步`fast=fast.next.next`
- 由于`fast`比`slow`走的快，如果有环，那么`fast`一定先进入环，而`slow`后进入环
- 两个指针进入环后，经过若干操作后**两个指针定能在环上相遇**，即可判断一个链表是有环。

​		**证明**：当`slow`进入环时，`fast`早已进入环。**因为`fast`每次比`slow`多走一步且`fast`与`slow`的距离小于环的长度，所以`fast`与`slow`相遇时，`slow`所走的距离不超过环的长度**。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202012148680.png" alt="image-20220201214811569" width="220px" />

**求环的入口点**

- 设置两个指针，一个指向`head`，一个指向相遇点
- 两个指针同步移动，均为一次走一步，再次相遇点即为环的入口点

​	**证明**：设头结点到环的入口点的距离为 $a$，环的入口点沿着环的方向到相遇点的距离为 $x$，环长为 $r$，相遇时 `fast` 绕过了 $n$ 圈。则有 $2(a+x)=a+nr+x$，即$a=nr-x$。从头结点到环的入口点的距离等于 $n$ 倍的环长减去环的入口点到相遇点的距离，因此可以用上述方法求入口点。

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202202012152550.png" alt="image-20220201215222499" width="180" />

类似的题目：[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)  [141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/) 

```java
class Solution {
    public int findDuplicate(int[] nums) {
        // 快慢指针从起点出发，慢指针走一步，快指针走两步，它们最终相遇，则一定有环
        int s = 0, f = 0;
        while((s == 0 && f == 0) || s != f) { // 如果有环，快指针和慢指针最终会相遇
            s = nums[s]; // 慢指针走一步
            f = nums[f]; f = nums[f]; // 快指针走两步
        }
        // 慢指针从起点出发，快指针从上次相遇点出发，它们同时走一步，再次相遇点为环的入口
        s = 0;
        while(s != f) {
            s = nums[s];
            f = nums[f];
        }
        return s;
    }
}
```

#### （12）[795. 区间子数组个数](https://leetcode.cn/problems/number-of-subarrays-with-bounded-maximum/)（双指针）

**问题**

给你一个整数数组 `nums` 和两个整数：`left` 及 `right` 。找出 `nums` 中连续、非空且其中最大元素在范围 `[left, right]` 内的子数组，并返回满足条件的子数组的个数。

生成的测试用例保证结果符合 **32-bit** 整数范围。

例子

```
输入：nums = [2,1,4,3], left = 2, right = 3
输出：3
解释：满足条件的三个子数组：[2], [2, 1], [3]
```

**数据范围**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^9`
- `0 <= left <= right <= 10^9`

**思路**

初始化指针$i=-1$和指针 $j=0$，指针$j$ 每次自增$1$。

设  $t$表示以$nums[j]$结尾的子数组的集合，这些子数组其最大元素在范围 $[left, right]$ 内。 $t$ 的属性是子数组的数量。

- 如果枚举$nums[j]$到比$right$大，则更新$i$为$j$。
- 如果$nums[j]$小于$left$，则以$nums[j]$结尾的子数组的集合，这些子数组其最大元素在范围 $[left, right]$ 内，**子数组的个数等于上一轮的$t$；**
- 如果$nums[j]$大于等于$left$，则更新 $t = j - i$。

```java
class Solution {
    public int numSubarrayBoundedMax(int[] nums, int left, int right) {
        int n = nums.length;
        // t表示以nums[j]结尾的子数组的集合，这些子数组其最大元素在范围 [left, right] 内。
        // t的属性是子数组的数量
        int res = 0, t = 0;
        for(int i = -1, j = 0; j < n; j ++) { // i 必须初始化-1
            if(nums[j] > right) i = j; // 如果枚举到比right大的数，则更新i为j
            if(nums[j] >= left) t = j - i; // 否则，t = j - i
            // 如果nums[j]小于left,则以nums[j]结尾的子数组的集合，这些子数组其最大元素在范围 [left, right] 内，子数组的个数等于上一轮的t
            res += t;
        }
        return res;
    }
}
```

#### （13）[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/) :star2:

**问题**

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

例子

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**数据范围**

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

**思路**

双指针算法， 时间复杂度是$O(n)$。

一开始两个指针一个指向开头一个指向结尾，**此时容器的底是最大的**，接下来随着指针向内移动，会造成容器的底变小，在这种情况下想要让容器盛水变多，就只有在容器的高上下功夫。

 那我们该如何决策哪个指针移动呢？我们能够发现不管是左指针向右移动一位，还是右指针向左移动一位，容器的底都是一样的，都比原来减少了 1。这种情况下我们想要让指针移动后的容器面积增大，**就要使移动后的容器的高尽量大**，所以我们选择指针所指的高较小的那个指针进行移动，这样我们就保留了容器较高的那条边，放弃了较小的那条边，以获得有更高的边的机会。

使用两个指针，值小的指针向内移动，这样就减小了搜索空间 **因为面积取决于指针的距离与值小的值乘积，如果值大的值向内移动，距离一定减小，而求面积的另外一个乘数一定小于等于值小的值，因此面积一定减小**，而我们要求最大的面积，因此值大的指针不动，而值小的指针向内移动遍历。

```java
class Solution {
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1; // 双指针算法
        int area = Math.min(height[l], height[r])*(r - l);
        // 短的线向里走
        while(l < r) {
            if(height[l] < height[r]) l ++ ;
            else r -- ;
            area = Math.max(area, Math.min(height[l], height[r])*(r - l));
        }
        return area;
    }
}
```

#### （14）[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/):star2:

**问题**

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

例子

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png" alt="img" style="zoom:75%;" />

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

**数据范围**

- `n == height.length`
- `1 <= n <= 2 * 10^4`
- `0 <= height[i] <= 10^5`

**思路**

双指针和容斥原理

<img src="https://assets.leetcode-cn.com/solution-static/42/1.png" alt="fig1" style="zoom:48%;" />

初始化两个指针，第一个指针$l$指向数组的起始位置并向右移动，第二个指针$r$指向数组的结束位置并向左移动。

指针$l$ 向右移动的过程中不断更新当前最大值 $leftmax$，并统计上述左上图的面积$leftsum$。

指针$r$ 向左移动的过程中不断更新当前最大值 $rightmax$，并统计上述右上图的面积$rightsum$。

设数组长度为 $n$ ，则上述中下图的面积等于 $maxheight * n$。设数组所有元素之和为 $sum$。

则接水量等于 $leftsum+rightsum-maxheight*n-sum$。

```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int leftmax = -1, rightmax = -1, leftsum = 0, rightsum = 0, sum = 0;
        for(int l = 0, r = n - 1; l < n && r >= 0; l ++, r --) {
            leftmax = Math.max(leftmax, height[l]);
            rightmax = Math.max(rightmax, height[r]);

            leftsum += leftmax; rightsum += rightmax;
            sum += height[l];
        }

        return leftsum + rightsum - leftmax * n - sum;
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
