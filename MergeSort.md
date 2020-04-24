# [数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

示例 1:
```
输入: [7,5,6,4]
输出: 5
```
官方题解还有[视频](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/shu-zu-zhong-de-ni-xu-dui-by-leetcode-solution/)的，超级清晰，易懂，冲冲冲。
```c++
int mergeAndCount(vector<int>& nums, vector<int>& temp, int left, int mid, int right) {
    for (int i = left; i <= right; i++) {
        temp[i] = nums[i];
    }
    int i = left;
    int j = mid + 1;
    int count = 0;
    for (int k = left; k <= right; k++) {
        if (i == mid + 1) {
            nums[k] = temp[j];
            j++;
        } else if (j == right + 1) {
            nums[k] = temp[i];
            i++;
        } else if (temp[i] <= temp[j]) {
            nums[k] = temp[i];
            i++;
        } else {
            nums[k] = temp[j];
            j++;
            count += (mid - i + 1);
        }
    }
    return count;
}

int reversePairs(vector<int>& nums, vector<int>& temp, int left, int right) {
    
    if (left == right) {
        return 0;
    }
    
    int mid = left + (right - left) / 2;
    
    int LeftPairs = reversePairs(nums, temp, left, mid);
    int RightPairs = reversePairs(nums, temp, mid + 1, right);
    
    if (nums[mid] <= nums[mid + 1]) {
        return LeftPairs + RightPairs;
    }
    
    int CrossPairs = mergeAndCount(nums, temp, left, mid, right);
    
    return LeftPairs + RightPairs + CrossPairs;
}

int reversePairs(vector<int>& nums) {
    if (nums.size() < 2) {
        return 0;
    }
    vector<int> temp(nums.size());
    return reversePairs(nums, temp, 0, nums.size() - 1);
}
```









































