```c++
#include <iostream>
#include <vector>
using namespace std;

vector<int> path;
vector<vector<int>> result;
// Tatal number = n, we want extract r numbers
void combination(int n, int r) {
    if (r == 0) {
        result.push_back(path);
        return;
    }
    // why is the bottom is r? Beacause we must traverse all the situations which result include 1(if r == 0, we return)
    for (int i = n; i >= r; i--) {
        path.push_back(i);
        combination(i - 1, r - 1);
        path.pop_back();
    }
}

int main(int argc, const char * argv[]) {
    int n, r;
    cin >> n >> r;
    combination(n, r);
    for (int i = 0; i < result.size(); i++) {
        for (auto v : result[i]) {
            cout << v << " ";
        }
        cout << endl;
    }
    return 0;
}
```
