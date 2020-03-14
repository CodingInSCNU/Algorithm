# vector基本用法
## 构造函数
```
vector<int> a;                          //构造一个空的vector

vector<int> a(n);                       //构造一个空间大小为 n 的vector，初始化为 0

vector<int> a(n, val);                  //构造一个空间大小为 n 的vector，初始化为 val
```
## 基础函数
```
a.assign(num, val);                     //清除原 vector 数组中的元素，重新加入 num 个值为 val 的元素

a.at(n);                                //返回指定位置下标为 n 的元素的引用

a.front();                              //返回当前vector起始元素的引用

a.back();                               //返回当前vector最末元素的引用

a.begin();                              //返回当前vector起始元素的迭代器

a.end();                                //返回当前vector最末元素的下一位置的迭代器

a.capacity();                           //返回当前vector在重新进行内存分配以前所能容纳的元素数量

a.size();                               //返回当前vector所容纳元素的数目

a.clear();                              //删除当前vector中的所有元素

a.empty();                              //如果当前vector没有容纳任何元素,则empty()函数返回true,否则返回false

a.erase(iterator loc);                  //删除当前vector指定位置 loc 的元素，a.erase(a.begin() + i)也就是删除 a[i]

a.erase(iterator start, iterator end);  //删除区间在 [start, end) 中的元素

a.insert(iterator loc, val);            //在指定位置loc前插入值为val的元素,返回指向这个元素的迭代器

a.insert(iterator loc, num, val);       //在指定位置loc前插入num个值为val的元素

a.max_size();                           //函数返回当前vector所能容纳元素数量的最大值(包括可重新分配内存)

a.rbegin();                             //rbegin函数返回指向当前vector末尾的逆迭代器(实际指向末尾的下一位置,而其内容为末尾元素的值)

a.rend();                               //rend()函数返回指向当前vector起始位置的逆迭代器

a.resize(size, val);                    //改变当前vector的大小为size,且对新创建的元素赋值val
```
---
# 合并两个数组
给定两个大小为 m 和 n 的有序数组 ```nums1``` 和 ```nums2```. 

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n)). 

你可以假设 ```nums1``` 和 ```nums2``` 不会同时为空。

示例 1:
```
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
```
示例 2:
```
nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```
以下算法不符合题目要求，具体解法 [click here](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xun-zhao-liang-ge-you-xu-shu-zu-de-zhong-wei-shu-b/)
```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        vector<int>a;
        a.resize(nums1.size() + nums2.size());
        //合并两个数组（两个数组分别一定要排好序）
        merge(nums1.begin(),nums1.end(),nums2.begin(),nums2.end(),a.begin());
        if(a.size()%2 == 0){
            int mid = a.size()/2;
            double m = (a[mid-1] + a[mid]) / 2.0;
            return m;
        }
        else{
            int mid = a.size()/2;
            return a[mid];
        }
    }
};
```

























