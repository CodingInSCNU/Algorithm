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
