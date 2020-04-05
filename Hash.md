# 字母异位词
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。  

示例:
```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```
说明：  

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。
```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> result;
        unsigned long n_size = strs.size();
        unordered_map<string, int> m;
        int index = 0;
        
        for (int i = 0; i < n_size; i++) {
            string temp = strs[i];
            sort(temp.begin(), temp.end());
            if (m.count(temp)) {
                result[m[temp]].push_back(strs[i]);
            }
            else {
                m[temp] = index++;
                vector<string> vec;
                vec.push_back(strs[i]);
                result.push_back(vec);
            }
        }
    
        return result;
    }
};
```



















