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
## 动态规划:  
>基本思路：要判断 i 到 j 是否回文字符串只需判断 i+1 到 j-1是否回文即可。即 dp[i][j] = (dp[i+1][j-1] && s[i] == s[j]),范围就是当 i+1 到 j-1的距离小于2，那么肯定是回文串，(j-1)-(i+1)+1<2 ,得到 j-i<3, that's all.时间和空间都是O(n^2).
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
## 中心扩散算法：  

中心扩散法的思路是：遍历每一个索引,以这个索引为中心,利用“回文串”中心对称的特点,往两边扩散,看最多能扩散多远,时间是O(n^2),空间是O(1).

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
[解法源网址、其中还有 Manacher算法(有兴趣可以看一下)](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/)  
