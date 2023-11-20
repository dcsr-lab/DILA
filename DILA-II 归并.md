# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之归并



### 1、归并

#### （1）4. 寻找两个正序数组的中位数

**问题**：

给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

 **思路**：**二路归并**

```python
class Solution:
    
    def merge(self, a, b): # 将两个正序数组进行归并
        res = [] # 存储a与b归并后的结果
        
        i, j = 0, 0 # i是指向a数组的指针，j指向b数组的指针
        while i < len(a) and j < len(b): # 二路归并
            if a[i] < b[j]:
                res.append(a[i])
                i += 1
            else:
                res.append(b[j])
                j += 1
        while i < len(a):
            res.append(a[i])
            i += 1
        while j < len(b):
            res.append(b[j])
            j += 1
        
        mid = 0 + len(res) - 1 >> 1 # 计算中点
        if len(res) % 2 == 0:
            return (res[mid] + res[mid + 1]) / 2
        else:
            return res[mid]

    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        return self.merge(nums1, nums2)
```

#### （2）21. 合并两个有序链表

**问题**：

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**思路**：二路归并

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        res = ListNode()
        i, j, k = list1, list2, res # i指向链表1, j指向链表2, res指向记录结果的指针

        while i and j: # 二路归并
            if i.val <= j.val:
                k.next, i = i, i.next
            else:
                k.next, j = j, j.next
            k = k.next 
        
        if i:
            k.next = i
        if j:
            k.next = j
        return res.next
```

#### （3）23. 合并K个升序链表

**问题**：

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**思路**：**k 路归并**(转化为分治和二路归并)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def merge(self, q, l, r):
        if l == r: # [l,r]区间只有一个元素时,则递归结束
            return q[r]
        
        mid = l + r >> 1 # mid是区间[l,r]的中点
        i, j = self.merge(q, l, mid), self.merge(q, mid + 1, r) # 分治

        k = res = ListNode()
        while i and j: # 二路归并
            if i.val <= j.val:
                k.next, i = i, i.next
            else:
                k.next, j = j, j.next
            k = k.next
        if i:
            k.next = i
        if j:
            k.next = j
        return res.next    

    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
        if not lists: # 特判
            return None
        return self.merge(lists, 0, len(lists) - 1) # k路归并
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
