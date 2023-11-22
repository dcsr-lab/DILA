# 剑指 Offer  算法笔记

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>



> 引言
>
> 此文章是为了记录算法学习的过程，主要包含**xxx**等知识
>
> :man:：`Laplace`	:timer_clock: ：`2021-11-07`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  



### 1、栈与队列（简单）

##### （1）剑指 offer 09. 用两个栈实现队列 

思路：

- 一个栈是**插入栈**，另一个栈是**删除栈**。

- 当有元素入队时，则将此元素压入插入栈中。栈底元素是最先入队的元素，栈顶元素是最后入队的元素。

- 当元素出队时，访问删除栈，弹出栈顶元素。当删除栈为空，则将插入栈的所有元素出栈，并依次压入删除栈。若删除栈还是为空，则没有元素需要出栈。


```c++
class CQueue {
public:

    stack<int> stk1, stk2; // 插入栈、删除栈

    // 构造时，初始化stk1, stk2
    CQueue() {
        while(!stk1.empty()){
            stk1.pop();
        }
        while(!stk2.empty()){
            stk2.pop();
        }
    }
    
    void appendTail(int value) { // 入队O(1)
        stk1.push(value);
    }
    
    int deleteHead() { // 出队,O(1), 虽然看起来是O(n),但是每个元素只会进出栈各1次，
        if(stk2.empty()){
            while(!stk1.empty()){
                stk2.push(stk1.top());
                stk1.pop();
            }
        }
        if(stk2.empty()){
            return -1;
        }else{
            int res = stk2.top();
            stk2.pop();
            return res; 
        }
    }
};
```

##### （2）剑指 offer 30. 包含min函数的栈

思路：

- 用两个栈实现，一个是**普通栈**，另一个**最小值栈**，栈的栈顶元素用来维护当前集合中的最小值。
- 最小值栈的实现：如果最小值栈为空，则将当前要压入的元素压入栈中；否则，将最小值栈的栈顶元素与当前要压入栈的元素比较，将两者中最小元素的压入最小值栈中。

```c++
class MinStack {
public:

    stack<int> stk; // 普通栈
    stack<int> minStk; // 栈顶元素维护当前集合中的最小值

    MinStack() {               
    }
    
    void push(int x) {  // O(1)
        stk.push(x);
        if(minStk.empty()){
            minStk.push(x);
        } else {
            int t = minStk.top();
            t = ::min(t,x);
            minStk.push(t);
        }
    }
    
    void pop() { // O(1)
        stk.pop();
        minStk.pop();
    }
    
    int top() {
        return stk.top();
    }
    
    int min() { // O(1)
        return minStk.top();
    }
};
```

### 2、链表（简单）

##### （1）剑指 offer 06. 从尾到头打印链表

思路：从链表头至链表尾顺序遍历链表，将遍历的元素依次压入栈中，**元素出栈顺序**即**逆序遍历链表的顺序**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:

    stack<int> stk;

    vector<int> reversePrint(ListNode* head) {
        ListNode* p = head;
        while(p != NULL){
            stk.push(p->val);
            p = p->next;
        }
        vector<int> vec;
        while(!stk.empty()){
            vec.push_back(stk.top());
            stk.pop();
        }
        return vec;
    }
};
```

##### （2）剑指 offer 24. 反转链表 :star2:

思路：用 p，q 指针分别指向两个结点，动态改变相邻结点的依赖关系

<div align="center">
    <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/202111081833748.png" alt="芝士" width = "650px" align= "center"/>
</div>

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:

    ListNode* reverseList(ListNode* head) { // p q 双指针
        ListNode *p = NULL, *q = head;
        while(q != NULL)
        {
            ListNode* t = q->next;
            q->next = p;
            p = q;
            q = t;
        }
        
        return p;
    }
};
```

##### （3）剑指 offer 35. 复杂链表的复制

思路：用哈希表记录原链表和新链表结点之间的映射关系，原链表结点和 rondom 之间的映射关系。用 $O(n)$ 的时间复杂度两次遍历链表，第一次复制 val和 next，第二次遍历复制 random。

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/
class Solution {
public:
    Node* copyRandomList(Node* head) {
        Node *h = NULL, *p = NULL, *q = NULL, *t = head;
        // mp1记录原链表与新链表的结点之间的映射关系, mp2 记录原表结点和 random 之间的映射关系
        unordered_map<Node*, Node*> mp1, mp2; 

        while(t != NULL){ // 复制链表（val, next）O(n)
            Node* node = new Node(t->val);
    
            mp1.insert(pair<Node*, Node*>(t, node));
            mp2.insert(pair<Node*, Node*>(t, t->random));

            p = q;
            q = node;

            if(t == head) {
                h = node;    
            }else{
                p->next = q;
            }
            t = t->next;
        }

        t = head, p = h;
        while(t != NULL && p != NULL){ // 更新新链表 random O(n)
            if(t->random != NULL){
                p->random = mp1.find(mp2.find(t)->second)->second;
            }
            t = t->next;
            p = p->next;
        }

        return h;
    }
};
```

### 3、字符串（简单）

##### （1）剑指 offer 05. 替换空格

思路：C++ String API

```c++
class Solution {
public:
    string replaceSpace(string s) {

        while(s.find(" ") != s.npos){
            // replace(p,n,s) 删除从p开始的n个字符，然后在p处插入字符串s
            s = s.replace(s.find(" "), 1, "%20");
        }
        return s;
    }
};
```

##### （2）剑指 offer 58 - II. 左旋字符串

思路：C++ String API

```c++
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        string a = s.substr(0, n); // 从0位置截取n个字符
        string b = s.substr(n); // 从 n位置截取到字符串尾部
        return b + a;
    }
};
```

### 4、查找算法（简单）

##### （1）剑指 offer 03. 数组中重复的数字

思路：线性查找求每个数出现的次数

```c++
class Solution {
public:

    static const int N = 100010; // 定义静态常量
    int st[N]; // 全局变量默认初始化为 0；

    int findRepeatNumber(vector<int>& nums) {
        
        for(int i = 0; i < nums.size(); i ++){ // 求数组中每个数出现的次数
            st[nums[i]] ++ ;
        }

        int res = -1;
        for(int i = 0; i < N; i ++){ // 从小到大找到第一个重复出现的数
            if(st[i] > 1){
                res = i;
                break;
            }
        }

        return res;
    }
};
```

##### （2）剑指 offer 53 - 1. 在排序数组中查找数字 

思路：已知数组已经排序，分别二分左边界和右边界，计算数字出现的次数

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        
        if(!nums.size()) return 0; // 数组为空

        int l = 0, r = nums.size()-1;
        while(l < r){ // 二分左边界
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        
        if(nums[l] != target) return 0; // 没找到

        int t = l; // 记录做边界
        r = nums.size()-1;
        while(l < r){ // 二分右边界
            int mid = l + r + 1>> 1;
            if(nums[mid] <= target) l = mid;
            else r = mid - 1;
        }

        return r - t + 1; // target 出现的次数
    }
};
```

##### （3）剑指 offer 53 - II. 0~n-1中缺失的数字 :raised_hand_with_fingers_splayed:

思路：

二分

- 下标为 i 的数字等于 i，则缺失的数字在 i 的右边；
- 下标为 i 的数字不等于 i，则缺失的数字可能等于 i;	

```c++
class Solution {
public:


    int missingNumber(vector<int>& nums) {
        // 二分
        // 长度为n-1的有序非重复数组，数字范围0~n-1；下标为i的数字应该等于i，否则发生错位
        int l = 0, r = nums.size()-1;
        while(l < r){
            int mid = l + r >> 1;
            if(nums[mid] != mid) r = mid;
            else l = mid + 1;
        }

        if(r == l && l != nums[l]) return l;
        else return nums.size(); // 缺 n-1
    }
};
```

### 5、查找算法（中等）

##### （1）剑指 offer 04. 二维数组中查找 [:raised_hand_with_fingers_splayed:](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/) 

思路：

遍历 + 二分 $O(nlogn)$：以行为单位遍历矩阵 matrix，然后在每一行中进行二分查找

```c++
class Solution {
public:

    bool bsearch(vector<int>& nums, int x){ // 从数组nums中，二分查找x
        int l = 0, r = nums.size() - 1;
        while(l < r){
            int mid = (l + r + 1) >> 1;
            if(nums[mid] > x) r = mid - 1;
            else l = mid;
        }

        if(l == r && nums[l] == x) return true;
        else return false;
    }

    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) { // 以行为单位遍历矩阵 matrix，然后在每一行中进行二分查找

        bool res = false;
        for(auto nums : matrix){
            if(bsearch(nums, target)){
                res = true;
                break;
            }
        }
        return res;
    }
};
```

**数组路径** $O(n + m)$：从矩阵的右上角开始查找，当前位置比 target 大，向左走一列，当前位置比 target 小，向下走一行

```c++
class Solution {
public:

    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target){
        if(!matrix.size() || !matrix[0].size()){
            return false;
        }

        for(int i = 0, j = matrix[0].size() - 1; i < matrix.size() && j >= 0; ){
                if(matrix[i][j] < target) i ++ ;
                else if(matrix[i][j] > target) j --;
                else if(matrix[i][j] == target) return true;
        }
        return false;
    }

};
```

##### （2）剑指 Offer 11. 旋转数组的最小数字 [:raised_hand_with_fingers_splayed:](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/):raised_hand_with_fingers_splayed:

[官方题解](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-by-leetcode-s/)  [精选题解](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/mian-shi-ti-11-xuan-zhuan-shu-zu-de-zui-xiao-shu-3/)

思路：$O(logn)$

- 初始化： 声明 $l$, $r$ 双指针分别指向 $nums$ 数组左右两端；
- 循环二分： 设 $mid=(l+r)/2$ 为每次二分的中点（ "/" 代表向下取整除法，因此恒有 $l \leq mid < r$ ），可分为以下三种情况：
- 当 $nums[mid]>nums[r]$ 时： $mid$ 一定在 左排序数组中，即旋转点 $x$ 一定在 $[mid + 1, r]$ 闭区间内，因此执行 $l=mid+1$；
- 当 $nums[mid] < nums[r]$ 时： $mid$ 一定在 右排序数组中，即旋转点 $x$ 一定在 $[l,mid]$ 闭区间内，因此执行 $r = mid$；
- 当 $nums[mid]=nums[r]$ 时：
  - 当 $nums[l] < nums[r]$ 时：$[l, r]$ 完全在右排序数组中，旋转点 $x$ 即为 $nums[l]$;
  - 当 $nums[l] > nums[r]$时，$mid$ 一定在右排序数组，即旋转点 $x$ 一定在 $[l,mid]$ 闭区间内，因此执行 $r = mid$；
  - 当 $nums[l] = nums[r]$，即当 $nums[l] = nums[mid] = nums[r]$， 无法判断 $mid$ 在哪个排序数组中，即无法判断旋转点 $x$ 在 $[l,mid]$ 还是 $[mid + 1, r]$ 区间中。解决方案： 执行 $r = r - 1$ 缩小判断范围;
    - $r = r- 1$ 后, 旋转点 $x$ 仍在区间 $[l,r]$ 内，$mid$ 可能在右边也可能在左边;
    - $r = r - 1$ 后, 越过（丢失）了旋转点 $x(=nums[r])$，但此时 $mid$ 一定在左边，此时 $[l,mid]$ 内的 $nums[i]$ 都相等，最后返回的值一定是 $nums[l]$;
- 返回值： 当 $l = r$ 时跳出二分循环，并返回 旋转点的值 $nums[l]$ 即可。

```c++
class Solution {
public:

    int bsearch(vector<int>& numbers){

        int l = 0, r = numbers.size()-1;

        while(l < r){
            int mid = (l + r) >> 1; // l <= mid < r
            if(numbers[mid] > numbers[r]){ // mid 的左边一定有序，最小值x在 [mid + 1, r];
                l = mid + 1;
            }
            else if(numbers[mid] < numbers[r]){ // mid 的右边一定有序，最小值x在 [l, mid]
                r = mid;
            }
            else{ // numbers[mid] == numbers[r] 
 
                if(numbers[l] < numbers[r]){ // [l, r] 完全在右边
                    return numbers[l];
                }else if(numbers[l] > numbers[r]){ // 即 numbers[l] > numbers[mid]，mid 在右边
                    r = mid;
                }else{ // numbers[l] = numbers[mid] = numbers[r]
                    r --; // 策略：慢慢缩小搜索范围，如果使用二分容易导致，最小值x不在[l,r] 之间
                    // (1)  r = r - 1 后, 最小值x仍在区间 [l,r] 内，mid 可能在右边也可能在左边
                    // (2)  r = r - 1 后, 越过（丢失）了 最小值x(=numbers[r])，但此时 mid 一定在左边，此时[l,mid]内的numbers[i]都相等，最后返回的值一定是numbers[l](=numbers[mid]=最小值x) 
                }
            } 
        }
        return numbers[l];
    }

    int minArray(vector<int>& numbers) {
        return bsearch(numbers);
    }
};
```

##### （3）剑指 Offer 50. 第一个只出现一次的字符

思路： 哈希表存储出现的频率 $O(n)$

```c++
class Solution {
public:
    // 暴力 + 哈希
    unordered_map<int, int> mp; // 默认初始化为 0 

    char firstUniqChar(string s) {
        for(int i = 0; i < s.length(); i ++){
            mp[s[i]] ++ ;
        }
        for(int i = 0; i < s.length(); i ++){
            if(mp[s[i]] == 1) 
                return s[i];
        }

        return ' ';
    }
};
```

[哈希表存储索引](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/solution/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-by-3zqv5/) $O(n)$

[队列](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/solution/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-by-3zqv5/) $O(n)$ 

### 6、搜索与回溯算法

##### （1）剑指 Offer 32 - I. 从上到下打印二叉树

思路：二叉树的层序遍历

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
   
    vector<int> bfs(TreeNode* root){ // 二叉树的层序遍历
        vector<int> res;
        queue<TreeNode*> q;

        if(root == NULL) return res; // 空二叉树，防止空指针异常
        q.push(root); // 根结点入队
        
        while(!q.empty()){
            TreeNode* t = q.front();
            q.pop();
            res.push_back(t->val);
            if(t->left != NULL) q.push(t->left);
            if(t->right != NULL) q.push(t->right);
        }

        return res;
    }

    vector<int> levelOrder(TreeNode* root) {
        return bfs(root);
    }
};
```

##### （2）剑指 Offer 32 - II. 从上到下打印二叉树 II

思路：二叉树的层序遍历，每一次遍历一层得到结点

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:

    vector<vector<int>> bfs(TreeNode* root){ // 程序遍历，每次遍历一层
        queue<TreeNode*> q;
        vector<vector<int>> res;

        if(root != NULL) q.push(root);
        while(!q.empty()){
            vector<int> temp;
            for(int i = q.size()-1; i >= 0; i --){ // 一次处理一层的结点
                TreeNode* t = q.front();
                q.pop();

                temp.push_back(t->val);
                if(t->left != NULL) q.push(t->left);
                if(t->right != NULL) q.push(t->right);
            }
            res.push_back(temp);
        }
        return res;
    }

    vector<vector<int>> levelOrder(TreeNode* root) {
        return bfs(root);
    }
};
```

##### （3）剑指 Offer 32 - III. 从上到下打印二叉树 III

思路：

- BFS，二叉树的层序遍历，每一次遍历一层得到结点，同时判断是奇数层还是偶数层，偶数层将结果逆序；
- 或者 DFS

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:

    vector<vector<int>> bfs(TreeNode* root){ // bfs 方式
        queue<TreeNode*> q;
        vector<vector<int>> res;

        int count = 1; // 层数
        if(root != NULL) q.push(root);
        while(!q.empty()){
            vector<int> temp;

            for(int i = q.size()-1; i >= 0; i --){
                TreeNode* t = q.front();
                q.pop();

                temp.push_back(t->val);
                if(t->left != NULL) q.push(t->left);
                if(t->right != NULL) q.push(t->right);
            }

            // if(count % 2 == 0) reverse(temp.begin(), temp.end());
            // res.push_back(temp);
            if(count % 2 == 0) res.push_back(vector<int>(temp.rbegin(), temp.rend()));
            else res.push_back(temp);
            count ++ ;
        }
        return res;
    }

    vector<vector<int>> ret;
    vector<int> r;
    void dfs(TreeNode* u, int l){ // dfs 方式
        if(u == NULL) return ;
        if(ret.size() == l) ret.push_back(r);

        if(l % 2 == 0) ret[l].push_back(u->val);  // 层数从0开始，偶数层顺序
        else ret[l].insert(ret[l].begin(), u->val); // 层数从1开始，奇数层逆序

        dfs(u->left, l + 1);
        dfs(u->right, l + 1);
    }

    vector<vector<int>> levelOrder(TreeNode* root) {
        dfs(root, 0);
        return ret;
        //return bfs(root);
    }
};
```

##### （4）剑指 Offer 27. 二叉树的镜像

思路：DFS 搜索二叉树，交换结点的左右子树

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:

    void dfs(TreeNode*& u){
        if(u == NULL) return ;

        TreeNode* temp = u->left;
        u->left = u->right;
        u->right = temp;

        if(u->left != NULL) dfs(u->left);
        if(u->right != NULL) dfs(u->right);
    }

    TreeNode* mirrorTree(TreeNode* root) {
        dfs(root);
        return root;
    }
};
```

##### （5）剑指 Offer 28. 对称的二叉树

思路：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:

    static const int N = 1010;
    int l[N], r[N], v[N], count = 1; // 存储原二叉树的结构和结点值
    int ml[N], mr[N], mv[N], mcount = 1; // 存储镜像二叉树的结构和结点值

    int dfs(TreeNode* u, int& k){ // 将原二叉树的结构和结点值存储下来
        if(u == NULL) return 0;

        int t = k;
        v[t] = u->val;
        if(u->left != NULL) l[t] = dfs(u->left, ++ k);
        if(u->right != NULL) r[t] = dfs(u->right, ++ k);

        return t;
    }

    int dfs_mirr(TreeNode* u, int& k){ // 将镜像二叉树的结构和结点值存储下来
        if(u == NULL) return 0;

        int t = k;
        mv[k] = u->val;
        
        if(u->left != NULL) ml[t] = dfs_mirr(u->left, ++ k);
        if(u->right != NULL) mr[t] = dfs_mirr(u->right, ++ k);
        return t;
    }

    void bulid_mirr(TreeNode*& u){ // 求二叉树的镜像
        if(u == NULL) return ;

        if(u->left != NULL) bulid_mirr(u->left);
        if(u->right != NULL) bulid_mirr(u->right);

        TreeNode* temp = u->left;
        u->left = u->right;
        u->right = temp;
    }

    bool isSymmetric(TreeNode* root) { 

        dfs(root, count);
        bulid_mirr(root);
        dfs_mirr(root, mcount);

        if(count == 2) return false; // 特判，包含2个结点二叉树一定不是对称二叉树
        bool flag = true;

        for(int i = 1; i <= count; i ++ ){ // 原二叉树与镜像二叉树一致，则一定是对称二叉树（特殊情况除外）      
            if(v[l[i]] != mv[ml[i]] || v[r[i]] != mv[mr[i]]){
                flag = false;
                break;
            }
        }

        return flag;
    }
};
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

