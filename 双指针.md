# 三数之和
给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 ```a，b，c``` ，使得 ```a + b + c = 0``` ？找出所有满足条件且不重复的三元组。  
注意：答案中不可以包含重复的三元组。  
示例：  
```
给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```
## 双指针
基本思路：
1. 将数组排序
2. 从头到尾遍历数组，每次遍历确定一个基准值，也就是 target ，每次遍历中都遍历一次比这个下标值之后的元素一次，用双指针只想后续数组的头和尾，比较大小来移动头指针或者尾指针
```c++
vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>>ans;
        sort(nums.begin(),nums.end());
        if (nums.size() < 3 || nums[0] > 0 || nums[nums.size()-1] < 0) {
            return ans;
        }
        for (int i=0; i<nums.size(); i++) {
            if (nums[i] > 0) {
                break;
            }
            if (i > 0 && nums[i] == nums[i-1]) {
                continue;
            }
            int target = 0 - nums[i];
            int j = i + 1;
            unsigned long m = nums.size() - 1;
            while (j < m) {
                if (nums[j] > target) {
                    break;
                }
                if ((j > i + 1 && nums[j] == nums[j-1]) || nums[j] + nums[m] < target) {
                    j++;
                }
                else
                    if ((m < nums.size() - 1 && nums[m] == nums[m+1]) || nums[j] + nums[m] > target) {
                        m--;
                    }
                    else {
                        vector<int>res;
                        res.push_back(nums[i]);
                        res.push_back(nums[j]);
                        res.push_back(nums[m]);
                        ans.push_back(res);
                        j++;
                    }
            }
        }
        return ans;
    }
```
