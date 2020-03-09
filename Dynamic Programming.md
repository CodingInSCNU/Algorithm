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
## Dynamic Programming  
基本思路：要判断 i 到 j 是否回文字符串只需判断 i+1 到 j-1是否回文即可。即 ```dp[i][j] = (dp[i+1][j-1] && s[i] == s[j])```,范围就是当 ```i+1``` 到 ```j-1``` 的距离小于2，那么肯定是回文串，```(j-1)-(i+1)+1<2``` ,得到 ```j-i<3```, that's all.
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
## 中心扩散算法：  

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
## 自顶向下的 Dynamic Programming
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
## 自底向上的 Dynamic Programming
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
## 贪心算法
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
## Dynamic Programming
dp[i][j] 代表 word1 到 i 位置转换成 word2 到 j 位置需要最少步数

所以，

当 ```word1[i] == word2[j]```,```dp[i][j] = dp[i-1][j-1]```

当 ```word1[i] != word2[j]```,```dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1]) + 1```

其中,```dp[i-1][j-1]``` 表示替换操作,```dp[i-1][j]``` 表示删除操作,```dp[i][j-1]``` 表示插入操作。

注意，针对第一行，第一列要单独考虑，我们引入 '' 下图所示：
![picture](https://pic.leetcode-cn.com/76574ab7ff2877d63b80a2d4f8496fab3c441065552edc562f62d5809e75e97e-Snipaste_2019-05-29_15-28-02.png)  
第一行，是 ```word1``` 为空变成 ```word2``` 最少步数，就是插入操作

第一列，是 ```word2``` 为空，需要的最少步数，就是删除操作

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











