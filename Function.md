# title
```c++
#include <iostream>
#include <cmath>
#include <cstring>
#include <string>
#include <vector>
#include <stack>
#include <queue>
#include <algorithm>
#include <map>
#include <set>
#include <list>
#include <numeric>
#include <deque>
#define TreeNull -100
using namespace std;

struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
```
---
# Array
```c++
class Array {
public:
    vector<int> built(int n) {
        vector<int> input(n);
        for (int i = 0; i < n; ++i) {
            cin >> input[i];
        }
        return input;
    }
    
    vector<vector<int>> built(int row, int column) {
        vector<vector<int>> input(row, vector<int>(column));
        for (int i = 0; i < row; ++i) {
            for (int j = 0; j < column; ++j) {
                cin >> input[i][j];
            }
        }
        return input;
    }
    
    void print(vector<int>& nums) {
        for (auto& num : nums) {
            cout << num << " ";
        }
        cout << endl;
    }
    
    void print(vector<vector<int>>& nums) {
        for (auto& row : nums) {
            for (auto& column : row) {
                cout << column << " ";
            }
            cout << endl;
        }
    }
};
```
---
# HexConversion
```c++
class HexConversion {
private:
    char hex[16]={'0','1','2','3','4','5','6','7','8','9','A','B','C','D','E','F'};
public:
    //  Decimal to binary
    string DecimalToBinary(int n) {
        string ans;
        while (n) {
            if (n & 1) {
                ans = '1' + ans;
            } else {
                ans = '0' + ans;
            }
            n >>= 1;
        }
        return ans;
    }
    
    //Decimal to hexadecimal
    string DecimalToHexadecimal(int n) {
        string ans;
        while (n > 0) {
            ans = hex[n % 16] + ans;
            n /= 16;
        }
        return "0x" + ans;
    }
};
```
---
# List
```c++
class List {
public:
    ListNode* createNewList(int n) {
        if (n < 1) {
            return nullptr;
        }
        int x;  cin >> x;
        ListNode* head = new ListNode(x);
        ListNode* tail = head;
        while (--n) {
            cin >> x;
            tail->next = new ListNode(x);
            tail = tail->next;
        }
        return head;
    }
    
    void printList(ListNode* head) {
        while (head) {
            cout << head->val << " ";
            head = head->next;
        }
        cout << endl;
    }
    
    //找到中间节点
    ListNode* findMidPoint(ListNode* head) {
        if (!head) {
            return nullptr;
        }
        if (!head->next) {
            return head;
        }
        ListNode* p = head, *q = head->next;
        while (q && q->next) {
            p = p->next;
            q = q->next->next;
        }
        return p;
    }
    
    // 反转链表
    ListNode* reverse(ListNode* head) { // 会改变原来链表
        ListNode* new_head = nullptr, *temp = nullptr;
        while (head) {
            temp = head->next;
            head->next = new_head;
            new_head = head;
            head = temp;
        }
        return new_head;
    }
    
    // 求两条链表的相交节点
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        ListNode *pA = headA, *pB = headB;
        while (pA != pB) {
            pA = pA == nullptr ? headB : pA->next;
            pB = pB == nullptr ? headA : pB->next;
        }
        return pA;
    }
};
```
---
# Tree
```c++
class Tree {
public:
    TreeNode* built(TreeNode* root, vector<int>& in, int i) {
        if (i >= in.size() || in[i] == TreeNull) {
            return nullptr;
        }
        root = new TreeNode(in[i]);
        root->left = built(root->left, in, 2 * i + 1);
        root->right = built(root->right, in, 2 * i + 2);
        return root;
    }
    
    void Preorder(TreeNode* root, vector<int>& ans) {
        if (!root) return;
        ans.push_back(root->val);
        Preorder(root->left, ans);
        Preorder(root->right, ans);
    }
    
    void Inorder(TreeNode* root, vector<int>& ans) {
        if (!root) return;
        Inorder(root->left, ans);
        ans.push_back(root->val);
        Inorder(root->right, ans);
    }
    
    void Postorder(TreeNode* root, vector<int>& ans) {
        if (!root) return;
        Postorder(root->left, ans);
        Postorder(root->right, ans);
        ans.push_back(root->val);
    }
    
    void Levelorder(TreeNode* root, vector<int>& ans) {
        queue<TreeNode*> qu;
        qu.push(root);
        while (!qu.empty()) {
            TreeNode* f = qu.front();   qu.pop();
            ans.push_back(f->val);
            if (f->left) {
                qu.push(f->left);
            }
            if (f->right) {
                qu.push(f->right);
            }
        }
    }
};
```
---
# KMP
```c++
class KMP {
public:
    vector<int> getNext(string p) {
        int m = (int)p.size(), i = 0;
        vector<int> next(m, -1);
        int j = -1; // 模式串指针
        while (i < m - 1) {
            if (j == -1 || p[i] == p[j]) {
                ++i;  ++j;
                next[i] = j;
            } else {
                j = next[j];
            }
        }
        return next;
    }
    int kmpMatch(string s, string p) {
        if (s == p) return 0;
        auto next = getNext(p);
        int m = (int)s.length(), i = 0; // 文本串指针
        int n = (int)p.length(), j = 0; // 模式串指针
        while (i < m && j < n) {
            if (j < 0 || s[i] == p[j]) {
                ++i; ++j;
            } else {
                j = next[j];
            }
        }
        if (j == n) {
            return i - j;
        }
        return -1;
    }
};
```
---
# main
```c++
int main(int argc, const char * argv[]) {
    return 0;
}

```
