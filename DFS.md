# [Fire Net](https://zoj.pintia.cn/problem-sets/91827364500/problems/91827364501)
Suppose that we have a square city with straight streets. A map of a city is a square board with n rows and n columns, each representing a street or a piece of wall.

A blockhouse is a small castle that has four openings through which to shoot. The four openings are facing North, East, South, and West, respectively. There will be one machine gun shooting through each opening.

Here we assume that a bullet is so powerful that it can run across any distance and destroy a blockhouse on its way. On the other hand, a wall is so strongly built that can stop the bullets.

The goal is to place as many blockhouses in a city as possible so that no two can destroy each other. A configuration of blockhouses is legal provided that no two blockhouses are on the same horizontal row or vertical column in a map unless there is at least one wall separating them. In this problem we will consider small square cities (at most 4x4) that contain walls through which bullets cannot run through.

The following image shows five pictures of the same board. The first picture is the empty board, the second and third pictures show legal configurations, and the fourth and fifth pictures show illegal configurations. For this board, the maximum number of blockhouses in a legal configuration is 5; the second picture shows one way to do it, but there are several other ways.

![picture](https://images.ptausercontent.com/0000%2F1002%2F1002.gif)  

Your task is to write a program that, given a description of a map, calculates the maximum number of blockhouses that can be placed in the city in a legal configuration.

The input file contains one or more map descriptions, followed by a line containing the number 0 that signals the end of the file. Each map description begins with a line containing a positive integer n that is the size of the city; n will be at most 4. The next n lines each describe one row of the map, with a '.' indicating an open space and an uppercase 'X' indicating a wall. There are no spaces in the input file.

For each test case, output one line containing the maximum number of blockhouses that can be placed in the city in a legal configuration.

**Sample input:**
```
4  
.X..
....
XX..
....
2
XX
.X
3
.X.
X.X
.X.
3
...
.XX
.XX
4
....
....
....
....
0
```
**Sample output:**
```
5
1
5
2
4
```
## 填鸭式解法
一个一个坑填进去，一条路走到黑，没什么特别好说的，直接上代码就明白了。  

```c++
#include <iostream>
#include <vector>
using namespace std;

int res, n;
vector<string> map;
void dfs(int pos, int count) {
    if (pos == n * n) {
        res = res < count ? count : res;
        return;
    }
    int row = pos / n, column = pos % n;
    bool isLegel = true;
    if (map[row][column] != 'X') {
        int upper = row;
        //upper
        while (isLegel && --upper >= 0) {
            if (map[upper][column] == 'X') {
                break;
            } else if (map[upper][column] == 'N') {
                isLegel = false;
                break;
            }
        }
        //lower
        int lower = row;
        while (isLegel && ++lower < n) {
            if (map[lower][column] == 'X') {
                break;
            } else if (map[lower][column] == 'N') {
                isLegel = false;
                break;
            }
        }
        //left
        int left = column;
        while (isLegel && --left >= 0) {
            if (map[row][left] == 'X') {
                break;
            } else if (map[row][left] == 'N') {
                isLegel = false;
                break;
            }
        }
        //right
        int right = column;
        while (isLegel && ++right < n) {
            if (map[row][right] == 'X') {
                break;
            } else if (map[row][right] == 'N') {
                isLegel = false;
                break;
            }
        }
        if (isLegel) {
            map[row][column] = 'N';
            dfs(pos + 1, count + 1);
            map[row][column] = '.';
        }
    }
    dfs(pos + 1, count);
}

int main(int argc, const char * argv[]) {
    string t;
    while (cin >> n && n != 0) {
        map.clear();
        res = 0;
        for (int i = 0; i < n; i++) {
            cin >> t;
            map.push_back(t);
        }
        dfs(0, 0);
        cout << res << endl;
    }
    return 0;
}
```








































