# 🔥 LeetCode 热题 HOT 100

<div align="center">
    <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202110171205398.jpg" alt="芝士" width = "150px" align= "center"/>
</div>



> 引言
>
> 此文章是为了
>
> :man:：`Laplace`	:timer_clock: ：`2021-03-03`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  



### 1、树

##### （1）98. 验证二叉搜索树

**问题**：

给你一个二叉树的根节点 `root` ，判断其是否是一个有效的二叉搜索树。

**有效** 二叉搜索树定义如下：

- 节点的左子树只包含 **小于** 当前节点的数
- 节点的右子树只包含 **大于** 当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树

**思路**：**中序遍历**，遍历中序序列是否是**正序**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:

    def __init__(self):
        self.inorder = [] # 存储中序序列

    def dfs(self, u):
        lres, rres = True, True # // 如果u没有左右子树时, 即为true, 表示左右子树都是颗二叉搜索树
        if u.left:
            lres = self.dfs(u.left) # 递归判断左子树是否为二叉搜索树
        
        size = len(self.inorder)
        if size and self.inorder[size - 1] >= u.val: # 判断当前结点与前继结点是否正序
            return False
        self.inorder.append(u.val)

        if u.right:
            rres = self.dfs(u.right) # 递归判断右子树是否为二叉搜索树

        if lres and rres:
            return True
        else:
            return False

    def isValidBST(self, root: TreeNode) -> bool:
        return self.dfs(root)
```

##### （2）102. 二叉树的层序遍历

**问题**：

给你二叉树的根节点 `root` ，返回其节点值的 **层序遍历** 。 （即逐层地，从左到右访问所有节点）。

**思路**：**层序遍历**，**按层遍历**

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:

    def bfs(self, root): # 层序遍历,返回每一层的结点
        res, q = [], [root] # res存储结果. q数组模拟队列
        hh, tt = 0, 0 # 队首和队尾指针

        while hh <= tt:
            head, tail = hh, tt # 按层遍历, head.tail记录每一层的开始和结尾
            layer = [] # 记录此层结点

            while hh <= tail: # 遍历每一层
                t = q[hh] # 队头元素出队列
                hh += 1
                layer.append(t.val)

                if t.left: # t的左孩子入队列
                    tt += 1
                    q.append(t.left)
                if t.right: # t的右孩子入队列
                    tt += 1
                    q.append(t.right)
            res.append(layer)

        return res

    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root: # 特判, root为None时
            return []
        return self.bfs(root) # 层序遍历
```



##### （3）105. 从前序与中序遍历序列构造二叉树

**问题**：

给定两个整数数组 `preorder` 和 `inorder` ，其中 `preorder` 是二叉树的**先序遍历**， `inorder` 是同一棵树的**中序遍历**，请构造二叉树并返回其根节点。

思路：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:

    def __init__(self):
        self.inorder = [] # 中序序列
        self.preorder = [] # 前序序列
        self.pos = {} # 哈希表, 每个结点在中序序列中的位置

    def build(self, il, ir, pl, pr): # il,ir分别为中序序列的左边界, 右边界; 同理pl,pr是前序序列
        root = TreeNode(self.preorder[pl]) # 根结点
        k = self.pos[root.val] # 根结点在中序遍历中的位置

        if il < k: # 构建左子树
            root.left = self.build(il, k - 1, pl + 1, pl + 1 + (k - 1 - il))
        if k < ir: # 构建右子树
            root.right = self.build(k + 1, ir, pl + 1 + (k - 1 - il) + 1, pr)
        return root

    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        self.inorder = inorder
        self.preorder = preorder
        n = len(inorder)
        self.pos = {inorder[i] : i for i in range(n)} # 初始化pos
        return self.build(0, n - 1, 0, n - 1)
```





### 4、上下车问题

##### （1）253. 会议室 II:writing_hand: 

**问题**：

给你一个会议时间安排的数组 `intervals` ，每个会议时间都会包括开始和结束的时间  `intervals[i] = [starti, endi]` ，返回 *所需会议室的最小数量* 。

**思路**：

理解成上下车问题会比较容易解决，不用在意是谁上车还是下车，只需要注意什么时候上下车就可以。

以第一个示例来说：`intervals = [[0,30],[5,10],[15,20]]`

```python
↑    ↑    ↓     ↑      ↓             ↓
0----5----10----15-----20-----------30-->
```

这样可以把上车和下车的时间分成两组，通过两个指针滑动的方式，判断同时在车上的最大数就可以了

```python
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        n = len(intervals)
        up, down = {}, {} # 哈希表, up[i] 表示在车站i上车的人数, down[i]表示在车站下车的人数
        
        for i in range(n): # 初始化up和down
            a, b = intervals[i][0], intervals[i][1]
            up[a] = up[a] + 1 if a in up else 1 
            down[b] = down[b] + 1 if b in down else 1

        st, ed = min(list(up)), max(list(down)) # st是最小车站号, ed是最大车站号
        
        cnt, res = 0, 0 # cnt表示车上的人数, res表示车上的最大人数
        for i in range(st, ed + 1): # 枚举所有站点
            if i in down: # i站点有人下车
                cnt -= down[i]
            if i in up:  # i站点有人上车
                cnt += up[i]        
            res = max(res, cnt)
        return res
```

##### （2）1094. 拼车:writing_hand: 

**问题**：

假设你是一位顺风车司机，车上最初有 `capacity` 个空座位可以用来载客。由于道路的限制，车 **只能** 向一个方向行驶（也就是说，**不允许掉头或改变方向**，你可以将其想象为一个向量）。

这儿有一份乘客行程计划表 `trips[][]`，其中`trips[i] = [num_passengers, start_location, end_location]` 包含了第 `i` 组乘客的行程信息：

- 必须接送的乘客数量；
- 乘客的上车地点；
- 以及乘客的下车地点。

这些给出的地点位置是从你的 **初始** 出发位置向前行驶到这些地点所需的距离（它们一定在你的行驶方向上）。

请你根据给出的行程计划表和车子的座位数，来判断你的车是否可以顺利完成接送所有乘客的任务（当且仅当你可以在所有给定的行程中接送所有乘客时，返回 `true`，否则请返回 `false`）。

**注意**：你可以假设乘客会自觉遵守 “**先下后上**” 的良好素质

**思路**：思路与上一题类似

```python
class Solution:
    def carPooling(self, trips: List[List[int]], capacity: int) -> bool:
        n = len(trips)
        up, down = {}, {} # 哈希表, up[i] 表示在车站i上车的人数, down[i]表示在车站下车的人数

        for i in range(n): # 初始化up和down
            a, b, c = trips[i][0], trips[i][1], trips[i][2]
            up[b] = up[b] + a if b in up else a
            down[c] = down[c] + a if c in down else a
        
        st, ed = min(list(up)), max(list(down)) # st是最小车站号, ed是最大车站号

        cnt = 0 # cnt表示车上的人数
        for i in range(st, ed + 1): # 枚举所有站点
            if i in down: # i站点有人下车
                cnt -= down[i]
            if i in up: # i站点有人上车
                if cnt + up[i] > capacity: # 车的容量不够
                    return False
                cnt += up[i]
        return True
```

### 5、数组

##### （1）448. 找到所有数组中消失的数字

**问题**：

给你一个含 `n` 个整数的数组 `nums` ，其中 `nums[i]` 在区间 `[1, n]` 内。请你找出所有在 `[1, n]`  范围内但没有出现在 `nums` 中的数字，并以数组的形式返回结果。

**思路**：

将所有正数作为数组下标，置对应数组值为负值。那么，仍为正数的位置即为（未出现过）消失的数字。

举个例子：

- 原始数组：`[4,3,2,7,8,2,3,1]`
- 重置后为：`[-4,-3,-2,-7,8,2,-3,-1]`

```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        for i in range(len(nums)): # 将所有正数作为数组下标，置对应数组值为负值
            if nums[abs(nums[i]) - 1] > 0:
                nums[abs(nums[i]) - 1] *= -1
        
        res = []
        for i in range(len(nums)):
            if(nums[i] > 0): # 仍为正数的位置即为（未出现过）消失的数字
                res.append(i + 1)
        return res
```

### 6、动态规划

##### （1）53. 最大子数组和

**问题**：

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

**思路**：

- $f[i]$ 表示以 $i$ 结尾连续子数组的最大和
  - $f[i] = f[i - 1] + nums[i], f[i - 1] >= 0$
  - $f[i] = nums[i], f[i - 1] < 0$
- 求出所有 $f[i]$ 的最大值

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        n = len(nums)
        f = [0 for i in range(n + 1)] # 初始化数组, f[i]表示以i结尾连续子数组的最大和

        res = -2e9 # 所有连续子数组的最大和
        for i in range(n):
            if f[i - 1] >= 0:
                f[i] = f[i - 1] + nums[i]
            else:
                f[i] = nums[i]
            res = max(res, f[i])
        return res
```

### 7、双指针

##### （1）283. 移动零

**问题**：

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。

**思路**：**双指针**， `i`从左往右依次找不为`0`的数。将所有不为`0`的数依次从左往右赋值

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int i = 0, j = 0; // 双指针, i从左往右依次找不为0的数. 将所有不为0的数依次从左往右赋值
        while(i < nums.length){
            if(nums[i] != 0) nums[j ++] = nums[i]; 
            i ++;
        }
        Arrays.fill(nums, j, i, 0); // 将剩余的数赋值为0   
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

排版美化工具：[Typora-Purple](https://theme.typora.io/theme/Purple/)

