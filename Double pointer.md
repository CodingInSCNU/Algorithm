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
# 接雨水
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。  
![p](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)  
示例:
```
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6
```
## 暴力法(按列计算)
**直观想法**
直接按问题描述进行。对于数组中的每个元素，我们找出下雨后水能达到的最高位置，等于两边最大高度的较小值减去当前高度的值。

算法：
- 初始化 ```ans = 0```.  
- 从左向右扫描数组
  - 初始化 ```max_left = 0``` 和 ```max_right = 0```.  
  - 从当前元素向左扫描并更新：```max_left = fmax(max_left, height[j])```.  
  - 从当前元素向右扫描并更新：```max_right = fmax(max_right, height[j])```.  
  - 将 ```fmin(max_left, max_right) - height[i]``` 累加到 ```ans```.  

```c++
int trap(vector<int>& height) {
    int ans = 0;
    int max_left, max_right;
    for (int i = 1; i < height.size(); i++) {
        max_left = max_right = 0;
        //找出 height[i] 左边最高的柱子
        for (int j = i; j >= 0; j--) {
            max_left = fmax(max_left, height[j]);
        }
        //找出 height[i] 右边最高的柱子
        for (int j = i; j < height.size(); j++) {
            max_right = fmax(max_right, height[j]);
        }
        //找出两根柱中的最小值，然后与当前的位置(height[i])高度比较，然后可以得出 height[i] 头上有多少体积的水
        ans += fmin(max_left, max_right) - height[i];
        cout << ans << " ";
    }
    cout << endl;
    return ans;
}
```
复杂性分析
- 时间复杂度：O(n^2),数组中的每个元素都需要向左向右扫描.  
- 空间复杂度：O(1).  

## 动态规划(按列计算，暴力法的改进)
状态：
```max_left[i]``` 和 ```max_right[i]``` 表示以下标为 ```i``` 向左或者向右的最大柱子高度。 

状态转移方程：
```max_left[i]```：```max_left[i] = fmax(height[i], max_left[i - 1])```.  
```max_right[i]```：```max_right[i] = fmax(height[i], max_right[i + 1])```.  
注意右数组是从尾部开始遍历的

边界情况：
```i - 1``` 和 ```i + 1``` 可能越界，先填好首尾的值，再遍历即可。 

```c++
int trap(vector<int>& height) {
    if (height.empty()) {
        return 0;
    }
    int ans = 0;
    vector<int> max_left(height.size());
    vector<int> max_right(height.size());
    max_left[0] = height[0];
    for (int i = 1; i < height.size(); i++) {
        max_left[i] = fmax(height[i], max_left[i - 1]);
    }
    max_right[height.size() - 1] = height[height.size() - 1];
    for (int i = height.size() - 2; i >= 0; i--) {
        max_right[i] = fmax(height[i], max_right[i + 1]);
    }
    for (int i = 1; i < height.size() - 1; i++) {
        ans += fmin(max_left[i], max_right[i]) - height[i];
    }
    return ans;
}
```
复杂度分析
- 时间复杂度：O(n)
  - 存储最大高度数组，需要两次遍历，每次 O(n).  
  - 最终使用存储的数据更新 ```ans```, O(n).  
- 空间复杂度：O(n), 使用额外数组空间  

## 栈的应用(按行计算)
动图演示：
































