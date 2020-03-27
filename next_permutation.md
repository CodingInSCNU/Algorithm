# 下一个排列
实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
```1,2,3``` → ```1,3,2```
```3,2,1``` → ```1,2,3```
```1,1,5``` → ```1,5,1```
## 思路：  
1.从后向前查找第一个相邻升序的元素对 ```(i, i+1)```，满足 ```A[i] < A[i+1]```。此时 ```[i+1,end)``` 必然是降序.  
2.在 ```[i+1,end)``` 从后向前查找第一个满足 ```A[i] < A[j]``` 的 ```j```.  
3.将 ```A[i]``` 与 ```A[j]``` 交换.  
4.可以断定这时 ```[i+1,end)``` 必然是降序，逆置 ```[i+1,end)```，使其升序.  
5.如果在步骤 1 找不到符合的相邻元素对，说明当前 ```[begin,end)``` 为一个降序顺序，则直接跳到步骤 4.  
```c++
void swap(vector<int>&nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}

void reverse(vector<int>&nums, int start) {
    int i = start, j = nums.size() - 1;
    while (i < j) {
        swap(nums, i, j);
        i++;
        j--;
    }
}

void nextPermutation(vector<int>& nums) {
    int i = nums.size() - 2;
    //找到第一个升序对
    while (i >= 0 && nums[i] >= nums[i+1]) {
        i--;
    }
    if (i >= 0) {
        int j = nums.size() - 1;
        //从右到左找到第一个比 nums[i] 大的值并交换
        while (j >= 0 && nums[j] <= nums[i]) {
            j--;
        }
        swap(nums, i, j);
    }
    //反转 i 后面的数组(因为后面的数组一定是降序，反转成升序即为最小的下一个排列)
    reverse(nums, i + 1);
}
```
# next_permutation
```c++
vector<vector<int>> permute(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    do {
        result.push_back(nums);
    }while (next_permutation(nums.begin(), nums.end()));

    return result;
}
```




