# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第二部分之数论



### 1、质数

#### （1）204. 计数质数

**问题**：给定整数 `n` ，返回 *所有小于非负整数 `n` 的质数的数量* 。

**思路**：**线性筛质数**

```java
class Solution {

    int[] primes; // primes 存储所有的质数
    int cnt = 0; // 质数的个数

    boolean[] st; // st[i] 为true, 表示i是合数且被筛过
    int[] minp; // minp[i] 是i的最小质因子
    boolean[] isPrime; // isPrime[i] 为true表示i是质数

    public void getPrimes(int n) { // 线性筛质数
        for(int i = 2; i <= n; i ++ ) {
            if(!st[i]) { isPrime[i] = true; minp[i] = i; primes[cnt ++] = i; } // i还没有被筛过, i一定是质数
            for(int j = 0; primes[j] <= n / i; j ++ ) {
                st[primes[j] * i] = true; // primes[j] * i是合数
                minp[primes[j] * i] = primes[j]; // primes[j] * i的最小质因子数primes[j]
                if(i % primes[j] == 0) break;
            }
        }
    }

    public int countPrimes(int n) {
        if(n <= 2) return 0;

        primes = new int[n + 10];
        st = new boolean[n + 10];
        minp = new int[n + 10];
        isPrime = new boolean[n + 10];

        getPrimes(n - 1);
        return cnt;
    }
}
```

### 2、约数

#### （1）1979. 找出数组的最大公约数

**问题**：给你一个整数数组 `nums` ，返回数组中最大数和最小数的 **最大公约数** 。两个数的 **最大公约数** 是能够被两个数整除的最大正整数。

**思路**：欧几里得算法求最大公约数

```java
class Solution {

    public int gcd(int a, int b) { // 欧几里得算法求a和b的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }

    public int findGCD(int[] nums) {
        int minv = Integer.MAX_VALUE, maxv = Integer.MIN_VALUE;
        for(int i = 0; i < nums.length; i ++ ) {
            maxv = Math.max(maxv, nums[i]);
            minv = Math.min(minv, nums[i]);
        }
        return gcd(minv, maxv);
    }
}
```

#### （2）6015. 统计可以被 K 整除的下标对数目:writing_hand: 

**问题**：

给你一个下标从 **0** 开始、长度为 `n` 的整数数组 `nums` 和一个整数 `k` ，返回满足下述条件的下标对 `(i, j)` 的数目：

- `0 <= i < j <= n - 1` 且
- `nums[i] * nums[j]` 能被 `k` 整除。

**思路**：**求最大公约数** 和 **求约数**

- 对于每一个$nums[i]$,  $(nums[i] \times nums[j]) \% k == 0$，即 $nums[i] \times nums[j]$ 至少包含一个$k$ 
- $d = gcd(nums[i],k)$，那么 $nums[j]$ 一定包含因数 $k/d$ 
- 求出 $nums$ 中包含因数 $k/d$ 数的个数，即是满足条件$(nums[i] \times nums[j]) \% k == 0$，下标对$(i,j)$的数目
- 将所有的下标对累加起来

```java
class Solution {
    int[] hash = new int[100010]; // hash[i]表示i出现的次数, 即整个nums数组中包含i为约数的数的个数
    
    public int gcd(int a, int b){ // 求a和b的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }
    
    public void getDivisors(int x){ // 求 x 所有约数
        for(int i = 1; i <= x / i; i ++)
            if(x % i == 0){
                hash[i] ++;
                if(x / i != i) hash[x / i] ++ ;
            }
    }
    
    public long coutPairs(int[] nums, int k) {
        long res = 0;
        
        for(int i = 0; i < nums.length; i ++){
            int d =  gcd(nums[i], k);
            res += hash[k / d];
            
            getDivisors(nums[i]);
        }
        return res;
    }
}
```

#### （3）5996. 统计数组中相等且可以被整除的数对

**问题**：

给你一个下标从 **0** 开始长度为 `n` 的整数数组 `nums` 和一个整数 `k` ，请你返回满足 `0 <= i < j < n` ，`nums[i] == nums[j]` 且 **`(i * j)` 能被 `k` 整除的数对 `(i, j)` 的 数目** 。

**思路**：**此题是上一题的扩展**

```java
class Solution {
    int[][] hash = new int[110][110]; // hash[i][j]表示nums中所有值等于i的数,它们的数组下标包含因数j的数的个数

    public int gcd(int a, int b){ // 求a和b的最大公约数
        return b != 0 ? gcd(b, a % b) : a;
    }

    public void getDivisors(int num, int x){ // 求x的所有约数
        for(int i = 1; i <= x / i; i ++)
            if(x % i == 0){
                hash[num][i] ++ ;
                if(x / i != i) hash[num][x / i] ++;
            }
    }

    public int countPairs(int[] nums, int k) {
        int res = 0;
        Arrays.fill(hash[nums[0]], 1); // nums[0]的下标为0, 0包含所有的因数(0~k), 因此个数都初始化为1
        for(int i = 1; i < nums.length; i ++){
            int d = gcd(i, k);
            res += hash[nums[i]][k / d]; // 满足nums[i] == nums[j] 且 (i*j)%k==0, j的个数

            getDivisors(nums[i], i);
        }
        return res;
    }
}
```

### 3、线性同余定理

#### （1）[523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)



#### （2）[523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)



#### （3）[560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)



#### （4）[2845. 统计趣味子数组的数目](https://leetcode.cn/problems/count-of-interesting-subarrays/)



### 4、分解质因数

#### （1）[2947. 统计美丽子字符串 I](https://leetcode.cn/problems/count-beautiful-substrings-i/)

**问题**

给你一个字符串 `s` 和一个正整数 `k` 。

用 `vowels` 和 `consonants` 分别表示字符串中元音字母和辅音字母的数量。

如果某个字符串满足以下条件，则称其为 **美丽字符串** ：

- `vowels == consonants`，即元音字母和辅音字母的数量相等。
- `(vowels * consonants) % k == 0`，即元音字母和辅音字母的数量的乘积能被 `k` 整除。

返回字符串 `s` 中 **非空美丽子字符串** 的数量。

子字符串是字符串中的一个连续字符序列。

英语中的 **元音字母** 为 `'a'`、`'e'`、`'i'`、`'o'` 和 `'u'` 。

英语中的 **辅音字母** 为除了元音字母之外的所有字母。

**数据范围**

- `1 <= s.length <= 1000`
- `1 <= k <= 1000`
- `s` 仅由小写英文字母组成。





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
