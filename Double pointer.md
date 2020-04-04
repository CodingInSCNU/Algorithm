# 三数之和
给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 ```a，b，c``` ，使得 ```a + b + c = 0``` ？找出所有满足条件且不重复的三元组。  
注意：答案中不可以包含重复的三元组。  
示例：  
```
给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```
## 双指针
基本思路：
1. 将数组排序
2. 从头到尾遍历数组，每次遍历确定一个基准值，也就是 target ，每次遍历中都遍历一次比这个下标值之后的元素一次，用双指针只想后续数组的头和尾，比较大小来移动头指针或者尾指针
```c++
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>>ans;
    sort(nums.begin(),nums.end());
    if (nums.size() < 3 || nums[0] > 0 || nums[nums.size()-1] < 0) {
        return ans;
    }
    for (int i=0; i<nums.size(); i++) {
        if (nums[i] > 0) {
            break;
        }
        if (i > 0 && nums[i] == nums[i-1]) {
            continue;
        }
        int target = 0 - nums[i];
        int j = i + 1;
        unsigned long m = nums.size() - 1;
        while (j < m) {
            if (nums[j] > target) {
                break;
            }
            if ((j > i + 1 && nums[j] == nums[j-1]) || nums[j] + nums[m] < target) {
                j++;
            }
            else
                if ((m < nums.size() - 1 && nums[m] == nums[m+1]) || nums[j] + nums[m] > target) {
                    m--;
                }
                else {
                    vector<int>res;
                    res.push_back(nums[i]);
                    res.push_back(nums[j]);
                    res.push_back(nums[m]);
                    ans.push_back(res);
                    j++;
                }
        }
    }
    return ans;
}
```
# 接雨水
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。  
![p](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)  
示例:
```
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6
```
## 暴力法(按列计算)
**直观想法**  
直接按问题描述进行。对于数组中的每个元素，我们找出下雨后水能达到的最高位置，等于两边最大高度的较小值减去当前高度的值。

算法：
- 初始化 ```ans = 0```.  
- 从左向右扫描数组
  - 初始化 ```max_left = 0``` 和 ```max_right = 0```.  
  - 从当前元素向左扫描并更新：```max_left = fmax(max_left, height[j])```.  
  - 从当前元素向右扫描并更新：```max_right = fmax(max_right, height[j])```.  
  - 将 ```fmin(max_left, max_right) - height[i]``` 累加到 ```ans```.  

```c++
int trap(vector<int>& height) {
    int ans = 0;
    int max_left, max_right;
    for (int i = 1; i < height.size(); i++) {
        max_left = max_right = 0;
        //找出 height[i] 左边最高的柱子
        for (int j = i; j >= 0; j--) {
            max_left = fmax(max_left, height[j]);
        }
        //找出 height[i] 右边最高的柱子
        for (int j = i; j < height.size(); j++) {
            max_right = fmax(max_right, height[j]);
        }
        //找出两根柱中的最小值，然后与当前的位置(height[i])高度比较，然后可以得出 height[i] 头上有多少体积的水
        ans += fmin(max_left, max_right) - height[i];
        cout << ans << " ";
    }
    cout << endl;
    return ans;
}
```
复杂性分析
- 时间复杂度：O(n^2),数组中的每个元素都需要向左向右扫描.  
- 空间复杂度：O(1).  

## 动态规划(按列计算，暴力法的改进)
状态：
```max_left[i]``` 和 ```max_right[i]``` 表示以下标为 ```i``` 向左或者向右的最大柱子高度。 

状态转移方程：
```max_left[i]```：```max_left[i] = fmax(height[i], max_left[i - 1])```.  
```max_right[i]```：```max_right[i] = fmax(height[i], max_right[i + 1])```.  
注意右数组是从尾部开始遍历的

边界情况：
```i - 1``` 和 ```i + 1``` 可能越界，先填好首尾的值，再遍历即可。 

```c++
int trap(vector<int>& height) {
    if (height.empty()) {
        return 0;
    }
    int ans = 0;
    vector<int> max_left(height.size());
    vector<int> max_right(height.size());
    max_left[0] = height[0];
    for (int i = 1; i < height.size(); i++) {
        max_left[i] = fmax(height[i], max_left[i - 1]);
    }
    max_right[height.size() - 1] = height[height.size() - 1];
    for (int i = height.size() - 2; i >= 0; i--) {
        max_right[i] = fmax(height[i], max_right[i + 1]);
    }
    for (int i = 1; i < height.size() - 1; i++) {
        ans += fmin(max_left[i], max_right[i]) - height[i];
    }
    return ans;
}
```
复杂度分析
- 时间复杂度：O(n)
  - 存储最大高度数组，需要两次遍历，每次 O(n).  
  - 最终使用存储的数据更新 ```ans```, O(n).  
- 空间复杂度：O(n), 使用额外数组空间  

## 栈的应用(按行计算)
**直观想法**  
我们可以不用像方法 2 那样存储最大高度，而是用栈来跟踪可能储水的最长的条形块。使用栈就可以在一次遍历内完成计算。

我们在遍历数组时维护一个栈。如果当前的条形块小于或等于栈顶的条形块，我们将条形块的索引入栈，意思是当前的条形块被栈中的前一个条形块界定。如果我们发现一个条形块长于栈顶，我们可以确定栈顶的条形块被当前条形块和栈的前一个条形块界定，因此我们可以弹出栈顶元素并且累加答案到 ```ans```  

动图演示：
![p](https://pic.leetcode-cn.com/7d5ff9af88634d417d7925e8987b7db92d3a26766bd9078215ab63df424fa745-water.gif)  
看 ```gif``` 图我们可以发现，遍历到某些柱子的时候，会由于和之前的某个柱子形成凹形的坑，接住雨水。
这道题目可以用单调栈来做。单调栈就是比普通的栈多一个性质，即维护一个栈内元素单调。
比如当前某个单调递减的栈的元素从栈底到栈顶分别是：```[10, 9, 8, 3, 2]```，如果要入栈元素 ```5```，需要把栈顶元素 ```pop``` 出去，直到满足单调递减为止，即先变成 ```[10, 9, 8]```，再入栈 ```5```，就是 ```[10, 9, 8, 5]```。  

我们为这道题演示一下 ```[4, 3, 1, 0, 1, 2, 4]``` 是怎么接雨水的，下图是最终的接雨水效果，蓝色部分是雨水。
![p](https://pic.leetcode-cn.com/1d1c62807d886ac9a10229cbae229465989bd6aa707449e9620a639772ba3f07-image.png)  

可以看下下面的图示。图示最上方是每个柱子的高度。左侧是单调栈的元素，```0``` 是栈顶。图中有红色边框的柱子是存在单调栈里的元素。
遍历到图示中箭头所指向的位置时，栈内元素是 ```[4, 3, 1, 0]```。由于当前的柱体的 ```1``` 大于栈顶元素 ```0```，那就可以接住雨水。接住雨水的量的高度是栈顶元素和左右两边形成的高度差的 ```min``` 。宽度是 ```1```。
![p](https://pic.leetcode-cn.com/83b78b42212c09b06452e8da21cd137d4a80bbdfbfcd34adff57bceba1003836-image.png)  

到下一个柱体高度为2时，栈内元素是 ```[4, 3, 1, 1]```。由于当前的柱体的 ```2``` 大于栈顶元素 ```1```，那就可以接住雨水。由于栈顶元素有相等的情况，所以可以把 ```1``` 全都 ```pop``` 出去，变成 ```[4, 3]```。此时最后一个 ```pop``` 出去的是 ```1```，高度是此时的栈顶元素和当前 ```2``` 柱体的高度的 ```min``` 再减去当前的 ```1```，宽度是 ```1``` 那个数字的位置和当前 ```2``` 柱体所在位置的差值。 可以算出来此次接住的雨水是 ```1 * 3```  
![p](https://pic.leetcode-cn.com/ff41c19058478bcbb2bef149e336360dec67f7941428b99fe46026e4e010d991-image.png)  

在下一个柱体高度为4时，栈内元素是 ```[4, 3, 2]```，先把 ```2``` ```pop``` 出来，栈顶元素 ```3``` 所在位置和当前的 ```4``` 可以接住雨水，雨水量是 ```1 * 4```  
![p](https://pic.leetcode-cn.com/3a812e3ba8322a8addd6870cc04690b761c52f1b8effbcd9cc67f6dc3e8cf2c3-image.png)  

但是由于栈顶元素 ```3``` 仍然小于当前的 ```4``` ,再 ```pop``` 出 ```3```。栈顶元素 ```4``` 所在位置和当前的 ```4``` 可以接住雨水，雨水量是 ```1 * 5```  
![p](https://pic.leetcode-cn.com/236d6cd02def72dcadf1aaa0f7bbbc767da161795d6702523835127002381a0f-image.png)  

算法
- 使用栈来存储条形块的索引下标.  
- 遍历数组：  
  - 当栈非空且 ```height[current] > height[st.top()]```.  
    - 意味着栈中元素可以被弹出。弹出栈顶元素 ```top```.  
    - 计算当前元素和栈顶元素的距离，准备进行填充操作 : ```distant = current - s.top() - 1```.  
    - 找出界定高度 : ```bounded_height = fmin(height[current], height[s.top()]) - height[top]```.  
    - 往答案中累加积水量 : ```ans += distant * bounded_height```.  
  - 将当前索引下标入栈.  
  - 将 ```current``` 移动到下个位置.  
  
```c++
int trap(vector<int>& height) {
    int ans = 0, current = 0;
    stack<int> s;
    while (current < height.size()) {
        while (!s.empty() && height[current] > height[s.top()]) {
            int top = s.top();
            s.pop();
            if (s.empty()) {
                break;
            }
            int distant = current - s.top() - 1;
            int bounded_height = fmin(height[current], height[s.top()]) - height[top];
            //能用乘法的原因就是是因为此接雨水的方法是一层一层接的(有可能一次接几层)，反正是按层次来的
            ans += distant * bounded_height;
        }
        s.push(current++);
    }
    return ans;
}
```
复杂度分析
- 时间复杂度：O(n), 单次遍历, 每个条形块最多访问两次（由于栈的弹入和弹出），并且弹入和弹出栈都是 O(1) 的.  
- 空间复杂度：O(n), 栈最多在阶梯型或平坦型条形块结构中占用 O(n) 的空间.  

## 双指针(按列计算)
有以上几个题解的理解基础上，双指针算法就不用解释啦，直接上代码！！  
```c++
int trap(vector<int>& height) {
    int ans = 0;
    int left = 0, right = height.size() - 1;
    int left_max = 0, right_max = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            height[left] >= left_max ? (left_max = height[left]) : (ans += left_max - height[left]);
            left++;
        } else {
            height[right] >= right_max ? (right_max = height[right]) : (ans += right_max - height[right]);
            right--;
        }
    }
    return ans;
}
```
复杂度分析
- 时间复杂度：O(n), 单次遍历 O(n).  
- 空间复杂度：O(1), ```left```, ```right```, ```left_max``` 和 ```right_max``` 只需要常数的空间.  

































