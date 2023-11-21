# Dive Into LeetCode’s Algorithm

<div align="center">
  <img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-11-17%20at%2013.04.48.png" alt="Screenshot 2023-11-17 at 13.04.48" width = "200px" align= "center"/>
</div>


> 引言
>
> 此 NoteBook For LeetCode (Version 1) 记录了LeetCode算法题解，包括**线段树、树状数组、数学、哈希计数、模拟、双指针、字符串、拓扑排序、排序、查找、树、前缀和与后缀和、动态规划（线性DP,树形DP,状态压缩DP,数位DP,状态机DP）、最短路、数论、贪心、Tire树、递归与回溯、链表、枚举、进制转换、单调栈、BFS、DFS、并查集、Topk问题、最大最小问题、最小最大问题、滑动窗口算法、二分、稀疏表、Tarjan 算法、FloodFill、Kadane's algorithm、KMP、扫描线**等。
>
> :man:：`Jackson Dean`	:timer_clock: ：`2022-02-04`  :pencil2:：`c/c++/java/python`  :gear:：[`Github`](https://github.com/JackFroster/JF-Notes)  :information_source:: [`LeetCode`](https://leetcode.cn/)  

# 第一部分之进制转换

### 1、进制转换

#### （1）[1073. 负二进制数相加](https://leetcode.cn/problems/adding-two-negabinary-numbers/)

**问题**：

给出基数为 **-2** 的两个数 `arr1` 和 `arr2`，返回两数相加的结果。

数字以 *数组形式* 给出：数组由若干 0 和 1 组成，按最高有效位到最低有效位的顺序排列。例如，`arr = [1,1,0,1]` 表示数字 `(-2)^3 + (-2)^2 + (-2)^0 = -3`。*数组形式* 中的数字 `arr` 也同样不含前导零：即 `arr == [0]` 或 `arr[0] == 1`。

返回相同表示形式的 `arr1` 和 `arr2` 相加的结果。两数的表示形式为：不含前导零、由若干 0 和 1 组成的数组。

**数据范围**

- `1 <= arr1.length, arr2.length <= 1000`
- `arr1[i]` 和 `arr2[i]` 都是 0 或 1
- `arr1` 和 `arr2` 都没有前导0

例：

```
输入：arr1 = [1,1,1,1,1], arr2 = [1,0,1]
输出：[1,0,0,0,0]
解释：arr1 表示 11，arr2 表示 5，输出表示 16 。
```

**思路**：

二进制转换成十进制，十进制相加，再转换为二进制

```java
class Solution {
    public int[] addNegabinary(int[] arr1, int[] arr2) {
        int a = 0;
        int b = 0;
        // 二进制转十进制
        for(int i = 0; i < arr1.length; i ++ )
           if(arr1[i] == 1) a += Math.pow(-2, arr1.length - 1 - i);

        for(int i = 0; i < arr2.length; i ++ )
           if(arr2[i] == 1) b += Math.pow(-2, arr2.length - 1 - i);

        int sum = a + b; 
        
        if(sum == 0) return new int[]{0};
        // 十进制转二进制
        ArrayList<Integer> res = new ArrayList<>();
        while(sum != 0) {
            int t = 0;
            if(sum < 0) {
                t = (sum - 1) / (-2);
            } else {
                t = sum / (-2);
            }
            res.add(sum - (-2 * t));
            sum = t;
        }
        Collections.reverse(res);
        return res.stream().mapToInt(t -> t).toArray();
    }
}
```

考虑数据范围，使用Java的BigInteger类，防止数据溢出：

```java
import java.math.BigInteger;

class Solution {
    
    private BigInteger binConvertToDec(int[] arr) {
        BigInteger x = new BigInteger("0");
        BigInteger base = new BigInteger("-2");
        for(int i = 0; i < arr.length; i ++ ) {
            if(arr[i] == 1) x = x.add(base.pow(arr.length - 1 - i));
        }
        return x;
    }
    
    private int[] decConvertToBin(BigInteger x) {
        BigInteger zero = new BigInteger("0"); 
        BigInteger one = new BigInteger("1");
        BigInteger base = new BigInteger("-2");
        ArrayList<Integer> res = new ArrayList<>();
        while(!x.equals(zero)) {
            BigInteger t = new BigInteger("0");
            if(x.compareTo(zero) < 0) {
                t = x.subtract(one).divide(base);
            } else {
                t = x.divide(base);
            }
            res.add(x.subtract(base.multiply(t)).intValue());
            x = t;
        }
        Collections.reverse(res);
        return res.stream().mapToInt(t -> t).toArray();
    }
    
    public int[] addNegabinary(int[] arr1, int[] arr2) {
        BigInteger a = binConvertToDec(arr1);
        BigInteger b = binConvertToDec(arr2);

        if(a.add(b).equals(new BigInteger("0"))) return new int[]{0};

        return decConvertToBin(a.add(b));
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
