#  最长回文子串
给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。  
示例 1：
```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```
示例 2：
```
输入: "cbbd"
输出: "bb"
```
## 方法一：Dynamic Programming  
### 1. 状态
```dp[i][j]```表示子串 ```s[i, j]``` 是否为回文子串。
### 2. 状态转移方程
```dp[i][j] = (dp[i + 1][j - 1] && s[i] == s[j])```
### 3.边界情况
当 ```i + 1``` 到 ```j - 1``` 的距离小于2，那么肯定是回文串，也就是 true，```(j - 1) - (i + 1) + 1 < 2``` ,得到 ```j - i < 3```
```c++
string longestPalindrome(string s) {
    int len = s.length();
    if (len < 2) {
        return s;
    }
    bool dp[len][len];
    for (int i=0; i<len; i++) {
        dp[i][i] = true;
    }
    int maxLen = 1;
    int start = 0;
    for (int j=1; j<len; j++) {
        for (int i=0; i<j; i++) {
            if (s[i] == s[j]) {
                if (j - i < 3) {
                    dp[i][j] = true;
                }
                else{
                    dp[i][j] = dp[i+1][j-1];
                }
            }
            else
                dp[i][j] = false;
            
            if (dp[i][j]) {
                int current = j - i + 1;
                if (current > maxLen) {
                    maxLen = current;
                    start = i;
                }
            }
        }
    }
    return s.substr(start,maxLen);
}
``` 
复杂度分析：  
- 时间复杂度：O(n^2)  
- 空间复杂度：O(n^2)
## 方法二：中心扩散算法：  

中心扩散法的思路是：遍历每一个索引,以这个索引为中心,利用“回文串”中心对称的特点,往两边扩散,看最多能扩散多远.

1、如果传入重合的索引编码，进行中心扩散，此时得到的回文子串的长度是奇数；

2、如果传入相邻的索引编码，进行中心扩散，此时得到的回文子串的长度是偶数。

```c++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

class Solution {
private:
    string centerSpread(string s, int left, int right) {
        // left = right 的时候，此时回文中心是一个空隙，向两边扩散得到的回文子串的长度是奇数
        // right = left + 1 的时候，此时回文中心是一个字符，向两边扩散得到的回文子串的长度是偶数
        int size = s.size();
        int i = left;
        int j = right;
        while (i >= 0 && j < size) {
            if (s[i] == s[j]) {
                i--;
                j++;
            } else {
                break;
            }
        }
        // 这里要小心，跳出 while 循环时，恰好满足 s[i] != s[j]，因此不能取 i，不能取 j
        return s.substr(i + 1, j - i - 1);
    }

public:
    string longestPalindrome(string s) {
        // 特判
        int size = s.size();
        if (size < 2) {
            return s;
        }
        int maxLen = 1;
        string res = s.substr(0, 1);

        // 中心位置枚举到 len - 2 即可
        for (int i = 0; i < size - 1; i++) {
            string oddStr = centerSpread(s, i, i);
            string evenStr = centerSpread(s, i, i + 1);
            string maxLenStr = oddStr.size() > evenStr.size() ? oddStr : evenStr;
            if (maxLenStr.length() > maxLen) {
                maxLen = maxLenStr.size();
                res = maxLenStr;
            }
        }
        return res;
    }
};
```
复杂度分析：  
- 时间复杂度：O(n^2)  
- 空间复杂度：O(1)  
## Manacher算法
### [有兴趣可以看一下，理解就好，不要求掌握](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/)
---
# 跳跃游戏
给定一个非负整数数组，你最初位于数组的第一个位置。  

数组中的每个元素代表你在该位置可以跳跃的最大长度。  

判断你是否能够到达最后一个位置。  

示例 1:
```
输入: [2,3,1,1,4]
输出: true
解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。
```
示例 2:
```
输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
```
## 方法一：回溯法
思路：在当前位置(position)的前提下，以```nums[position]```为最大跳跃长度，换句话说，就是一个界限(bound)，从跳```1```步到```bound```步，每个都判断一次```nums[position] + position >= nums.size() - 1```，条件成立即代表可以跳跃成功。
```c++
//方法一：回溯法
class Solution {
private:
    bool flag;
public:
    // 对于跳跃来说，有两个点，一个是当前的位置（position），另一个是下一次跳跃能走的最大步数（nums[position])
    void backtrack(vector<int>& nums, int position) {
        if (nums[position] + position >= nums.size() - 1) {
            flag = true;
            return;
        }
        int bound = nums[position]; // bound 表示在当前下标 index 下可以前进的最大步数
        for (int i = bound; i > 0; i--) {
            backtrack(nums, position + i);
        }
    }

    bool canJump(vector<int>& nums) {

        this->flag = false;
        backtrack(nums, 0);

        if (flag) {
            return true;
        }
        return false;
    }
};
```
复杂度分析：
- 时间复杂度：*O(2^n)*,最多有 2^n 种从第一个位置到最后一个位置的跳跃方式，其中 n 是数组 nums 的元素个数.  
- 空间复杂度：*O(n)*,回溯法只需要栈的额外空间.  
## 方法二：自顶向下的 Dynamic Programming
思路：自顶向下的动态规划可以理解成回溯法的一种优化。我们发现当一个坐标已经被确定为好 / 坏之后，结果就不会改变了，这意味着我们可以记录这个结果，每次不用重新计算。  

因此，对于数组中的每个位置，我们记录当前坐标是好 / 坏，记录在数组 ``memo`` 中，定义元素取值为 GOOD ，BAD，UNKNOWN。这种方法被称为记忆化。

例如，对于输入数组 ```nums = [2, 4, 2, 1, 0, 2, 0]``` 的记忆表如下，G 代表 GOOD，B 代表 BAD。我们发现不能从下标 2，3，4 到达最终坐标 6，但可以从 0，1，5 和 6 到达最终坐标 6。  
|Index|0|1|2|3|4|5|6|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|nums|2|4|2|1|0|2|0|
|memo|G|G|B|B|B|G|G|

**步骤**
初始化 ```memo``` 的所有元素为 UNKNOWN，除了最后一个显然是 GOOD （自己一定可以跳到自己）
优化递归算法，每步回溯前先检查这个位置是否计算过（当前值为：GOOD / BAD）
如果已知直接返回结果 True / False
否则按照之前的回溯步骤计算
计算完毕后，将结果存入```memo```表中
```c++
//方法二：自顶向下的动态规划
class Solution {
private:
    enum Index {
        GOOD,BAD,UNKNOWN
    };
    vector<Index> memo;
public:

    bool canJumpFromPosition(vector<int>& nums, int position) {
        if (memo[position] != UNKNOWN) {
            return memo[position] == GOOD ? true : false;
        }

        int FurthestJump = fmin(position + nums[position], nums.size() - 1);
        for (int i = position + 1; i <= FurthestJump; i++) {
            if (canJumpFromPosition(nums, i)) {
                memo[position] = GOOD;
                return true;
            }
        }
        memo[position] = BAD;
        return false;
    }
    bool canJump(vector<int>& nums) {
        unsigned long n_size = nums.size();
        for (int i = 0; i < n_size; i++) {
            memo.push_back(UNKNOWN);
        }
        memo[n_size - 1] = GOOD;

        return canJumpFromPosition(nums, 0);
    }
};
```
复杂度分析：
- 时间复杂度：*O(2^n)*,数组中的每个元素，假设为 ```i```，需要搜索右边相邻的 ```nums[i]``` 个元素查找是否有 GOOD 的坐标。 ```nums[i]``` 最多为 n, n 是 ```nums``` 数组的大小。  
- 空间复杂度：*O(2n) = O(n)*,第一个 n 是栈空间的开销，第二个 n 是记忆表的开销。  
## 方法三：自底向上的 Dynamic Programming
底向上和自顶向下动态规划的区别就是消除了回溯，在实际使用中，自底向下的方法有更好的时间效率因为我们不再需要栈空间，可以节省很多缓存开销。更重要的事，这可以让之后更有优化的空间。回溯通常是通过反转动态规划的步骤来实现的。  

这是由于我们每次只会向右跳动，意味着如果我们从右边开始动态规划，每次查询右边节点的信息，都是已经计算过了的，不再需要额外的递归开销，因为我们每次在 ```memo``` 表中都可以找到结果。
```c++
//自底向上的动态规划
class Solution {
private:
    enum Index {
        GOOD,BAD,UNKNOW
    };
    vector<Index> memo;
public:

    bool canJump(vector<int>& nums) {
        unsigned long n_size = nums.size();
        for (int i = 0; i < n_size; i++) {
            memo.push_back(UNKNOW);
        }
        memo[n_size - 1] = GOOD;

        for (int i = n_size - 2; i >= 0; i--) {
            int FurthestJump = fmin(i + nums[i], n_size - 1);
            for (int j = i + 1; j <=FurthestJump; j++) {
                if (memo[j] == GOOD) {
                    memo[i] = GOOD;
                    break;
                }
            }
        }

        return memo[0] == GOOD;
    }
};
```
复杂度分析：
- 时间复杂度：*O(2^n)*,数组中的每个元素，假设为 ```i```，需要搜索右边相邻的 ```nums[i]``` 个元素查找是否有 GOOD 的坐标。 ```nums[i]``` 最多为 n，n 是 ```nums``` 数组的大小。  
- 空间复杂度：*O(n)*,记忆表的存储开销。  
## 方法四：贪心算法
当我们把代码改成自底向上的模式，我们会有一个重要的发现，从某个位置出发，我们只需要找到第一个标记为 GOOD 的坐标（由跳出循环的条件可得），也就是说找到最左边的那个坐标。如果我们用一个单独的变量来记录最左边的 GOOD 位置，我们就可以避免搜索整个数组，进而可以省略整个 ```memo``` 数组。

从右向左迭代，对于每个节点我们检查是否存在一步跳跃可以到达 GOOD 的位置（```currPosition + nums[currPosition] >= leftmostGoodIndex```）。如果可以到达，当前位置也标记为 GOOD ，同时，这个位置将成为新的最左边的 GOOD 位置，一直重复到数组的开头，如果第一个坐标标记为 GOOD 意味着可以从第一个位置跳到最后的位置。

模拟一下这个操作，对于输入数组 ```nums = [9, 4, 2, 1, 0, 2, 0]```，我们用 G 表示 GOOD，用 B 表示 BAD 和 U 表示 UNKNOWN。我们需要考虑所有从 0 出发的情况并判断坐标 0 是否是好坐标。由于坐标 1 是 GOOD，我们可以从 0 跳到 1 并且 1 最终可以跳到坐标 6，所以尽管 ```nums[0]``` 可以直接跳到最后的位置，我们只需要一种方案就可以知道结果。
|Index|0|1|2|3|4|5|6|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|nums|9|4|2|1|0|2|0|
|memo|U|G|B|B|B|G|G|

```c++
//贪心算法
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int lastPos = nums.size() - 1;
        for (int i = nums.size() - 1; i >= 0; i--) {
            if (i + nums[i] >= lastPos) {
                lastPos = i;
            }
        }
        return lastPos == 0;
    }
};
```
复杂度分析：  
- 时间复杂度：*O(n)*,只需要访问 ```nums``` 数组一遍，共 n 个位置，n 是 ```nums``` 数组的长度。  
- 空间复杂度：*O(1)*,不需要额外的空间开销。  
---
# 编辑距离
给定两个单词 word1 和 word2，计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符
示例 1:
```
输入: word1 = "horse", word2 = "ros"
输出: 3
解释: 
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```
示例 2:
```
输入: word1 = "intention", word2 = "execution"
输出: 5
解释: 
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```
## 1. 状态
```dp[i][j]``` 代表 word1 到 ```i``` 位置转换成 word2 到 ```j``` 位置需要最少步数
## 2. 状态转移方程
当 ```word1[i] == word2[j]```,```dp[i][j] = dp[i - 1][j - 1]```

当 ```word1[i] != word2[j]```,```dp[i][j] = min(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1]) + 1```

其中,```dp[i-1][j-1]``` 表示替换操作,```dp[i-1][j]``` 表示删除操作,```dp[i][j-1]``` 表示插入操作。

注意，针对第一行，第一列要单独考虑，我们引入 '' 下图所示：
![picture](https://pic.leetcode-cn.com/76574ab7ff2877d63b80a2d4f8496fab3c441065552edc562f62d5809e75e97e-Snipaste_2019-05-29_15-28-02.png)  
第一行，是 ```word1``` 为空变成 ```word2``` 最少步数，就是插入操作

第一列，是 ```word2``` 为空，需要的最少步数，就是删除操作
## 3. 边界情况
第一行（第一列）初始化为其长度（高度），具体看上图。  
```c++
int minDistance(string word1, string word2) {
    // dp[i][j] 表示 word1 的前 i 个字母和 word2 的前 j 个字母之间的编辑距离。
    unsigned long len1 = word1.length(), len2 = word2.length();
    int dp[len1 + 1][len2 + 1];
    for (int i = 0; i < len1 + 1; i++) {
        dp[i][0] = i;
    }
    for (int j = 0; j < len2 + 1; j++) {
        dp[0][j] = j;
    }
    for (int i = 1; i < len1 + 1; i++) {
        for (int j = 1; j < len2 + 1; j++) {
            if (word1[i - 1] == word2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {    //dp[i-1][j-1] 表示替换操作，dp[i-1][j] 表示删除操作，dp[i][j-1] 表示插入操作
                dp[i][j] = fmin(dp[i - 1][j - 1], fmin(dp[i - 1][j], dp[i][j - 1])) + 1;
            }
        }
    }
    return dp[len1][len2];
}
```
---
# 正则表达式匹配
给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。
```
'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素
所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。
```
说明:

- ```s``` 可能为空，且只包含从 ```a-z``` 的小写字母。
- ```p``` 可能为空，且只包含从 ```a-z``` 的小写字母，以及字符 ```.``` 和 ```*```。
示例 1:
```
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```
示例 2:
```
输入:
s = "aa"
p = "a*"
输出: true
解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```
示例 3:
```
输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
```
示例 4:
```
输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
```
示例 5:
```
输入:
s = "mississippi"
p = "mis*is*p*."
输出: false
```
## 1. 状态
```dp[i][j]``` 表示 ```s``` 的前```i```个能否被 ```p``` 的前 ```j``` 个匹配，其实是在比较 ```s[i-1]``` 与 ```p[i-1]```。
## 2. 状态转移方程
### T1. ```s[i - 1] == p[j - 1]```  
```dp[i][j] = dp[i - 1][j - 1]```  
### T2. ```s[i - 1] != p[j - 1]```  
#### (1) ```p[j - 1] == '.'```
```.```是万能字符，相当于```s[i - 1]```的值,```dp[i][j] = dp[i - 1][j - 1]```  
#### (2) ```p[j - 1] == '*'```
首先给了 ```*```，明白 ```*``` 的含义是匹配零个或多个前面的那一个元素，所以要考虑他**前面的元素** ```p[j-2]```。```*``` 跟着他前一个字符走，前一个能匹配上 ```s[i - 1]```，```*``` 才能有用，前一个都不能匹配上 ```s[i - 1]``` ，```*``` 也无能为力，只能让前一个字符消失，也就是匹配 0 次前一个字符。总的来说，对于一个 ```*``` 来说，就是对其前面那个字符匹配 0 次，1 次，多次。
##### 1) ```p[j - 2] != s[i - 1]```
这就是刚才说的那种前一个字符匹配不上的情况。比如```(ab, abc * )```。遇到 ```*``` 往前看两个，发现前面 ```s[i]``` 的 ```ab``` 对 ```p[j-2]``` 的 ```ab``` 能匹配，虽然后面是 ```c*```，但是可以看做匹配 0 次 ```c```，相当于直接去掉 ```c *```  
```dp[i][j] = dp[i][j - 2]```
##### 2) ```p[j-1] == s[i] || p[j-1] == "."```
- 取 0 个字符: ```s: aab, p: aabb*```, 虽然 ```j - 2``` 和 ```i - 1``` 相等, 但是```dp[i][j-2]```已经匹配了, 直接删去```j - 1``` 和 ```j - 2``` 即可（你来之前我们就已经是总冠军了）  
```dp[i][j] = dp[i][j-2]```
- 取 1 个字符: ```s: aab, p: aab*```  
```dp[i][j] = dp[i][j-1]```
- 取多个字符: ```s：###b, p：###b*```,```dp[i-1][j]``` 就是看 ```s``` 里 ```b``` 多不多，```###``` 和 ```###b*``` 是否匹配，一旦匹配，```s``` 后面再添个 ```b``` 也不影响，因为有 ```*``` 在，也就是 ```###b``` 和 ```###b*```也会匹配  
```dp[i][j] = dp[i - 1][j]```  

以上三者满足一种就能匹配上。  

```c++
bool isMatch(string s, string p) {
    if (s == "" && p == "") {
        return true;
    }
    unsigned long m = s.length(), n = p.length();
    bool dp[m + 1][n + 1];
    memset(dp, false, sizeof(dp));
    dp[0][0] = true;
    for (int j = 1; j <= n; j++) {
        if ((p[j - 1] == '*') && dp[0][j - 2]) {
            dp[0][j] = true;
        }
    }
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (s[i - 1] == p[j - 1] || p[j - 1] == '.') {
                dp[i][j] = dp[i - 1][j - 1];
            }
            else
                if (p[j - 1] == '*') {
                    if (p[j - 2] == s[i - 1] || p[j - 2] == '.') {  //三者满足其一即可
                        dp[i][j] = dp[i - 1][j] || dp[i][j - 1] || dp[i][j-2];
                    }
                    else
                        dp[i][j] = dp[i][j-2];  //p[j - 2] != s[i - 1]
                }
         }
    }
    return dp[m][n];
}
```
#### 还有[类似的更简单的题目](https://leetcode-cn.com/problems/wildcard-matching/)
---
# 最长上升子序列
给定一个无序的整数数组，找到其中最长上升子序列的长度。  

示例:
```
输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
```
## 方法一：basic dynamic programming
### 状态
```dp[i]``` 表示在区间 ```[0, i)``` 中的最大上升子序列长度.  
### 状态转移方程
在 ```nums[j] < nums[i] (j < i)``` 的前提下在区间 ```[0, i)``` 中找到 ```dp``` 中的一个最大的数，满足 ```dp[i] = dp[j] + 1``` 。 
### 边界情况
没有边界情况，每次循环都是从头开始遍历。 
```c++
int lengthOfLIS(vector<int>& nums) {
    if (nums.empty()) {
        return 0;
    }
    vector<int> dp(nums.size(), 1);
    for (int i = 1; i < nums.size(); i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                if (dp[j] >= dp[i]) {
                    dp[i] = dp[j] + 1;
                }
            }
        }
    }
    int max = 1;
    for (auto v : dp) {
        if (v > max) {
            max = v;
        }
    }
    return max;
}
```
复杂度分析：
- 时间复杂度：O(N^2) ： 遍历计算 ```dp``` 列表需 O(N)，计算每个 ```dp[i]``` 需 O(N)。  
- 空间复杂度：```dp``` 列表占用线性大小额外空间。  
## 方法二：dynamic programming + dichotomy(二分法)
### 状态
我们考虑维护一个新的列表 ```tails```，其中每个元素 ```tails[k]``` 的值代表长度为 ```k + 1``` 的子序列尾部元素的值（太抽象了吧，其实就是把结果数组给求出来而已）
### 状态转移方程
- 设常量数字 ```N```，和随机数字 ```x```，我们可以容易推出：当 ```N``` 越小时，```N < x``` 的几率越大。例如： ```N = 0``` 肯定比 ```N = 1000``` 更可能满足 ```N < x```。
- 在遍历计算每个 ```tails[k]```，不断更新长度为 ```[1,k]``` 的子序列尾部元素值，始终保持**每个尾部元素值最小**
- ```tails``` 列表一定是严格递增的： 即当尽可能使每个子序列尾部元素值最小的前提下，子序列越长，其序列尾部元素值一定更大
- 由于 ```tails``` 是递增的，所以查找比当前元素 ```nums[i]``` 小的最大元素的位置时使用二分法可以将时间复杂度从 O(N) 降低为 O(log(N)) 
- 初始化数组为 0  

举些例子（在每一次循环中的 ```tails``` 列表改变过程）

示例1:
```
输入：10 9 2 5 3 7 101 18
tails:
The 1th turn: 10 0 0 0 0 0 0 
The 2th turn: 9 0 0 0 0 0 0 
The 3th turn: 2 0 0 0 0 0 0 
The 4th turn: 2 5 0 0 0 0 0 
The 5th turn: 2 3 0 0 0 0 0 
The 6th turn: 2 3 7 0 0 0 0 
The 7th turn: 2 3 7 101 0 0 0 
```
示例2:
```
输入：3 5 6 2 5 4 19 5 6 7 12
tails:
The 1th turn: 3 0 0 0 0 0 0 0 0 0 0 
The 2th turn: 3 5 0 0 0 0 0 0 0 0 0 
The 3th turn: 3 5 6 0 0 0 0 0 0 0 0 
The 4th turn: 2 5 6 0 0 0 0 0 0 0 0 
The 5th turn: 2 5 6 0 0 0 0 0 0 0 0 
The 6th turn: 2 4 6 0 0 0 0 0 0 0 0 
The 7th turn: 2 4 6 19 0 0 0 0 0 0 0 
The 8th turn: 2 4 5 19 0 0 0 0 0 0 0 
The 9th turn: 2 4 5 6 0 0 0 0 0 0 0 
The 10th turn: 2 4 5 6 7 0 0 0 0 0 0 
The 11th turn: 2 4 5 6 7 12 0 0 0 0 0 
```
### 边界情况
没有边界情况。 
```c++
int lengthOfLIS(vector<int>& nums) {
    int res = 0;
    vector<int> tails(nums.size(), 0);
    for (auto v : nums) {
        int i = 0, j = res;
        //find the index of the biggest element but less than "v"
        while (i < j) {
            int m = (i + j) / 2;
            if (tails[m] < v) {
                i = m + 1;
            }  else  j = m;
        }
        tails[i] = v;
        if (res == j) {
            res++;
        }
    }
    return res;
}
```
复杂度分析：
- 时间复杂度：O(nlogn)。数组 ```nums``` 的长度为 n，我们依次用数组中的元素去更新 ```tails``` 数组，而更新 ```tails``` 数组时需要进行 O(logn) 的二分搜索，所以总时间复杂度为 O(nlogn)。
- 空间复杂度：O(n)，需要额外使用长度为 ```n``` 的 ```tails``` 数组。
---
# 最长有效括号
给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。

示例 1:
```
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
```
示例 2:
```
输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"
```
## 方法一：暴力法(exceed time)
Algorithm:
将每一种子串都判断是否符合括号匹配模式。  
```c++
bool isVaild(string s) {
    stack<char> a;
    for (auto v : s) {
        if (v == '(') {
            a.push(v);
        } else if (!a.empty() && a.top() == '(') {
            a.pop();
        } else return false;
    }
    return a.empty();
}

int longestValidParentheses(string s) {
    int maxLen = 0;
    for (int i = 0; i < s.length(); i++) {
        for (int j = 2; j + i <= s.length(); j += 2) {
            if (isVaild(s.substr(i, j))) {
                if (j > maxLen) {
                    maxLen = j;
                }
            }
        }
    }
    return maxLen;
}
```
复杂度分析：  
- 时间复杂度：O(n^2)。从长度为 n 的字符串产生所有可能的子字符串需要的时间复杂度为 O(n^2)。验证一个长度为 n 的子字符串需要的时间为 O(n) 。
- 空间复杂度： O(n) 。子字符串的长度最多会需要一个深度为 n 的栈。
## 方法二：Dynamic programming
### 状态
```dp[i]``` 表示以下标 ```i``` 为字符结尾的最长有效字符串的长度
### 状态转移方程
以 ```(``` 结尾的子字符串不考虑，因为不可能构成合法括号
#### if ```s[i] == ')'```
1. ```s[i - 1] == '('```，也就是字符串形如 ```“……()”```，我们可以推出：  
```dp[i] = dp[i − 2] + 2```。   
因为结束部分的 "()" 是一个有效子字符串，并且将之前有效子字符串的长度增加了 2.  
2. ```s[i - 1] == ')'```，也就是字符串形如 ```“.......))”```，我们可以推出：  
if ```s[i - dp[i - 1] - 1] == '('```，```dp[i] = dp[i − 1] + dp[i − dp[i − 1] − 2] + 2```。  
因为如果倒数第二个 ```)```是一个有效子字符串的一部分（记为```subs```），我们此时需要判断 ```subs``` 前面一个符号是不是 ```(``` ，如果恰好是```(```，我们就用 ```subs``` 的长度(```dp[i - 1```)加上 2 去更新 ```dp[i]```。除此以外，我们也会把子字符串 ```subs``` 前面的有效字符串的长度加上，也就是 ```dp[i − dp[i − 1] − 2]```.  
### 边界情况
- ```i - 2``` 有可能小于零越界了，这种情况下就是只有 ```()``` ，前面记为 0 就好了.  
- ```i - dp[i - 1] - 1``` 和 ```i - dp[i - 1] - 2``` 都可能越界，越界了当成 0 来计算就可以了.  
```c++
int longestValidParentheses(string s) {
    int maxLen = 0;
    vector<int> dp(s.length());
    for (int i = 1; i < s.length(); i++) {
        if (s[i] == ')') {
            if (s[i - 1] == '(') {
                dp[i] = (i - 2 >= 0 ? dp[i - 2] : 0) + 2;
            } else if (i - dp[i - 1] - 1 >= 0 and s[i - dp[i - 1] - 1] == '(') {
                dp[i] = (i - dp[i - 1] - 2 >= 0 ? dp[i - dp[i - 1] - 2] : 0) + dp[i - 1] + 2;
            }
        }
        maxLen = fmax(dp[i], maxLen);
    }
    return maxLen;
}
```
复杂度分析：  
- 时间复杂度：O(n)。遍历整个字符串一次，就可以将 ```dp``` 数组求出来.  
- 空间复杂度： O(n)。需要一个大小为 ```n``` 的 ```dp``` 数组.  

# 鸡蛋掉落
你将获得 ```K``` 个鸡蛋，并可以使用一栋从 ```1``` 到 ```N```  共有 ```N``` 层楼的建筑。

每个蛋的功能都是一样的，如果一个蛋碎了，你就不能再把它掉下去。

你知道存在楼层 ```F``` ，满足 ```0 <= F <= N``` 任何从高于 ```F``` 的楼层落下的鸡蛋都会碎，从 ```F``` 楼层或比它低的楼层落下的鸡蛋都不会破。

每次移动，你可以取一个鸡蛋（如果你有完整的鸡蛋）并把它从任一楼层 ```X``` 扔下（满足 ```1 <= X <= N```）。

你的目标是确切地知道 ```F``` 的值是多少。

无论 ```F``` 的初始值如何，你确定 ```F``` 的值的最小移动次数是多少？

示例 1：
```
输入：K = 1, N = 2
输出：2
解释：
鸡蛋从 1 楼掉落。如果它碎了，我们肯定知道 F = 0 。
否则，鸡蛋从 2 楼掉落。如果它碎了，我们肯定知道 F = 1 。
如果它没碎，那么我们肯定知道 F = 2 。
因此，在最坏的情况下我们需要移动 2 次以确定 F 是多少。
```
示例 2：
```
输入：K = 2, N = 6
输出：3
```
示例 3：
```
输入：K = 3, N = 14
输出：4
```

提示：
```
1 <= K <= 100
1 <= N <= 10000
```
## 动态规划 + 二分搜索
过于经典的面试题，读题困难，菜是原罪。请看 [官方题解](https://leetcode-cn.com/problems/super-egg-drop/solution/ji-dan-diao-luo-by-leetcode-solution/)  
```c++
unordered_map<int, int> memo;
int dp(int K, int N) {
    if (memo.find(N * 100 + K) == memo.end()) {
        int ans;
        if (N == 0) {
            ans = 0;
        } else if (K == 1) {
            ans = N;
        } else {
            int lo = 1, hi = N;
            while (lo + 1 < hi) {
                int x = (lo + hi) / 2;
                int t1 = dp(K - 1, x - 1);//下层
                int t2 = dp(K, N - x);//上层
                if (t1 < t2) {
                    lo = x;
                } else if (t1 > t2) {
                    hi = x;
                } else lo = hi = x;
            }
            ans = 1 + fmin(fmax(dp(K - 1, lo - 1), dp(K, N - lo)),
                           fmax(dp(K - 1, hi - 1), dp(K, N - hi)));
        }
        memo[N * 100 + K] = ans;
    }
    return memo[N * 100 + K];
}

int superEggDrop(int K, int N) {
    return dp(K, N);
}
```































