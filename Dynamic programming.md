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
[解法源网址](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/)  
```
基本思路：要判断 i 到 j 是否回文字符串只需判断 i+1 到 j-1是否回文即可。即 dp[i][j] = (dp[i+1][j-1] && s[i] == s[j]),范围就是当 i+1 到 j-1的距离小于2，那么肯定是回文串，(j-1)-(i+1)+1<2 ,得到 j-i<3, that's all.
```
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
