# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之链表

### 1、链表 

#### （1）82. 删除排序链表中的重复元素 II 

给定一个已排序的链表的头 `head` ， *删除原始链表中所有重复数字的节点，只留下不同的数字* 。返回 *已排序的链表* 。

例1：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202209062358031.jpeg" alt="img" width="500px" />

例2：

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202209062358007.jpeg" alt="img" width="350px"  />

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null) return head; // 特判
        // pre 是 cur的前继结点, post是cur的后继结点, h 指向头结点
        ListNode pre = new ListNode(0x3f3f3f3f, head), cur = head, post = head.next, h = pre;
        while(cur != null && post != null) {
            int cnt = 0;
            while(post != null && cur.val == post.val) { // 找到cur之后第一个与cur.val值不相同的结点
                post = post.next;
                cnt ++ ;
            }

            if(cnt > 0)  pre.next = post; // 存在重复结, 删除所有重复结点
            else pre = cur; // 不存在重复结点
            cur = post;
            if(post != null) post = post.next;
        }

        return h.next;
    }
}
```



#### （2）[24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)（递归）

**问题**

给你一个链表，两两**交换其中相邻的节点**，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg" alt="img" style="zoom:80%;" />

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**数据范围** 

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {

    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) return head;

        ListNode res = head.next;
        head.next = swapPairs(res.next);
        res.next = head;
        
        return res;
    }
}
```

#### （3）[25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/) （递归）

**问题**

给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表。

`k` 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]
```

**数据范围**

- 链表中的节点数目为 `n`
- `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if(head == null) return head;
        ListNode cur = head;
        int cnt = k;
        while((cnt --) != 0) {
            cur = cur.next;
            if(cur == null && cnt != 0) return head; // 将最后剩余的节点保持原有顺序
        }

        ListNode p = null, q = head;
        cnt = k;
        while((cnt --) != 0) {
            ListNode t = q.next; // 记录q的next节点
            q.next = p; // 将q的next指向q的前一个节点p
            p = q;
            q = t;
        }
        head.next = reverseKGroup(cur, k);
        return p;
    }
}
```

#### （4）[189. 轮转数组](https://leetcode.cn/problems/rotate-array/) （翻转）

**问题**

给定一个整数数组 `nums`，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。

例子

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]
```

**数据范围**

- `1 <= nums.length <= 10^5`
- `-2^31 <= nums[i] <= 2^31 - 1`
- `0 <= k <= 10^5`

**思路**

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

#### （5）[61. 旋转链表](https://leetcode.cn/problems/rotate-list/) （翻转）

**问题**

给你一个链表的头节点 `head` ，旋转链表，将链表每个节点向右移动 `k` 个位置。

例子

<img src="https://assets.leetcode.com/uploads/2020/11/13/rotate1.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,2,3,4,5], k = 2
输出：[4,5,1,2,3]
```

**数据范围**

- 链表中节点的数目在范围 `[0, 500]` 内
- `-100 <= Node.val <= 100`
- `0 <= k <= 2 * 10^9`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {

    public ListNode reverse(ListNode p, ListNode q, int k) {
        while(q != null && (k --) != 0) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }
        return p;
    }

    public ListNode rotateRight(ListNode head, int k) {  
        if(head == null || k == 0 || head.next == null) return head;

        int cnt = 1;
        ListNode t = head;
        while((t = t.next) != null) cnt ++;
        if(k % cnt == 0) return head;

        head = reverse(null, head, 0x3f3f3f3f);

        ListNode second = head;
        for(int i = 1; i <= k % cnt; i ++) second = second.next;

        second = reverse(null, second, 0x3f3f3f3f);

        ListNode first = head;
        head = reverse(null, head, k % cnt);
        
        first.next = second;
        return head;
    }
}
```

#### （6）[725. 分隔链表](https://leetcode.cn/problems/split-linked-list-in-parts/) （模拟）

**问题**

给你一个头结点为 `head` 的单链表和一个整数 `k` ，请你设计一个算法将链表分隔为 `k` 个连续的部分。

**每部分的长度应该尽可能的相等：任意两部分的长度差距不能超过 1 。**这可能会导致有些部分为 null 。

这 `k` 个部分应该按照在链表中出现的顺序排列，并且**排在前面的部分的长度应该大于或等于排在后面的长度**。

返回一个由上述 `k` 部分组成的数组。

例子

<img src="https://assets.leetcode.com/uploads/2021/06/13/split1-lc.jpg" alt="img" style="zoom:50%;" />

```
输入：head = [1,2,3], k = 5
输出：[[1],[2],[3],[],[]]
解释：
第一个元素 output[0] 为 output[0].val = 1 ，output[0].next = null 。
最后一个元素 output[4] 为 null ，但它作为 ListNode 的字符串表示是 [] 。
```

**数据范围**

- 链表中节点的数目在范围 `[0, 1000]`
- `0 <= Node.val <= 1000`
- `1 <= k <= 50`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode[] splitListToParts(ListNode head, int k) {
        if(head == null) return new ListNode[k];

        ListNode[] res = new ListNode[k];
        
        int cnt = 1;
        ListNode t = head;
        while((t = t.next) != null) cnt ++;

        if(cnt <= k) {
            int idx = 0;
            for(ListNode i = head; i != null;) {
                res[idx ++] = i;
                ListNode p = i.next;
                i.next = null;
                i = p;
            }
        } else {
            int each = cnt / k;
            ListNode p = null, q = head;
            for(int i = 0; i < k; i ++) {
                if(p != null) p.next = null;
                res[i] = q;
                int n = i < cnt - k * each ? each + 1 : each;
                for(int j = 0; j < n; j ++) {
                    p = q;
                    q = q.next;
                }
            }
        }
        return res;
    }
}

// cnt <= k
// cnt > k
// 每部分的节点个数 [cnt / k] 
// 前cnt - k * [cnt / k]个部分, 每个部分再增加一个节点
```

#### （7）[19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/) 

**问题**

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

例子

<img src="https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**数据范围**

- 链表中结点的数目为 `sz`
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode pre = null, i = head, j = head;
        int k = 1;

        while(i != null) {
            if(k > n) { // 当i走过第n个结点时, j开始走
                pre = j;
                j = j.next;
            }
            i = i.next;
            k ++ ;
        }

        if(pre != null) pre.next = j.next;
        else head = head.next; // 删除的是头结点
        return head;
    }
}
```

#### （8）[1171. 从链表中删去总和值为零的连续节点](https://leetcode.cn/problems/remove-zero-sum-consecutive-nodes-from-linked-list/) 

**问题** 

给你一个链表的头节点 `head`，请你编写代码，反复删去链表中由 **总和** 值为 `0` 的连续节点组成的序列，直到不存在这样的序列为止。

删除完毕后，请你返回最终结果链表的头节点。

例子

```
输入：head = [1,2,-3,3,1]
输出：[3,1]
提示：答案 [1,2,1] 也是正确的。
```

**数据范围**

- 给你的链表中可能有 `1` 到 `1000` 个节点。
- 对于链表中的每个节点，节点的值：`-1000 <= node.val <= 1000`.

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeZeroSumSublists(ListNode head) {
        List<Integer> arr = new ArrayList<>();
        arr.add(0x3f3f3f3f); // 站位
        for(ListNode i = head; i != null; i = i.next) arr.add(i.val); // 链表转换成数组, 下标从1开始

        int n  = arr.size();
        int[] s = new int[n]; // 前缀和, 下标从1开始
        for(int i = 1; i < n; i ++ )
            s[i] = s[i - 1] + arr.get(i);

        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i ++ ) {
            if(map.get(s[i]) == null) { 
                map.put(s[i], i); // 存储s[i]出现的下标i
                continue;
            }
            for(int j = map.get(s[i]) + 1; j < n && j <= i; j ++ ) {
                arr.set(j, 0x3f3f3f3f); // 删除和等于0的子数组
                map.remove(s[j]); // 将map中对应的元素去除
            }
            map.put(s[i], i);
        }

        ListNode h = new ListNode(), p = h; 
        for(int i = 1; i < arr.size(); i ++) {
            if(arr.get(i) == 0x3f3f3f3f) continue;
            p.next = new ListNode(arr.get(i));
            p = p.next;
        }
        return h.next;
    }
}
//   [1,2,-3,3,1]
// [0,1,3, 0,3,4]
```

#### （9）[328. 奇偶链表](https://leetcode.cn/problems/odd-even-linked-list/) 

**问题**

给定单链表的头节点 `head` ，将所有索引为奇数的节点和索引为偶数的节点分别组合在一起，然后返回重新排序的列表。

**第一个**节点的索引被认为是 **奇数** ， **第二个**节点的索引为 **偶数** ，以此类推。

请注意，偶数组和奇数组内部的相对顺序应该与输入时保持一致。

你必须在 `O(1)` 的额外空间复杂度和 `O(n)` 的时间复杂度下解决这个问题。

例子

<img src="https://assets.leetcode.com/uploads/2021/03/10/oddeven-linked-list.jpg" alt="img" style="zoom:80%;" />

```
输入: head = [1,2,3,4,5]
输出: [1,3,5,2,4]
```

**数据范围**

- `n == ` 链表中的节点数
- `0 <= n <= 10^4`
- `-10^6 <= Node.val <= 10^6`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if(head == null || head.next == null || head.next.next == null) return head;
        ListNode hodd = head, heven = head.next;
        ListNode t = head.next.next, p = hodd, q = heven;
        
        for(int i = 3; t != null; i ++) {
            if(i % 2 == 1) {
                p.next = t;
                p = p.next;
            } else {
                q.next = t;
                q = q.next;
            }
            t = t.next;
        }
        p.next = heven;
        q.next = null;
        return hodd;
    }
}
```

#### （10）[237. 删除链表中的节点](https://leetcode.cn/problems/delete-node-in-a-linked-list/):star2:

**问题**

有一个单链表的 `head`，我们想删除它其中的一个节点 `node`。

给你一个需要删除的节点 `node` 。**你将 无法访问 第一个节点 `head`。**

链表的所有值都是 **唯一的**，并且保证给定的节点 `node` 不是链表中的最后一个节点。

删除给定的节点。注意，删除节点并不是指从内存中删除它。这里的意思是：

- 给定节点的值不应该存在于链表中。
- 链表中的节点数应该减少 1。
- `node` 前面的所有值顺序相同。
- `node` 后面的所有值顺序相同。

**自定义测试：**

- 对于输入，你应该提供整个链表 `head` 和要给出的节点 `node`。`node` 不应该是链表的最后一个节点，而应该是链表中的一个实际节点。
- 我们将构建链表，并将节点传递给你的函数。
- 输出将是调用你函数后的整个链表。

例子

<img src="https://assets.leetcode.com/uploads/2020/09/01/node1.jpg" alt="img" style="zoom: 50%;" />

```
输入：head = [4,5,1,9], node = 5
输出：[4,1,9]
解释：指定链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9
```

**数据范围**

- 链表中节点的数目范围是 `[2, 1000]`
- `-1000 <= Node.val <= 1000`
- 链表中每个节点的值都是 **唯一** 的
- 需要删除的节点 `node` 是 **链表中的节点** ，且 **不是末尾节点**

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

#### （11）[147. 对链表进行插入排序](https://leetcode.cn/problems/insertion-sort-list/)

**问题**

给定单个链表的头 `head` ，使用 **插入排序** 对链表进行排序，并返回 *排序后链表的头* 。

**插入排序** 算法的步骤:

1. 插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
2. 每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
3. 重复直到所有输入数据插入完为止。

下面是插入排序算法的一个图形示例。部分排序的列表(黑色)最初只包含列表中的第一个元素。每次迭代时，从输入数据中删除一个元素(红色)，并就地插入已排序的列表中。

对链表进行插入排序。

例子

<img src="https://assets.leetcode.com/uploads/2021/03/04/sort1linked-list.jpg" alt="img" style="zoom:67%;" />

```
输入: head = [-1,5,3,4,0]
输出: [-1,0,3,4,5]
```

**数据范围**

- 列表中的节点数在 `[1, 5000]`范围内
- `-5000 <= Node.val <= 5000`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode insertionSortList(ListNode head) {
        ListNode visualh = new ListNode(-0x3f3f3f3f, null); // 虚拟头结点

        ListNode i = head;
        while(i != null) {
            ListNode j = visualh, t = i.next;
            while(j.next != null && j.next.val < i.val) j = j.next;
            i.next = j.next;
            j.next = i;

            i = t;
        }
        return visualh.next;
    }
}
```

#### （12）[148. 排序链表](https://leetcode.cn/problems/sort-list/)

**问题**

给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 **排序后的链表** 。

例子

<img src="https://assets.leetcode.com/uploads/2020/09/14/sort_list_1.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```

**数据范围**：

- 链表中节点的数目在范围 `[0, 5 * 10^4]` 内
- `-10^5 <= Node.val <= 10^5`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) return head; // 区间中只有一个结点，则返回此结点
        ListNode s = head, f = head; // 通过快慢指针找到链表的中点
        while(f.next != null && f.next.next != null) {
            s = s.next; f = f.next.next;
        }
        ListNode mid = s.next; s.next = null;
        ListNode i = sortList(head), j = sortList(mid); // 排序

        ListNode res = new ListNode(), k = res; // 归并
        while(i != null && j != null){
            if(i.val <= j.val) { k = k.next = i; i = i.next; }
            else { k = k.next = j; j = j.next; }
        }

        if(i != null) k.next = i; 
        if(j != null) k.next = j;
        return res.next;
    }
}
```

#### （13）[86. 分隔链表](https://leetcode.cn/problems/partition-list/) 

**问题**

给你一个链表的头节点 `head` 和一个特定值 `x` ，请你对链表进行分隔，使得所有 **小于** `x` 的节点都出现在 **大于或等于** `x` 的节点之前。

你应当 **保留** 两个分区中每个节点的初始相对位置。

例子

<img src="https://assets.leetcode.com/uploads/2021/01/04/partition.jpg" alt="img" style="zoom:67%;" />

```
输入：head = [1,4,3,2,5,2], x = 3
输出：[1,2,2,4,3,5]
```

**数据范围**

- 链表中节点的数目在范围 `[0, 200]` 内
- `-100 <= Node.val <= 100`
- `-200 <= x <= 200`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode ph = new ListNode(-0x3f3f3f3f, null); // 指向元素值小于x的节点构成链表
        ListNode qh = new ListNode(-0x3f3f3f3f, null); // 指向元素值大于x的节点构成链表

        ListNode p = ph, q = qh;
        for(ListNode i = head; i != null; i = i.next) {
            if(i.val < x) { // 将i分类到元素值小于x的节点构成链表
                p.next = i;
                p = i;
            } else { // 将i分类到元素值大于或等于x的节点构成链表
                q.next = i;
                q = i;
            }
        }
        p.next = qh.next; // 合并两个链表
        q.next = null; // 清空尾节点的next值
        return ph.next;
    }
}
```

#### （14）[2130. 链表最大孪生和](https://leetcode.cn/problems/maximum-twin-sum-of-a-linked-list/)

**问题**

在一个大小为 `n` 且 `n` 为 **偶数** 的链表中，对于 `0 <= i <= (n / 2) - 1` 的 `i` ，第 `i` 个节点（下标从 **0** 开始）的孪生节点为第 `(n-1-i)` 个节点 。

- 比方说，`n = 4` 那么节点 `0` 是节点 `3` 的孪生节点，节点 `1` 是节点 `2` 的孪生节点。这是长度为 `n = 4` 的链表中所有的孪生节点。

**孪生和** 定义为一个节点和它孪生节点两者值之和。

给你一个长度为偶数的链表的头节点 `head` ，请你返回链表的 **最大孪生和** 。

例子

<img src="https://assets.leetcode.com/uploads/2021/12/03/eg1drawio.png" alt="img" style="zoom:67%;" />

```java
输入：head = [5,4,2,1]
输出：6
解释：
节点 0 和节点 1 分别是节点 3 和 2 的孪生节点。孪生和都为 6 。
链表中没有其他孪生节点。
所以，链表的最大孪生和是 6 。
```

**数据范围**

- 链表的节点数目是 `[2, 10^5]` 中的 **偶数** 。
- `1 <= Node.val <= 10^5`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public int pairSum(ListNode head) {
        int cnt = 0;
        for(ListNode i = head; i != null; i = i.next) cnt ++;
        
        ListNode p = null, q = head;
        for(int i = 0; i < cnt / 2; i ++) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }

        int res = -0x3f3f3f3f;
        while(p != null && q != null) {
            res = Math.max(res, p.val + q.val);
            p = p.next;
            q = q.next;
        }
        return res;
    }
}
```

#### （15）[2487. 从链表中移除节点](https://leetcode.cn/problems/remove-nodes-from-linked-list/)（单减栈）

**问题**

给你一个链表的头节点 `head` 。

对于列表中的每个节点 `node` ，如果其右侧存在一个具有 **严格更大** 值的节点，则移除 `node` 。

返回修改后链表的头节点 `head` 。

例子

<img src="https://assets.leetcode.com/uploads/2022/10/02/drawio.png" alt="img" style="zoom: 67%;" />



```
输入：head = [5,2,13,3,8]
输出：[13,8]
解释：需要移除的节点是 5 ，2 和 3 。
- 节点 13 在节点 5 右侧。
- 节点 13 在节点 2 右侧。
- 节点 8 在节点 3 右侧。
```

**数据范围**

- 给定列表中的节点数目在范围 `[1, 10^5]` 内
- `1 <= Node.val <= 10^5`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    // 单减栈
    public ListNode removeNodes(ListNode head) {
        ArrayDeque<ListNode> stk = new ArrayDeque<>();

        ListNode vh = new ListNode(0x3f3f3f3f, null);
        stk.push(vh);
        
        for(ListNode i = head; i != null; i = i.next) {
            while(stk.peek().val < i.val) stk.pop();
            stk.peek().next = i;
            stk.push(i);
        }
        return vh.next;
    }
}

// 思路：
// 
```

#### （16）[1019. 链表中的下一个更大节点](https://leetcode.cn/problems/next-greater-node-in-linked-list/)（翻转链表+单减栈）

**问题**

给定一个长度为 `n` 的链表 `head`。

对于列表中的每个节点，查找下一个 **更大节点** 的值。也就是说，对于每个节点，找到它旁边的第一个节点的值，这个节点的值 **严格大于** 它的值。

返回一个整数数组 `answer` ，其中 `answer[i]` 是第 `i` 个节点( **从1开始** )的下一个更大的节点的值。如果第 `i` 个节点没有下一个更大的节点，设置 `answer[i] = 0` 。

例子

<img src="https://assets.leetcode.com/uploads/2021/08/05/linkedlistnext2.jpg" alt="img" style="zoom:80%;" />

```
输入：head = [2,7,4,3,5]
输出：[7,0,5,5,0]
```

**数据范围**

- 链表中节点数为 `n`
- `1 <= n <= 10^4`
- `1 <= Node.val <= 10^9`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public int[] nextLargerNodes(ListNode head) {
        ListNode p = null , q = head;
        int cnt = 0;
        while(q != null) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
            cnt ++;
        }

        ArrayDeque<ListNode> stk = new ArrayDeque<>();
        ListNode vh = new ListNode(0x3f3f3f3f, null);
        stk.push(vh);

        int idx = cnt - 1; 
        int[] res = new int[cnt];
        for(ListNode i = p; i != null; i = i.next) {
            while(stk.peek().val <= i.val) stk.pop();
            if(stk.peek().val != 0x3f3f3f3f) res[idx --] = stk.peek().val;
            else res[idx --] = 0;
            stk.push(i);
        }
        return res;
    }
}
// 翻转
// 单减栈
```

#### （17）[2058. 找出临界点之间的最小和最大距离](https://leetcode.cn/problems/find-the-minimum-and-maximum-number-of-nodes-between-critical-points/)

**问题**

每行的元素从左到右升序排列，每列的元素从上到下升序排列

如果当前节点的值 **严格大于** 前一个节点和后一个节点，那么这个节点就是一个 **局部极大值点** 。

如果当前节点的值 **严格小于** 前一个节点和后一个节点，那么这个节点就是一个 **局部极小值点** 。

注意：节点只有在同时存在前一个节点和后一个节点的情况下，才能成为一个 **局部极大值点 / 极小值点** 。

给你一个链表 `head` ，返回一个长度为 2 的数组 `[minDistance, maxDistance]` ，其中`minDistance` 是任意两个不同临界点之间的最小距离，`maxDistance` 是任意两个不同临界点之间的最大距离。如果临界点少于两个，则返回 `[-1，-1]` 。

例子

![img](https://assets.leetcode.com/uploads/2021/10/13/a2.png)

```
输入：head = [5,3,1,2,5,1,2]
输出：[1,3]
解释：存在三个临界点：
- [5,3,1,2,5,1,2]：第三个节点是一个局部极小值点，因为 1 比 3 和 2 小。
- [5,3,1,2,5,1,2]：第五个节点是一个局部极大值点，因为 5 比 2 和 1 大。
- [5,3,1,2,5,1,2]：第六个节点是一个局部极小值点，因为 1 比 5 和 2 小。
第五个节点和第六个节点之间距离最小。minDistance = 6 - 5 = 1 。
第三个节点和第六个节点之间距离最大。maxDistance = 6 - 3 = 3 。
```

**数据范围**

- 链表中节点的数量在范围 `[2, 10^5]` 内
- `1 <= Node.val <= 10^5`

**思路**

设指针 $q$ 指向了当前节点，$p$ 指向了$q$ 的前继节点，遍历链表。$idx$从$1$ 开始，记录节点编号，$lastid$ 记录上一个临界点的编号。 $maxv$  是临界点之间的最大距离, $minv$ 是临界点之间的最小距离

-  当前节点 $q$ 满足 $q.val > p.val \ \&\& \  q.val > q.next.val$ 时，$q$ 是局部极大值，更新$maxv = maxv + idx - lastid$。
-  当前节点$q$ 满足 $q.val < p.val \ \& \& \ q.val < q.next.val$ 时，$q$ 是局部极小值，更新 $ minv = Math.min(minv, idx - lastid)$。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public int[] nodesBetweenCriticalPoints(ListNode head) {
        int maxv = 0, minv = 0x3f3f3f3f; // maxv 是临界点之间的最大距离, minv 是临界点之间的最小距离
        int cnt = 0, idx = 1, lastid = -1; // cnt是临界点的个数，idx是链表节点编号从1开始, lastid是上一个临界点编号初始-1
        ListNode p = null, q = head; // p是q的前继节点，q是当前节点
        while(q != null) { // 遍历链表
            if(p != null && q.next != null) {
                if((q.val > p.val && q.val > q.next.val) || (q.val < p.val && q.val < q.next.val)) { // 局部极值点
                    if(lastid != -1) maxv = maxv + idx - lastid;
                    if(lastid != -1) minv = Math.min(minv, idx - lastid);
                    lastid = idx;
                    cnt ++ ;
                } 
            }

            p = q;
            q = q.next;
            idx ++;
        }
        if(cnt < 2) return new int[]{-1, -1};
        return new int[]{minv, maxv};
    }
}
```

#### （18）[2074. 反转偶数长度组的节点](https://leetcode.cn/problems/reverse-nodes-in-even-length-groups/)

**问题**

给你一个链表的头节点 `head` 。

链表中的节点 **按顺序** 划分成若干 **非空** 组，这些非空组的长度构成一个自然数序列（`1, 2, 3, 4, ...`）。一个组的 **长度** 就是组中分配到的节点数目。换句话说：

- 节点 `1` 分配给第一组
- 节点 `2` 和 `3` 分配给第二组
- 节点 `4`、`5` 和 `6` 分配给第三组，以此类推

注意，最后一组的长度可能小于或者等于 `1 + 倒数第二组的长度` 。

**反转** 每个 **偶数** 长度组中的节点，并返回修改后链表的头节点 `head` 。

例子

![img](https://assets.leetcode.com/uploads/2021/10/25/eg1.png)

```
输入：head = [5,2,6,3,9,1,7,3,8,4]
输出：[5,6,2,3,9,1,4,8,3,7]
解释：
- 第一组长度为 1 ，奇数，没有发生反转。
- 第二组长度为 2 ，偶数，节点反转。
- 第三组长度为 3 ，奇数，没有发生反转。
- 最后一组长度为 4 ，偶数，节点反转。
```

**数据范围**

- 链表中节点数目范围是 `[1, 10^5]`
- `0 <= Node.val <= 10^5`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    
    public ListNode reverse(ListNode h, int k) {
        if(h == null) return null; // 递归结束

        ListNode l = null, r = h; // r表示下一组的起始节点, l表示本组的尾节点
        int cnt = 0; // 数组实际长度
        for(int i = 0; r != null && i < k; i ++) {
            l = r;
            r = r.next;
            cnt ++;
        }

        r = reverse(r, k + 1); // 递归翻转r节点之后的链表
        if(cnt % 2 == 1) { // 本组是奇数长度，不用翻转
            l.next = r;
            return h;
        }
        // 本组是偶数长度，不用翻转
        ListNode p = null, q = h;
        while((cnt --) != 0) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }
        h.next = r;
        return p;
    }

    public ListNode reverseEvenLengthGroups(ListNode head) {
        return reverse(head, 1);
    }
}
```

#### （19）[2095. 删除链表的中间节点](https://leetcode.cn/problems/delete-the-middle-node-of-a-linked-list/)

**问题**

给你一个链表的头节点 `head` 。**删除** 链表的 **中间节点** ，并返回修改后的链表的头节点 `head` 。

长度为 `n` 链表的中间节点是从头数起第 `⌊n / 2⌋` 个节点（下标从 **0** 开始），其中 `⌊x⌋` 表示小于或等于 `x` 的最大整数。

- 对于 `n` = `1`、`2`、`3`、`4` 和 `5` 的情况，中间节点的下标分别是 `0`、`1`、`1`、`2` 和 `2` 。

 例子

<img src="https://assets.leetcode.com/uploads/2021/11/16/eg1drawio.png" alt="img" style="zoom:80%;" />

```
输入：head = [1,3,4,7,1,2,6]
输出：[1,3,4,1,2,6]
解释：
上图表示给出的链表。节点的下标分别标注在每个节点的下方。
由于 n = 7 ，值为 7 的节点 3 是中间节点，用红色标注。
返回结果为移除节点后的新链表。 
```

**数据范围**

- 链表中节点的数目在范围 `[1, 105]` 内
- `1 <= Node.val <= 105`

**思路**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteMiddle(ListNode head) {
        if(head.next == null) return null; // 特判

        ListNode s = head, f = head, p = null; // s是慢指针，f是快指针, p是s的前继结点
        while(f != null && f.next != null) { // s每次走一步, f每次走两步
            p = s;
            s = s.next;
            f = f.next.next;
        }
        p.next = s.next; // s指向中间节点，删除中间节点
        return head;
    }
}
```

#### （20）[143. 重排链表](https://leetcode.cn/problems/reorder-list/) 

**问题**

给定一个单链表 `L` 的头节点 `head` ，单链表 `L` 表示为：

```
L0 → L1 → … → Ln - 1 → Ln
```

请将其重新排列后变为：

```
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
```

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

<img src="https://pic.leetcode-cn.com/1626420320-YUiulT-image.png" alt="img" style="zoom:50%;" />

```
输入：head = [1,2,3,4,5]
输出：[1,5,2,4,3]
```

**数据范围**

- 链表的长度范围为 `[1, 5 * 10^4]`
- `1 <= node.val <= 1000`

**思路**

- 找链表中点
- 依据链表中点将链表分成前后两段，后半段链表翻转
- 错位合并两段链表

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public void reorderList(ListNode head) {
        if(head == null || head.next == null || head.next.next == null) return;
        // 找链表中点
        ListNode s = head, f = head, pre = null;
        while(f != null && f.next != null) {
            pre = s;
            s = s.next;
            f = f.next.next;
        }
        // 依据链表中点将链表分成前后两段，后半段链表翻转
        pre.next = null;
        ListNode l1 = head, l2 = s;
        ListNode p = null, q = l2;
        while(q != null) {
            ListNode t = q.next;
            q.next = p;
            p = q;
            q = t;
        }
        l2 = p;
        // 错位合并两段链表
        q = new ListNode();
        while(l1 != null && l2 != null) {
            q.next = l1;
            q = q.next;
            l1 = l1.next;

            q.next = l2;
            q = q.next;
            l2 = l2.next;
        }

        if(l1 != null) q.next = l1;
        if(l2 != null) q.next = l2;
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
