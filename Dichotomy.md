# Binarysearch concrete methods 
- [full details](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/er-fen-cha-zhao-suan-fa-xi-jie-xiang-jie-by-labula/)
# 搜索旋转排序数组
假设按照升序排序的数组在预先未知的某个点上进行了旋转。  

( 例如，数组 ```[0,1,2,4,5,6,7]``` 可能变为 ```[4,5,6,7,0,1,2]```)。  

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。  

你可以假设数组中不存在重复的元素。  

你的算法时间复杂度必须是 O(log n) 级别。  

示例1:
```
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```
示例2:
```
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```
## 二分查找
思路：找出旋转的节点将数组分成两个部分，判断大小用二分法搜索对应数组
```c++
class Solution {
public:
    vector<int>nums;
    int target;
    
    int find_rotate_index(int left, int right) {
        if (nums[left] < nums[right]) {
            return 0;
        }
        while (left <= right) {
            int pivot = (left + right) / 2;
            if (nums[pivot] > nums[pivot+1])
                return pivot + 1;
            else {
                if (nums[pivot] < nums[left])
                    right = pivot - 1;
                else
                    left = pivot + 1;
            }
        }
        return 0;
    }
    
    int search(int left, int right) {
        while (left <= right) {
            int pivot = (left + right) / 2;
            if (nums[pivot] == target) {
                return pivot;
            }
            else {
                if (target < nums[pivot]) {
                    right = pivot - 1;
                }
                else
                    left = pivot + 1;
            }
        }
        return -1;
    }
    
    int search(vector<int>& nums, int target) {
        this->nums = nums;
        this->target = target;
        int len = nums.size();
        if (len == 0) {
            return -1;
        }
        if (len == 1) {
            return this->nums[0] == target ? 0 : -1;
        }
        int rotate_index = find_rotate_index(0, len - 1);
        if (nums[rotate_index] == target) {
            return rotate_index;
        }
        if (rotate_index == 0) {
            return search(0, len - 1);
        }
        if (target < nums[0]) {
            return search(rotate_index, len - 1);
        }
        return search(0, rotate_index);
    }
};
```
# 在排序数组中查找元素的第一个和最后一个位置
给定一个按照升序排列的整数数组 ```nums```，和一个目标值 ```target```。找出给定目标值在数组中的开始位置和结束位置。  

你的算法时间复杂度必须是 O(log n) 级别。  

如果数组中不存在目标值，返回 ```[-1, -1]```。  

示例1:
```
输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
```
示例2:
```
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]
```
## 二分查找
思路：使用 int search_left(vector<int>& nums, int target) 和 int search_right(vector<int>& nums, int target) 函数把 target 的最左和最右边界的坐标找出来，找不到则返回 -1.
```c++
class Solution {
private:
    vector<int>nums;
    int target;
public:
    
    int search_left(vector<int>& nums, int target) {
        
        if (nums.size() == 0) {
            return -1;
        }
        int left = 0, right = nums.size();
        //终止条件为 left == right，区间为 [left, right).
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] == target) {
                right = mid;
            }
            else
                if (nums[mid] < target) {
                    left = mid + 1;
                }
            else
                if (nums[mid] > target) {
                    right = mid;
                }
        }
        if (left == nums.size()) {
            return -1;
        }
        
        return nums[left] == target ? left : -1;
    }
    
    int search_right(vector<int>& nums, int target) {
        
        if (nums.size() == 0) {
            return -1;
        }
        int left = 0, right = nums.size();
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] == target) {
                left = mid + 1;
            }
            else
                if (nums[mid] < target) {
                    left = mid + 1;
                }
            else
                if (nums[mid] > target) {
                    right = mid;
                }
        }
        if (left == 0) { // think: why?
            return -1;
        }
        //think: why is (left - 1) ?
        return nums[left - 1] == target ? left - 1 : -1;
    }
    
    vector<int> searchRange(vector<int>& nums, int target) {
    
        this->nums = nums;
        this->target = target;
        
        if (nums.size() == 0) {
            return {-1, -1};
        }
        int left = search_left(nums, target);
        int right = search_right(nums, target);
        return {left, right};
    }
};
```






