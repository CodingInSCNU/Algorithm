# 链表反转
```c++
list *reverse(list *head)
{
    list *temp = NULL;
    list *new_head = NULL;
    while (head) {
        temp = head->next;
        head->next = new_head;
        new_head = head;
        head = temp;
    }
    return new_head;
}
```
---
# 两数之和
给你两个 非空 链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 ```0``` 之外，这两个数字都不会以零开头。

示例：
```
输入：(7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 8 -> 0 -> 7
```
## 栈

```c++
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    stack<int> s1, s2;
    while (l1) {
        s1.push(l1 -> val);
        l1 = l1 -> next;
    }
    while (l2) {
        s2.push(l2 -> val);
        l2 = l2 -> next;
    }
    int carry = 0;
    ListNode* ans = nullptr;
    while (!s1.empty() or !s2.empty() or carry != 0) {
        int a = s1.empty() ? 0 : s1.top();
        int b = s2.empty() ? 0 : s2.top();
        if (!s1.empty()) s1.pop();
        if (!s2.empty()) s2.pop();
        int cur = a + b + carry;
        carry = cur / 10;
        cur %= 10;
        auto curnode = new ListNode(cur);
        curnode -> next = ans;
        ans = curnode;
    }
    return ans;
}
```
