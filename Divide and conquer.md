# [合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)
合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

示例:
```
输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6
```

```c++
ListNode* mergeList(ListNode* head1, ListNode* head2) {
    if (!head1) {
        return head2;
    }
    if (!head2) {
        return head1;
    }
    if (head1->val <= head2->val) {
        head1->next = mergeList(head1->next, head2);
        return head1;
    } else {
        head2->next = mergeList(head1, head2->next);
        return head2;
    }
}

ListNode* mergeWithDivideAndConquer(vector<ListNode*>& lists, int left, int right) {
    if (left == right) {
        return lists[left];
    }
    if (left > right) {
        return nullptr;
    }
    int mid = left + (right - left) / 2;
    return mergeList(mergeWithDivideAndConquer(lists, left, mid),
                     mergeWithDivideAndConquer(lists, mid + 1, right));
}

ListNode* mergeKLists(vector<ListNode*>& lists) {
//    ListNode* head = NULL;
//    for (int i = 0; i < lists.size(); i++) {
//        head = mergeList(head, lists[i]);
//    }
//    return head;
    return mergeWithDivideAndConquer(lists, 0, lists.size() - 1);
}
```
---
# [寻找两个有序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。

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
```c++
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
    unsigned long m = nums1.size(), n = nums2.size();
    if (m > n) {
        vector<int> temp = nums1; nums1 = nums2; nums2 = temp;
        unsigned long t = m; m = n; n = t;
    }
    int iMin = 0, iMax = m, halfLen = (m + n + 1) / 2;
    while (iMin <= iMax) {
        int i = (iMin + iMax) / 2;
        int j = halfLen - i;
        if (i < iMax && nums2[j-1] > nums1[i]){
            iMin = i + 1; // i is too small
        }
        else if (i > iMin && nums1[i-1] > nums2[j]) {
            iMax = i - 1; // i is too big
        }
        else { // i is perfect
            int maxLeft = 0;
            if (i == 0) { maxLeft = nums2[j-1]; }
            else if (j == 0) { maxLeft = nums1[i-1]; }
            else { maxLeft = fmax(nums1[i-1], nums2[j-1]); }
            if ( (m + n) % 2 == 1 ) { return maxLeft; }

            int minRight = 0;
            if (i == m) { minRight = nums2[j]; }
            else if (j == n) { minRight = nums1[i]; }
            else { minRight = fmin(nums2[j], nums1[i]); }

            return (maxLeft + minRight) / 2.0;
        }
    }
    return 0.0;
  }
  ```

































































