给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。  

示例 1:
```
输入: num1 = "2", num2 = "3"
输出: "6"
```
示例 2:
```
输入: num1 = "123", num2 = "456"
输出: "56088"
```

思路：下标为 ```i``` 和 ```j``` 的数相乘其结果值的坐标为 ```[i + j, i + j + 1]``` , so update new value in the cycle is ok.  

![picture](https://pic.leetcode-cn.com/171cad48cd0c14f565f2a0e5aa5ccb130e4562906ee10a84289f12e4460fe164-image.png)
```c++
string multiply(string num1, string num2) {
    
    int len1 = num1.length();
    int len2 = num2.length();
    string res(len1 + len2, '0');
    for (int i = len2 - 1; i >= 0; i--) {
        for (int j = len1 - 1; j >= 0; j--) {
            int temp = (res[i+j+1]-'0') + (num1[j] - '0') * (num2[i] - '0');
            res[i+j+1] = temp % 10 + '0';
            res[i+j] += temp / 10;
        }
    }
    
    for (int i = 0; i < len1 + len2; i++) {
        if (res[i] != '0') {
            return res.substr(i);
        }
    }
    return "0";
}
```
