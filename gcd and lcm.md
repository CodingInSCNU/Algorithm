# 最大公因数
```c++
int gcd(int a, int b) {
    if (b == 0) {
        return a;
    }
    return gcd(b, a % b);
}
```
# 最小公倍数
```c++
int lcm(int a, int b) {
    return a / gcd(a, b) * b;
}
```
$n^2$
