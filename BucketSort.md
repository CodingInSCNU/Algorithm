![p](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=305161475,1761458255&fm=26&gp=0.jpg)
# 基本思想（分治思想）：
桶排序假设待排序的一组数均匀独立的分布在一个范围中，并将这一范围划分成几个子范围（桶）。

然后基于某种映射函数```f``` ，将待排序列的关键字 ```k``` 映射到第 ```i``` 个桶中 (即桶数组 ```Bucket``` 的下标```i```，其次，很重要的一点就是：映射函数要是线性的，不然会重叠，达不到排序的目的) ，那么该关键字 ```k``` 就作为 ```B[i]``` 中的元素，每个桶的元素可以用链表连接起来。

接着将各个桶中的数据有序的合并起来 : 对每个桶 ```B[i]``` 中的所有元素进行比较排序 (也可以使用快排，以下代码使用的是归并排序(O(nlogn)))。然后依次枚举输出 ```B[0]….B[M]``` 中的全部内容即是一个有序序列。

步骤：
1、根据待排序集合中最大元素和最小元素的差值范围和映射规则，确定申请的桶个数；  
2、遍历待排序集合，将每一个元素移动到对应的桶中；  
3、对每一个桶中元素进行排序，并移动到已排序集合中。  

## 动图演示:
![picture](https://img-blog.csdn.net/2018033115584192?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RldmVsb3BlcjEwMjQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

>设待排序集合为：[-7, 51, 3, 121, -3, 32, 21, 43, 4, 25, 56, 77, 16, 22, 87, 56, -10, 68, 99, 70]  
映射规则为: f(x) = x / 10 + min / 10;

## Step1:
遍历集合可得，最大值为：```max = 121```，最小值为：```min = -10```，待申请桶的个数为：```max / 10 - min / 10 + 1 = 14```。 

## Step2:
遍历待排序集合，依次添加各元素到对应的桶中。
|桶下标|桶中元素|
|:-:|:-:|
|0|-7, -3, -10|
|1|3, 4|
|2|16|
|3|21, 22, 25|
|4|32|
|5|43|
|6|51, 56, 56|
|7|68|
|8|77, 70|
|9|87|
|10|99|
|11||
|12||
|13|121|

## step 3:
对每一个桶中元素进行排序，并移动回原始集合中，即完成排序过程。

```c++
#include <iostream>
#include <cstdlib>
#include <vector>
using namespace std;

struct Node {
    int data = 0;
    Node* next = NULL;
};

Node* mergeTwoList(Node* h1, Node* h2) {
    if(!h1) return h2;
    if(!h2) return h1;
    if(h1->data < h2->data) {
        h1->next = mergeTwoList(h1->next, h2);
        return h1;
    }
    else {
        h2->next = mergeTwoList(h1, h2->next);
        return h2;
    }
}

Node* sortList(Node* head) {
    if(!head || !head->next) return head;
    Node* pre = head, *slow = head, *fast = head;
    while(fast && fast->next) {
        pre = slow;
        slow = slow->next;
        fast = fast->next->next;
    }
    pre->next = nullptr;
    return mergeTwoList(sortList(head), sortList(slow));
}

int f(double n) {
    return n / 10 + 1;
}

void BucketSort(vector<int>& InputData, int n_size) {
    vector<Node*> Bucket(n_size);
    for (int i = 0; i < n_size; i++) {
        int Insert_index = f(InputData[i]);
        Node* head = Bucket[Insert_index];
        Node* new_head = (Node* )malloc(sizeof(Node*));
        new_head->data = InputData[i];
        new_head->next = head;
        Bucket[Insert_index] = sortList(new_head);
    }
    int tail = 0;
    //将桶中的元素逐个添加到原数组，覆盖原本的值
    for (int i = 0; i < n_size; i++) {
        Node* head = Bucket[i];
        while (head) {
            InputData[tail++] = head->data;
            head = head->next;
        }
    }
}

void print(vector<int> InputData) {
    for (auto v : InputData) {
        cout << v << " ";
    }
    cout << endl;
}

int main(int argc, const char * argv[]) {
    int n_size;
    cin >> n_size;
    vector<int> InputData(n_size);
    for (int i = 0; i < n_size; i++) {
        cin >> InputData[i];
    }
    BucketSort(InputData, n_size);
    print(InputData);
    return 0;
}
/*
 20
 -7 51 3 121 -3 32 21 43 4 25 56 77 16 22 87 56 -10 68 99 70
 */
```
















