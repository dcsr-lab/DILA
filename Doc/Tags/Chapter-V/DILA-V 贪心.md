# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>

> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第五部分之贪心



### 1、贪心

#### （1）1403. 非递增顺序的最小子序列

**问题**

给你一个数组 `nums`，请你从中抽取一个子序列，满足该子序列的元素之和 **严格** 大于未包含在该子序列中的各元素之和。

如果存在多个解决方案，只需返回 **长度最小** 的子序列。如果仍然有多个解决方案，则返回 **元素之和最大** 的子序列。

与子数组不同的地方在于，「数组的子序列」**不强调**元素在原数组中的**连续性**，也就是说，它可以通过从数组中分离一些（也可能不分离）元素得到。

注意，题目数据保证满足所有约束条件的解决方案是 **唯一** 的。同时，返回的答案应当按 **非递增顺序** 排列。

**思路** 

**贪心**：将所有元素**从大到小**排序，每次取**值最大的数**加入子序列，当子序列的元素之和 **严格** 大于未包含在该子序列中的各元素之和时，即求得**非递增顺序的最小子序列**

```java
class Solution {
    public List<Integer> minSubsequence(int[] nums) {
        // 大根堆
        PriorityQueue<Integer> heap = new PriorityQueue<>((o1,o2) -> -Integer.compare(o1, o2));
        
        int totalsum = 0; // 所有元素的和
        for(int i = 0; i < nums.length; i ++ ) { // 构建堆
            heap.offer(nums[i]);
            totalsum += nums[i];
        }

        List<Integer> res = new ArrayList<>(); // 存储 非递增顺序的最小子序列
        int partsum = 0; // 非递增顺序的最小子序列的和

        // 贪心, 每次取数值最大的数加入res
        while(partsum <= totalsum - partsum) { // 子序列的元素之和严格大于未包含在该子序列中的各元素之和
            int t = heap.poll();
            res.add(t);
            partsum += t;
        }

        return res;
    }
}
```

#### （2）[LCP 33. 蓄水](https://leetcode.cn/problems/o8SXZn/)（贪心+堆）

**问题**：

给定 N 个无限容量且初始均空的水缸，每个水缸配有一个水桶用来打水，第 `i` 个水缸配备的水桶容量记作 `bucket[i]`。小扣有以下两种操作：

- 升级水桶：选择任意一个水桶，使其容量增加为 `bucket[i]+1`。
- 蓄水：将全部水桶接满水，倒入各自对应的水缸。

每个水缸对应最低蓄水量记作 `vat[i]`，返回小扣至少需要多少次操作可以完成所有水缸蓄水要求。

注意：实际蓄水量 **达到或超过** 最低蓄水量，即完成蓄水要求。

```
例子：

输入：bucket = [9,0,1], vat = [0,2,2]

输出：3

解释：
第 1 次操作均选择升级 bucket[1]
第 2~3 次操作选择蓄水，即可完成蓄水要求。
```

**数据范围：**

- `1 <= bucket.length == vat.length <= 100`
- `0 <= bucket[i], vat[i] <= 10^4`

**思路**：**贪心和优先队列**

[蓄水 - 蓄水 - 力扣（LeetCode）](https://leetcode.cn/problems/o8SXZn/solution/xu-shui-by-leetcode-solution-g4lx/)

贪心的做法：

- 用「最大堆」（「优先队列」）来维护一对水缸，对应的水桶以及所需的蓄水操作数。蓄水操作数大的排在堆顶。

- 每次尝试进行减小总的操作次数——从「最大堆」中取出需要「蓄水」操作最多的水桶，并进行「升级水桶」操作使得其需要的「蓄水」操作至少减少 
  1，然后再次放入「最大堆」中，并更新当前需要的总的操作次数最小值，直到当「升级水桶」的操作次数已经不能再减少总的操作次数为止：
  - 「升级水桶」的次数已经大于等于当前已得的总操作次数最小值；
  - 此时需要的「蓄水」操作等于 1；

- 一直升级水桶。理论升级最大次数：将所有的桶升级到缸一样，也就是SumVat-SumBucket+1 次（但是注意排除vat为0的，不需要加入sumVat中）； 然后一次倒入，但是这种情况不可能达到。
- 升级停止条件：当最大升级次数大于等于last最小操作次数时，last不可能再继续减小了，所以停止迭代。

```java
class Storage {
    int bucket; // 水桶容量
    int vat; // 水缸对应最低蓄水量
    int op; // 蓄水操作

    Storage(int bucket, int vat) {
        this.bucket = bucket;
        this.vat = vat;
        if( vat != 0 ) this.op = (int)Math.ceil((double)vat / bucket);
        else this.op = 0;
    }

    public void upgrade() {
        this.bucket += 1;
        this.op = (int)Math.ceil((double)vat / bucket);
    }
}

class Solution {
    public int storeWater(int[] bucket, int[] vat) {
        // 大根堆
        PriorityQueue<Storage> heap = new PriorityQueue<>((o1, o2) -> -Integer.compare(o1.op, o2.op));

        int upgrade = 0;
        for( int i = 0; i < bucket.length; i ++ ) {
            if(bucket[i] == 0 && vat[i] != 0) { // 水桶为容量为0, 水缸对应最低蓄水量 > 0, 则一定需要升级水桶
                upgrade ++;
                bucket[i] += 1;
            } 
            heap.offer(new Storage(bucket[i], vat[i]));
        }
        // 贪心的做法：一直升级水桶。理论升级最大次数：将所有的桶升级到缸一样，也就是SumVat-SumBucket+1 次（但是注意排除vat为0的，不需要加入sumVat中）； 然后一次倒入，但是这种情况不可能达到。
        int last = Integer.MAX_VALUE;
        while (upgrade < last) { // 所以当最大升级次数大于等于last最小操作次数时，last不可能再继续减小了，所以停止迭代
            Storage s = heap.poll();
            last = Math.min(last, upgrade + s.op);
            if(s.op == 1) break;
        
            s.upgrade();
            upgrade ++ ;
            heap.offer(s);
            // System.out.println(s.bucket + ", " + s.vat + ", " + upgrade + ", " + s.op);
        }

        return last;
    }
}
```

#### （3）[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

**问题：**

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

例子：

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```java
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**数据范围**：

- `n == height.length`
- `2 <= n <= 105`
- `0 <= height[i] <= 104`

**思路**：**贪心+双指针**

- **设置两个指针**，一个指针指向数组的左端，另一个指针指向数组的右端；
- **短线的指针向里移动**，<u>以该短线为端点的所有区间的储水量都小于等于当前的出水量</u>；记录当前值，相当于以该短线为端点的所有区间都被考虑过了。
- 记录所有储水量的最大值。

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

#### （4）[1090. 受标签影响的最大值](https://leetcode.cn/problems/largest-values-from-labels/)

**问题**：

我们有一个 `n` 项的集合。给出两个整数数组 `values` 和 `labels` ，第 `i` 个元素的值和标签分别是 `values[i]` 和 `labels[i]`。还会给出两个整数 `numWanted` 和 `useLimit` 。

从 `n` 个元素中选择一个子集 `s` :

- 子集 `s` 的大小 **小于或等于** `numWanted` 。
- `s` 中 **最多** 有相同标签的 `useLimit` 项。

一个子集的 **分数** 是该子集的值之和。

返回子集 `s` 的最大 **分数** 。

例子：

```
输入：values = [5,4,3,2,1], labels = [1,1,2,2,3], numWanted = 3, useLimit = 1
输出：9
解释：选出的子集是第一项，第三项和第五项。
```

**数据范围**：

- `n == values.length == labels.length`
- `1 <= n <= 2 * 104`
- `0 <= values[i], labels[i] <= 2 * 104`
- `1 <= numWanted, useLimit <= n`

**思路**：

贪心思想：在总数和相同标签出现的次数限制下，我们应该**优先选择值最大的数**。可以考虑先对数组进行从大倒序排序，在进行贪心选择。

```java
class Solution {

    public void swap(int[] values, int[] labels, int i, int j) {
        int temp = values[i];
        values[i] = values[j];
        values[j] = temp;

        temp = labels[i];
        labels[i] = labels[j];
        labels[j] = temp;
    }

    public void quicksort(int[] values, int[] labels, int l, int r) {
        if (l >= r ) return ; // 只有一个节点或者没有节点

        // 确定中点
        int mid = (l + r) >> 1;
        int x = values[ mid ], i = l - 1, j = r + 1;
        // 双指针算法, 直到两个指针相遇,使得左边所有的数都小于中点右边的数
        while(i < j) {
            do {
                i ++ ;
            } while(values[i] > x); // 从左往右, 找到左边第一个小于等于x的数
            do {
                j --;
            } while(values[j] < x); // 从右往左, 找到右边第一个大于等于x的数
            if(i < j) swap(values, labels, i, j); // 交换i和j位置的两个数
        }

        // 递归分别排序左右两个部分
        quicksort(values, labels, l, j);
        quicksort(values, labels, j + 1, r);
    }

    public int largestValsFromLabels(int[] values, int[] labels, int numWanted, int useLimit) {
        int n = values.length;
        quicksort(values, labels, 0, n - 1); // 倒序

        int count = 0, score = 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i ++ ) {
            if(count < numWanted && map.getOrDefault(labels[i], 0) < useLimit) {
                score += values[i];
                count ++ ;
                map.put(labels[i], map.getOrDefault(labels[i],0) + 1);
            }
        }
        return score;
    }
}
```

#### （5）[179. 最大数](https://leetcode.cn/problems/largest-number/)

**问题**：

给定一组非负整数 `nums`，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。**注意：**输出结果可能非常大，所以你需要返回一个字符串而不是整数。

**例子**：

```
输入：nums = [3,30,34,5,9]
输出："9534330"
```

**数据范围**：

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 109`

**思路**：

重新定义排序规则：

- 贪心选择，首位数字**大**的排在前面；
- 如果首位数字相同, 两种字符串组合按字典序比较 a + b > b + a ?

```java
class Solution {
    public String largestNumber(int[] nums) {
        ArrayList<Integer> arr = new ArrayList<>();
        for(int i = 0; i < nums.length; i ++) arr.add(nums[i]);

        Collections.sort(arr, (o1, o2) -> { // 定义排序规则
            String a = o1.toString(), b = o2.toString();
            if(a.charAt(0) == b.charAt(0)) { // 如果首位数字相同, 两种字符串组合按字典序比较 a + b > b + a ?
                String s1 = a + b;
                String s2 = b + a;
                return - s1.compareTo(s2);
            }
            return - a.substring(0, 1).compareTo(b.substring(0, 1)); // 规则一: 贪心选择，首位数字大的排在前面
        });

        StringBuilder res = new StringBuilder();
        boolean flag = false;
        for(int i = 0; i < arr.size(); i ++) {
            int x = arr.get(i);
            if (x != 0) flag = true;
            if(flag) res.append(x); // 去除前导零
        }
        return res.toString().equals("") ? "0" : res.toString();
    }
}
```

#### （6）[剑指 Offer 45. 把数组排成最小的数](https://leetcode.cn/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

**问题**：

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的**所有数字中最小**的一个。

例子：

```
输入: [3,30,34,5,9]
输出: "3033459"
```

**数据范围**：

- `0 < nums.length <= 100`
- 输出结果可能非常大，所以你需要返回一个字符串而不是整数
- 拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

**思路**：

重新定义排序规则：

- 贪心选择，首位数字**小**的排在前面；
- 如果首位数字相同, 两种字符串组合按字典序比较 a + b > b + a ?

```java
class Solution {
    // 自定义排序规则
    public String minNumber(int[] nums) {
        List<Integer> arr = new ArrayList<Integer>();
        for(int i = 0; i < nums.length; i ++ )
            arr.add(nums[i]);

        Collections.sort(arr, (a, b) -> {
            String x = String.valueOf(a);
            String y = String.valueOf(b);
            if (x.charAt(0) == y.charAt(0)) return (x + y).compareTo(y + x);
            return x.charAt(0) - y.charAt(0) > 0 ? 1 : -1;
        });

        StringBuilder builder = new StringBuilder();
        for(int i = 0; i < arr.size(); i ++ )
            builder.append(arr.get(i));

        return builder.toString();
    }
}
```



#### （7）[55. 跳跃游戏](https://leetcode.cn/problems/jump-game/)

**问题**：

给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

例子：

```java
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

**数据范围**：

- `1 <= nums.length <= 3 * 104`
- `0 <= nums[i] <= 105`

**思路**：

贪心思想：在当前能跳到的范围内，寻找接下来能够跳到最远的位置作为下一个跳跃点。

- 如果某一个作为 起跳点 的格子可以跳跃的距离是 3，那么表示后面 3 个格子都可以作为 起跳点;

- 可以对每一个能作为 起跳点 的格子都尝试跳一次，把 能跳到最远的距离 不断更新;

- 如果可以一直跳到最后，就成功了.

  

```java
class Solution {
   public boolean canJump(int[] nums) {
       if(nums.length == 1) return true; // 特判
       int i = 0;
       while (i < nums.length) {
           int jump = i + nums[i], index = i; // 当前的起跳位置
           for(int j = i + 1; j <= i + nums[i]; j ++ ) { // 在index:i~i + nums[i],都可以作为起跳点
               if(j >= nums.length - 1) return true; // 可以跳到最后一个位置
               if(j + nums[j] >= jump) { // 在这些起跳点中找到可以跳的最远的位置
                   jump = j + nums[j];
                   index = j;
               }
           }
           i = index; // 更新下一个起跳位置
           if (nums[i] == 0 && i != nums.length - 1) return false; // 此为止不是最后一个位置且跳跃长度
       }
       return true;
   }
}
```

#### （8）[2611. 老鼠和奶酪](https://leetcode.cn/problems/mice-and-cheese/)（贪心+堆）

**问题**：

有两只老鼠和 `n` 块不同类型的奶酪，每块奶酪都只能被其中一只老鼠吃掉。

下标为 `i` 处的奶酪被吃掉的得分为：

- 如果第一只老鼠吃掉，则得分为 `reward1[i]` 。
- 如果第二只老鼠吃掉，则得分为 `reward2[i]` 。

给你一个正整数数组 `reward1` ，一个正整数数组 `reward2` ，和一个非负整数 `k` 。请你返回第一只老鼠恰好吃掉 `k` 块奶酪的情况下，**最大** 得分为多少。

例子：

```
输入：reward1 = [1,1,3,4], reward2 = [4,4,1,1], k = 2
输出：15
解释：这个例子中，第一只老鼠吃掉第 2 和 3 块奶酪（下标从 0 开始），第二只老鼠吃掉第 0 和 1 块奶酪。
总得分为 4 + 4 + 3 + 4 = 15 。
15 是最高得分。
```

**数据范围**：

- `1 <= n == reward1.length == reward2.length <= 105`

- `1 <= reward1[i], reward2[i] <= 1000`
- `0 <= k <= n`

**思路**：

- 用一个变量 $sum$ 维护了$reward2$所有元素之和
- 用大根堆维护 $reward1[i]-reward2[i]$。
- 用 $sum$ 加上$k$个最大的$reward1[i]-reward2[i] $。

```java
class Solution {
    public int miceAndCheese(int[] reward1, int[] reward2, int k) {
        int n = reward1.length, sum = 0; // sum 维护了reward所有元素之和
        PriorityQueue<Integer> heap = new PriorityQueue<>((a, b) -> -a.compareTo(b)); // 维护reward1[i]-reward2[i]
        for(int i = 0; i < n ; i ++ ) {
            sum += reward2[i];
            heap.offer(reward1[i] - reward2[i]);
        }

        while(k -- != 0) sum += heap.poll(); // 选出k个最大的reward1[i]-reward2[i]
        return sum;
    }
}
```

#### （9）[LCP 30. 魔塔游戏](https://leetcode.cn/problems/p0NxJO/)（贪心+堆）

**问题**：

小扣当前位于魔塔游戏第一层，共有 `N` 个房间，编号为 `0 ~ N-1`。每个房间的补血道具/怪物对于血量影响记于数组 `nums`，其中正数表示道具补血数值，即血量增加对应数值；负数表示怪物造成伤害值，即血量减少对应数值；`0` 表示房间对血量无影响。

**小扣初始血量为 1，且无上限**。假定小扣原计划按房间编号升序访问所有房间补血/打怪，**为保证血量始终为正值**，小扣需对房间访问顺序进行调整，**每次仅能将一个怪物房间（负数的房间）调整至访问顺序末尾**。请返回小扣最少需要调整几次，才能顺利访问所有房间。若调整顺序也无法访问完全部房间，请返回 -1。

例子：

```
输入：nums = [100,100,100,-250,-60,-140,-50,-50,100,150]

输出：1

解释：初始血量为 1。至少需要将 nums[3] 调整至访问顺序末尾以满足要求。
```

**数据范围**：

- `1 <= nums.length <= 10^5`
- `-10^5 <= nums[i] <= 10^5`

**思路**：

用小根堆$heap$维护所有访问过的房间，其堆顶元素表示当前访问过的最小补血数值/最大伤害值。用一个$sum$表示当前的血量。

当当前血量为负数，就将堆顶元素出堆，即将该房间调整至访问顺序末尾。

所以贪心的思想就是当当前血量为负数时，尽可能的将访问过的伤害值最大的房间调整值末尾。

时间复杂度是 $O(n)$。

```java
class Solution {
    public int magicTower(int[] nums) {
        long sum = 0;
        int cnt = 0;
        for(int i = 0; i < nums.length; i ++ ) sum += nums[i];
        if(sum < 0) return -1;

        var heap = new PriorityQueue<Integer>();
        sum = 0;
        for(int i = 0; i < nums.length; i ++) {
            sum += nums[i];
            heap.offer(nums[i]);

            while(sum < 0) {
                int x = heap.poll();
                sum -= x;
                cnt ++ ;
            }
        }
        return cnt;
    }
}
```

#### （10）[2178. 拆分成最多数目的正偶数之和](https://leetcode.cn/problems/maximum-split-of-positive-even-integers/) 

**问题**：

给你一个整数 `finalSum` 。请你将它拆分成若干个 **互不相同** 的正偶数之和，且拆分出来的正偶数数目 **最多** 。

比方说，给你 `finalSum = 12` ，那么这些拆分是 **符合要求** 的（互不相同的正偶数且和为 `finalSum`）：`(2 + 10)` ，`(2 + 4 + 6)` 和 `(4 + 8)` 。它们中，`(2 + 4 + 6)` 包含最多数目的整数。注意 `finalSum` 不能拆分成 `(2 + 2 + 4 + 4)` ，因为拆分出来的整数必须互不相同。

请你返回一个整数数组，表示将整数拆分成 **最多** 数目的正偶数数组。如果没有办法将 `finalSum` 进行拆分，请你返回一个 **空** 数组。你可以按 **任意** 顺序返回这些整数。

例子：

```
输入：finalSum = 12
输出：[2,4,6]
解释：以下是一些符合要求的拆分：(2 + 10)，(2 + 4 + 6) 和 (4 + 8) 。
(2 + 4 + 6) 为最多数目的整数，数目为 3 ，所以我们返回 [2,4,6] 。
[2,6,4] ，[6,2,4] 等等也都是可行的解。
```

**数据范围**：

- `1 <= finalSum <= 10^10`

**思路**：

- 首先判断`finalSum`是否是偶数，如果是奇数就直接返回空集合。
- 贪心地从最小的偶数2开始搜索，步长为2。设当前搜索的偶数为$k$，每次用 $finalSum -= k$。
- 如果最终的 $finalSum$在之前搜索过的偶数中已经出现了，那么将$finalSum$加到最后一个偶数上。

```java
class Solution {
    public List<Long> maximumEvenSplit(long finalSum) {
        if(finalSum % 2 == 1) return new ArrayList<>();
        long cnt = finalSum / 2;

        var res = new ArrayList<Long>();
        for(long i = 1; i <= cnt; i ++) {
            res.add(i * 2);
            cnt -= i;
        }
        res.set(res.size() - 1, res.get(res.size() - 1) + cnt * 2);
        return res;
    }
}
```





#### （11）[2934. 最大化数组末位元素的最少操作次数](https://leetcode.cn/problems/minimum-operations-to-maximize-last-elements-in-arrays/) :star:



#### （12）[2216. 美化数组的最少删除数](https://leetcode.cn/problems/minimum-deletions-to-make-array-beautiful/)



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
