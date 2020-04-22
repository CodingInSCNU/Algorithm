# 机器人的运动范围
地上有一个 ```m``` 行 ```n``` 列的方格，从坐标 ```[0,0]``` 到坐标 ```[m-1,n-1]``` 。一个机器人从坐标 ```[0, 0]``` 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当 ```k``` 为 ```18``` 时，机器人能够进入方格 ```[35, 37]``` ，因为 ```3 + 5 + 3 + 7 = 18```。但它不能进入方格 ```[35, 38]```，因为 ```3 + 5 + 3 + 8 = 19```。请问该机器人能够到达多少个格子？

示例 1：
```
输入：m = 2, n = 3, k = 1
输出：3
```
示例 1：
```
输入：m = 3, n = 1, k = 0
输出：1
```
## 方法一：BFS
我们将行坐标和列坐标数位之和大于 ```k``` 的格子看作障碍物，那么这道题就是一道很传统的搜索题目，我们可以使用广度优先搜索或者深度优先搜索来解决它，本文选择使用广度优先搜索的方法来解。  

计算数位之和就不多做叙述了。  

这道题还有一个隐藏的优化：我们在搜索的过程中搜索方向可以缩减为向右和向下，而不必再向上和向左进行搜索。为什么呢？  
>因为假设 ```m, n``` 不变，随着 ```k``` 值的增加，```(0, 0)``` 所在的蓝色方格区域内新加入的非障碍方格都可以由上方或左方的格子移动一步得到.  
![picture](https://pic.leetcode-cn.com/eb360a76b26e583f23a5c8743c6f398f84be3d292d17179327f8c57ddab784b0-幻灯片7.JPG)  

这里有一个隐藏的问题：  
读者可能以为是 ```m * n``` 矩阵中**所有**行列位数之和满足条件就计数器加一，但结果并不对，不是吗？为什么呢？  
>因为小机器人是从 ```(0, 0)``` 开始往下或者往右走的，那如果它所有往下和往右的路都是不能走的呢？也就是说它的路被断了，详细看上图。  

所以我们可以使用 ```BFS``` 来遍历，使用队列才存储能走的路，如果队列空了，也就是说无路可走了，此时返回就好。   

```c++
int get(int x) {
    int res = 0;
    while (x) {
        res += x % 10;
        x /= 10;
    }
    return res;
}

int movingCount(int m, int n, int k) {
    if (!k) return 1;
    queue<pair<int,int> > Q;
    // 向右和向下的方向数组
    int dx[2] = {0, 1};
    int dy[2] = {1, 0};
    vector<vector<int>> vis(m, vector<int>(n, 0));
    Q.push(make_pair(0, 0));
    vis[0][0] = 1;
    int ans = 1;
    while (!Q.empty()) {
        auto [x, y] = Q.front();
        Q.pop();
        // 往下或者往右
        for (int i = 0; i < 2; ++i) {
            int tx = dx[i] + x;
            int ty = dy[i] + y;
            if (tx < 0 || tx >= m || ty < 0 || ty >= n || vis[tx][ty] || get(tx) + get(ty) > k) continue;
            Q.push(make_pair(tx, ty));
            vis[tx][ty] = 1;
            ans++;
        }
    }
    return ans;
}
```
复杂度分析
- 时间复杂度：```O(mn)```，其中 ```m``` 为方格的行数，```n``` 为方格的列数。考虑所有格子都能进入，那么搜索的时候一个格子最多会被访问的次数为常数，所以时间复杂度为 ```O(2mn) = O(mn)```.  
- 空间复杂度：```O(mn)```，其中 ```m``` 为方格的行数，```n``` 为方格的列数。搜索的时候需要一个大小为 ```O(mn)``` 的标记结构用来标记每个格子是否被走过。  

## 方法二：递推
考虑到方法一提到搜索的方向只需要朝下或朝右，我们可以得出一种递推的求解方法。  

算法：
定义 ```vis[i][j]``` 为 ```(i, j)``` 坐标是否可达，如果可达返回 ```1```，否则返回 ```0```。

首先 ```(i, j)``` 本身需要可以进入，因此需要先判断 ```i``` 和 ```j``` 的数位之和是否大于 ```k``` ，如果大于的话直接设置 ```vis[i][j]``` 为不可达即可。

否则，前面提到搜索方向只需朝下或朝右，因此 ```(i, j)``` 的格子只会从 ```(i - 1, j)``` 或者 ```(i, j - 1)``` 两个格子走过来（不考虑边界条件），那么 ```vis[i][j]``` 是否可达的状态则可由如下公式计算得到：
>```vis[i][j] = vis[i-1][j] or vis[i][j-1]```  

即只要有一个格子可达，那么 ```(i, j)``` 这个格子就是可达的，因此我们只要遍历所有格子，递推计算出它们是否可达然后用变量 ```ans``` 记录可达的格子数量即可。

初始条件 ```vis[i][j] = 1``` ，递推计算的过程中注意边界的处理。  

```c++
int get(int x) {
    int res = 0;
    while (x) {
        res += x % 10;
        x /= 10;
    }
    return res;
}

int movingCount(int m, int n, int k) {
    if (!k) return 1;
    vector<vector<int> > vis(m, vector<int>(n, 0));
    int ans = 1;
    vis[0][0] = 1;
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if ((i == 0 && j == 0) || get(i) + get(j) > k) continue;
            // 边界判断
            if (i - 1 >= 0) vis[i][j] |= vis[i - 1][j];
            if (j - 1 >= 0) vis[i][j] |= vis[i][j - 1];
            ans += vis[i][j];
        }
    }
    return ans;
}
```
复杂度分析
- 时间复杂度：```O(mn)```.  
- 空间复杂度：```O(mn)```.  

# [Knight Moves](https://zoj.pintia.cn/problem-sets/91827364500/problems/91827364590)

A friend of you is doing research on the Traveling Knight Problem (TKP) where you are to find the shortest closed tour of knight moves that visits each square of a given set of n squares on a chessboard exactly once. He thinks that the most difficult part of the problem is determining the smallest number of knight moves between two given squares and that, once you have accomplished this, finding the tour would be easy.  
Of course you know that it is vice versa. So you offer him to write a program that solves the "difficult" part.

Your job is to write a program that takes two squares a and b as input and then determines the number of knight moves on a shortest route from a to b.  

Input Specification  
The input file will contain one or more test cases. Each test case consists of one line containing two squares separated by one space. A square is a string consisting of a letter (a-h) representing the column and a digit (1-8) representing the row on the chessboard.  
Output Specification  
For each test case, print one line saying "To get from xx to yy takes n knight moves.".  
**Sample Input**  
```
e2 e4
a1 b2
b2 c3
a1 h8
a1 h7
h8 a1
b1 c3
f6 f6
```
**Sample Output**
```
To get from e2 to e4 takes 2 knight moves.
To get from a1 to b2 takes 4 knight moves.
To get from b2 to c3 takes 2 knight moves.
To get from a1 to h8 takes 6 knight moves.
To get from a1 to h7 takes 5 knight moves.
To get from h8 to a1 takes 6 knight moves.
To get from b1 to c3 takes 1 knight moves.
To get from f6 to f6 takes 0 knight moves.
```

这题就是普通的BFS，就是一匹马不断的走日字形路，就不多做赘述了，直接上代码。  
```c++
#include <iostream>
#include <cstdio>
#include <queue>
#include <cstring>
#include <cstdlib>
using namespace std;

struct node {
    int x, y;
    int step;
};

// 8 * 8 chessboard
//a letter (a-h) representing the column and a digit (1-8) representing the row
int sx, sy, ex, ey, res;
bool visited[8][8];
//方向数组
int dir[8][2] = {
    {1, 2}, {-1, 2}, {2, 1}, {-2, 1}, {1, -2}, {-1, -2}, {2, -1}, {-2, -1}
};
void bfs() {
    node* start = (node* )malloc(sizeof(node));
    //initialize origin
    start->x = sx; start->y = sy; start->step = 0;
    queue<node*> q;
    q.push(start);
    visited[start->x][start->y] = true;
    while (!q.empty()) {
        node* temp = q.front();
        q.pop();
        if (temp->x == ex && temp->y == ey) {
            res = temp->step;
        }
        for (int i = 0; i < 8; i++) {
            int plus_x = temp->x + dir[i][0], plus_y = temp->y + dir[i][1];
            //下一个 step 的坐标要在合法范围内且没有走过
            if (plus_x >= 0 && plus_x <= 7 && plus_y >= 0 && plus_y <= 7 && !visited[plus_x][plus_y]){
                node* kids = (node* )malloc(sizeof(node));
                kids->x = plus_x; kids->y = plus_y; kids->step = temp->step + 1;
                q.push(kids);
                visited[plus_x][plus_y] = true;
            } else continue;
        }
    }
}

int main(int argc, const char * argv[]) {
    char a[5], b[5];
    while (scanf("%s%s", a, b) != EOF) {
        sx = a[1] - '1'; sy = a[0] - 'a';
        ex = b[1] - '1'; ey = b[0] - 'a';
        memset(visited, false, sizeof(visited));
        if (strcmp(a, b) == 0) {
            printf("To get from %s to %s takes %d knight moves.\n", a, b, 0);
            continue;
        }
        bfs();
        printf("To get from %s to %s takes %d knight moves.\n", a, b, res);
    }
    return 0;
}
```
# 01矩阵
给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。

示例 1: 
```
输入:

0 0 0
0 1 0
0 0 0
输出:

0 0 0
0 1 0
0 0 0
```
示例 2: 
```
输入:

0 0 0
0 1 0
1 1 1
输出:

0 0 0
0 1 0
1 2 1
```
## [BFS](https://leetcode-cn.com/problems/01-matrix/solution/01ju-zhen-by-leetcode-solution/)
```c++
int dirs[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    vector<vector<int>> updateMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> dist(m, vector<int>(n));
        vector<vector<int>> seen(m, vector<int>(n));
        queue<pair<int, int>> q;
        // 将所有的 0 添加进初始队列中
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (matrix[i][j] == 0) {
                    q.emplace(i, j);
                    seen[i][j] = 1;
                }
            }
        }

        // 广度优先搜索
        while (!q.empty()) {
            auto [i, j] = q.front();
            q.pop();
            for (int d = 0; d < 4; ++d) {
                int ni = i + dirs[d][0];
                int nj = j + dirs[d][1];
                if (ni >= 0 && ni < m && nj >= 0 && nj < n && !seen[ni][nj]) {
                    dist[ni][nj] = dist[i][j] + 1;
                    q.emplace(ni, nj);
                    seen[ni][nj] = 1;
                }
            }
        }

    return dist;
    }
```
---
# [二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/)
给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

示例:
```
输入: [1,2,3,null,5,null,4]
输出: [1, 3, 4]
解释:

   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
```

```c++
vector<int> rightSideView(TreeNode* root) {
    unordered_map<int, int> rightmostValueAtDepth;
    vector<int> ans;
    int max_depth = -1;
    queue<TreeNode*> nodeQueue;
    queue<int> depthQueue;
    nodeQueue.push(root);
    depthQueue.push(0);
    while (!nodeQueue.empty()) {
        TreeNode* temp = nodeQueue.front();  nodeQueue.pop();
        int depth = depthQueue.front();  depthQueue.pop();
        if (temp) {
            max_depth = fmax(depth, max_depth);
            rightmostValueAtDepth[depth] = temp->val;
            nodeQueue.push(temp->left);
            nodeQueue.push(temp->right);
            depthQueue.push(depth + 1);
            depthQueue.push(depth + 1);
        }
    }
    for (int i = 0; i <= max_depth; i++) {
        ans.push_back(rightmostValueAtDepth[i]);
    }
    return ans;
}
```


































