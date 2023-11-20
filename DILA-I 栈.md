# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之栈

### 1、栈

#### （1）716. 最大栈

**问题**：

设计一个最大栈数据结构，既支持栈操作，又支持查找栈中最大元素。

实现 `MaxStack` 类：

- `MaxStack()` 初始化栈对象
- `void push(int x)` 将元素 x 压入栈中。
- `int pop()` 移除栈顶元素并返回这个元素。
- `int top()` 返回栈顶元素，无需移除。
- `int peekMax()` 检索并返回栈中最大元素，无需移除。
- `int popMax()` 检索并返回栈中最大元素，并将其移除。如果有多个最大元素，只要移除 **最靠近栈顶** 的那个。

**注意**：调用 `pop`、`top`、`peekMax` 或 `popMax` 时，栈中 **至少存在一个元素**

**思路**：用**两个栈**来实现

- 设置两个栈，一个普通栈，一个最大值栈
- 最大值栈，栈顶元素是当前集合中的最大值

```python
class MaxStack:

    def __init__(self):
        self.stk = [] # 普通的栈
        self.maxStk = [] # 栈顶元素是当前集合中的最大值

    def push(self, x: int) -> None:
        self.stk.append(x)
        if not self.maxStk:
            self.maxStk.append(x)
        else:
            self.maxStk.append(max(self.maxStk[-1], x))

    def pop(self) -> int:
        self.maxStk.pop()
        return self.stk.pop()

    def top(self) -> int:
        return self.stk[-1]

    def peekMax(self) -> int:
        return self.maxStk[-1]

    def popMax(self) -> int:
        temp = []
        x = self.maxStk[-1] # 当前栈中的最大元素
        while x != self.stk[-1]: # 其他元素出栈，直到栈顶为最大元素位置
            temp.append(self.pop())
        self.pop() # 删除最大元素
        
        while temp: # 将临时出栈的元素依次入栈
            self.push(temp.pop())
        return x
```

### 



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
