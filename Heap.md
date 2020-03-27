# 小项堆
```c++
int a[1010];
void MIN_HEAPIFY(int value,int N){
    a[N] = value;
    while (N/2) {
        if (a[N/2] > value) {
            a[N] = a[N/2];
            a[N/2] = value;
        }
        N /= 2;
    }
}
```
