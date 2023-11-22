

### LeetCode算法竞赛简易实验



##### 实验内容

在[LeetCode算法竞赛平台](https://leetcode.cn/contest/)上，进行一次大语言模型ChatGPT(GPT3.5)与算法编程人员的竞赛实验。

ChatGPT参加[2023年10月14日 22:30的LeetCode双周赛](https://leetcode.cn/contest/biweekly-contest-115/)，算法编程人员参加[2023年10月15日 10:30的单周赛](https://leetcode.cn/contest/weekly-contest-367/)，竞赛时长均为1小时30分钟。



##### **2023年10月14日 22:30的LeetCode双周赛**

###### 题目完成情况和得分情况

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-10-15%20at%2012.31.46.png" alt="Screenshot 2023-10-15 at 12.31.46" style="zoom:50%;" />

全国排名

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-10-15%20at%2012.32.08.png" alt="Screenshot 2023-10-15 at 12.32.08" style="zoom:50%;" />

全球排名

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-10-15%20at%2012.33.10.png" alt="Screenshot 2023-10-15 at 12.33.10" style="zoom:50%;" />



###### **GPT的思考过程**

**题目二** [100078. 最长相邻不相等子序列 I](https://leetcode.cn/problems/longest-unequal-adjacent-groups-subsequence-i/)

第一轮交互

GPT输入：

```c++
给你一个整数 n 和一个下标从 0 开始的字符串数组 words ，和一个下标从 0 开始的 二进制 数组 groups ，两个数组长度都是 n 。

你需要从下标 [0, 1, ..., n - 1] 中选出一个 最长子序列 ，将这个子序列记作长度为 k 的 [i0, i1, ..., ik - 1] ，对于所有满足 0 < j + 1 < k 的 j 都有 groups[ij] != groups[ij + 1] 。

请你返回一个字符串数组，它是下标子序列 依次 对应 words 数组中的字符串连接形成的字符串数组。如果有多个答案，返回任意一个。

子序列 指的是从原数组中删掉一些（也可能一个也不删掉）元素，剩余元素不改变相对位置得到的新的数组。

注意：words 中的字符串长度可能 不相等 。
```

GPT输出：

您可以使用动态规划（Dynamic Programming）来解决这个问题。以下是一个可能的实现示例：

```java
import java.util.*;

public class Solution {

    public String[] longestSubsequence(int n, String[] words, int[] groups) {
        List<List<String>> dp = new ArrayList<>();
        List<Integer> maxLen = new ArrayList<>();
        int maxIdx = -1;

        for (int i = 0; i < n; i++) {
            int group = groups[i];
            int curMaxLen = 0;
            List<String> curList = null;

            for (int j = 0; j < i; j++) {
                if (groups[j] != group && maxLen.get(j) > curMaxLen) {
                    curMaxLen = maxLen.get(j);
                    curList = dp.get(j);
                }
            }

            List<String> newList;
            if (curList == null) {
                newList = new ArrayList<>();
            } else {
                newList = new ArrayList<>(curList);
            }
            newList.add(words[i]);
            dp.add(newList);
            maxLen.add(curMaxLen + 1);

            if (curMaxLen + 1 > maxLen.get(maxIdx)) {
                maxIdx = i;
            }
        }
        List<String> result = dp.get(maxIdx);
        return result.toArray(new String[0]);
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int n = 5;
        String[] words = {"a", "b", "c", "d", "e"};
        int[] groups = {0, 0, 1, 1, 0};
        String[] result = solution.longestSubsequence(n, words, groups);
        System.out.println(Arrays.toString(result));
    }
}

```

在此示例中，您将传递整数 n，一个字符串数组 words 和一个整数数组 groups。该算法将返回一个字符串数组，这个数组是满足条件的最长子序列。



第二轮交互

GPT输入：

```
程序有问题
```

GPT输出：

抱歉，我之前的实现可能存在问题。下面是一个修正后的示例：

```java
import java.util.*;

public class Solution {
    public String[] longestSubsequence(int n, String[] words, int[] groups) {
        List<List<String>> dp = new ArrayList<>();
        List<Integer> maxLen = new ArrayList<>();
        int maxLength = 0;
        int endIndex = -1;

        for (int i = 0; i < n; i++) {
            List<String> maxList = new ArrayList<>();
            for (int j = 0; j < i; j++) {
                if (dp.get(j).size() > maxList.size() && groups[j] != groups[i]) {
                    maxList = new ArrayList<>(dp.get(j));
                }
            }
            maxList.add(words[i]);
            dp.add(maxList);
            if (maxList.size() > maxLength) {
                maxLength = maxList.size();
                endIndex = i;
            }
        }

        List<String> result = dp.get(endIndex);
        return result.toArray(new String[0]);
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int n = 5;
        String[] words = {"a", "b", "c", "d", "e"};
        int[] groups = {0, 0, 1, 1, 0};
        String[] result = solution.longestSubsequence(n, words, groups);
        System.out.println(Arrays.toString(result));
    }
}

```

请注意，这只是一个示例实现，如果您的实际情况更为复杂，可能需要更多的错误检查和边界条件处理。



题目三 [100077. 最长相邻不相等子序列 II](https://leetcode.cn/problems/longest-unequal-adjacent-groups-subsequence-ii/)

GPT输入：

```
给你一个整数 n 和一个下标从 0 开始的字符串数组 words ，和一个下标从 0 开始的 二进制 数组 groups ，两个数组长度都是 n 。

两个长度相等字符串的 汉明距离 定义为对应位置字符 不同 的数目。

你需要从下标 [0, 1, ..., n - 1] 中选出一个 最长子序列 ，将这个子序列记作长度为 k 的 [i0, i1, ..., ik - 1] ，它需要满足以下条件：

相邻 下标对应的 groups 值 不同。即，对于所有满足 0 < j + 1 < k 的 j 都有 groups[ij] != groups[ij + 1] 。
对于所有 0 < j + 1 < k 的下标 j ，都满足 words[ij] 和 words[ij + 1] 的长度 相等 ，且两个字符串之间的 汉明距离 为 1 。
请你返回一个字符串数组，它是下标子序列 依次 对应 words 数组中的字符串连接形成的字符串数组。如果有多个答案，返回任意一个。

子序列 指的是从原数组中删掉一些（也可能一个也不删掉）元素，剩余元素不改变相对位置得到的新的数组。

注意：words 中的字符串长度可能 不相等 。
```

GPT输出：

为了解决这个问题，您可以使用动态规划的方法。下面是一个可能的实现示例，它可以帮助您构建最长子序列。请注意，以下代码仅给出了一个概念性的示例，具体实现可能需要根据实际情况进行调整和修改。

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Solution {
    public String[] longestSubsequence(int n, String[] words, int[] groups) {
        List<Integer>[] dp = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            dp[i] = new ArrayList<>();
            dp[i].add(i);
        }

        int maxLen = 0;
        int endIndex = -1;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (groups[i] != groups[j] && words[i].length() == words[j].length() && hammingDistance(words[i], words[j]) == 1) {
                    if (dp[j].size() + 1 > dp[i].size()) {
                        dp[i] = new ArrayList<>(dp[j]);
                        dp[i].add(i);
                    }
                }
            }
            if (dp[i].size() > maxLen) {
                maxLen = dp[i].size();
                endIndex = i;
            }
        }

        List<String> result = new ArrayList<>();
        for (int index : dp[endIndex]) {
            result.add(words[index]);
        }
        return result.toArray(new String[0]);
    }

    private int hammingDistance(String s1, String s2) {
        int distance = 0;
        for (int i = 0; i < s1.length(); i++) {
            if (s1.charAt(i) != s2.charAt(i)) {
                distance++;
            }
        }
        return distance;
    }
}

```

您可以调用 `longestSubsequence` 方法，并将相应的参数传递给它，它将返回一个字符串数组作为最终的结果。



题目四 [100029. 和带限制的子多重集合的数目](https://leetcode.cn/problems/count-of-sub-multisets-with-bounded-sum/)

第一轮交互

GPT输入：

```
请你返回 nums 中子多重集合的和在闭区间 [l, r] 之间的 子多重集合的数目 。

由于答案可能很大，请你将答案对 109 + 7 取余后返回。

子多重集合 指的是从数组中选出一些元素构成的 无序 集合，每个元素 x 出现的次数可以是 0, 1, ..., occ[x] 次，其中 occ[x] 是元素 x 在数组中的出现次数。

注意：

如果两个子多重集合中的元素排序后一模一样，那么它们两个是相同的 子多重集合 。
空 集合的和是 0 。

测试样例1：
输入：nums = [1,2,2,3], l = 6, r = 6
输出：1

测试样例2：
输入：nums = [2,1,4,2,7], l = 1, r = 5
输出：7

测试样例3：
输入：nums = [1,2,1,3,5,2], l = 3, r = 5
输出：9

测试样例4:
输入：nums = [0,0,1,2,3], l = 2, r = 3
输出：9

数据范围：
1 <= nums.length <= 20000;
0 <= nums[i] <= 20000;
nums 的和不超过 20000;
0 <= l <= r <= 20000;
```

GPT 输出：

这里是一个可能满足你需求的 Java 代码实现：

```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Solution {
    public int countMultisetSum(List<Integer> nums, int l, int r) {
        int MOD = 1000000007;
        int n = nums.size();
        long[][] dp = new long[n + 1][r + 1];

        Map<Integer, Integer> counts = new HashMap<>();
        for (int num : nums) {
            counts.put(num, counts.getOrDefault(num, 0) + 1);
        }

        dp[0][0] = 1;
        int idx = 1;
        for (Map.Entry<Integer, Integer> entry : counts.entrySet()) {
            int val = entry.getKey();
            int freq = entry.getValue();
            for (int i = 0; i <= r; i++) {
                for (int j = 0; j <= freq; j++) {
                    if (i - j * val >= 0) {
                        dp[idx][i] += dp[idx - 1][i - j * val];
                        dp[idx][i] %= MOD;
                    }
                }
            }
            idx++;
        }

        long result = 0;
        for (int i = l; i <= r; i++) {
            result = (result + dp[idx - 1][i]) % MOD;
        }
        return (int) result;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        List<Integer> nums1 = List.of(1, 2, 2, 3);
        int l1 = 6, r1 = 6;
        System.out.println(solution.countMultisetSum(nums1, l1, r1)); // Output: 1

        List<Integer> nums2 = List.of(2, 1, 4, 2, 7);
        int l2 = 1, r2 = 5;
        System.out.println(solution.countMultisetSum(nums2, l2, r2)); // Output: 7

        List<Integer> nums3 = List.of(1, 2, 1, 3, 5, 2);
        int l3 = 3, r3 = 5;
        System.out.println(solution.countMultisetSum(nums3, l3, r3)); // Output: 9

        List<Integer> nums4 = List.of(0, 0, 1, 2, 3);
        int l4 = 2, r4 = 3;
        System.out.println(solution.countMultisetSum(nums4, l4, r4)); // Output: 9
    }
}

```

您可以将此代码复制粘贴到您的 Java 环境中进行测试。



第二轮交互

GPT输入：代码超出内存限制，请进行优化

GPT输出：为了降低内存消耗，您可以尝试使用滚动数组来优化代码。这将有助于减少二维数组的空间占用。以下是经过优化的代码实现：

```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Solution {
    public int countMultisetSum(List<Integer> nums, int l, int r) {
        int MOD = 1000000007;
        int n = nums.size();
        Map<Integer, Integer> counts = new HashMap<>();
        for (int num : nums) {
            counts.put(num, counts.getOrDefault(num, 0) + 1);
        }

        long[] dp = new long[r + 1];
        dp[0] = 1;
        for (Map.Entry<Integer, Integer> entry : counts.entrySet()) {
            int val = entry.getKey();
            int freq = entry.getValue();
            long[] temp = dp.clone();
            for (int i = 0; i <= r; i++) {
                for (int j = 0; j <= freq; j++) {
                    if (i - j * val >= 0) {
                        dp[i] += temp[i - j * val];
                        dp[i] %= MOD;
                    }
                }
            }
        }

        long result = 0;
        for (int i = l; i <= r; i++) {
            result = (result + dp[i]) % MOD;
        }
        return (int) result;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        List<Integer> nums1 = List.of(1, 2, 2, 3);
        int l1 = 6, r1 = 6;
        System.out.println(solution.countMultisetSum(nums1, l1, r1)); // Output: 1

        List<Integer> nums2 = List.of(2, 1, 4, 2, 7);
        int l2 = 1, r2 = 5;
        System.out.println(solution.countMultisetSum(nums2, l2, r2)); // Output: 7

        List<Integer> nums3 = List.of(1, 2, 1, 3, 5, 2);
        int l3 = 3, r3 = 5;
        System.out.println(solution.countMultisetSum(nums3, l3, r3)); // Output: 9

        List<Integer> nums4 = List.of(0, 0, 1, 2, 3);
        int l4 = 2, r4 = 3;
        System.out.println(solution.countMultisetSum(nums4, l4, r4)); // Output: 9
    }
}

```

这个版本应该会降低内存使用，希望能够满足您的要求。

**题目四最终的程序还是存在部分问题**



##### **2023年10月14日 22:30的LeetCode双周赛**

###### 题目完成情况和得分情况

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-10-15%20at%2012.33.31.png" alt="Screenshot 2023-10-15 at 12.33.31" style="zoom:50%;" />

全国排名

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-10-15%20at%2012.34.01.png" alt="Screenshot 2023-10-15 at 12.34.01" style="zoom: 50%;" />

全球排名

<img src="https://raw.githubusercontent.com/JackFroster/Images/main/image/Screenshot%202023-10-15%20at%2012.34.39.png" alt="Screenshot 2023-10-15 at 12.34.39" style="zoom:50%;" />



##### 结论

实验组：昨天LeetCode双周赛，ChatGPT代表我参赛，做对了3道题目，得分12分，最后一道hard题目没有做对；对照组：今天我自己参加LeetCode单周赛，做对了3道题目，得分12分，最后一道hard题目没有做对；结论：我的编程水平是LeetCode的编程水平的下界