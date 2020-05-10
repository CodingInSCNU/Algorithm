# 樹的遍歷
- **層次遍歷**
```C++
void Hierarchical_traversal(tree* root){
    tree* temp = NULL;
    queue<tree *> Q;
    if (!root) {
        return;
    }
    Q.push(root);
    while (!Q.empty()) {
        temp = Q.front();
        Q.pop();
        cout<<temp->data<<" ";
         if (temp->left) {
            Q.push(temp->left);
        }
        if (temp->right) {
            Q.push(temp->right);
        }
    }
}
```
- **先序遍歷**
```C++
void Preorder_traversal(AVLTree* root){
    if (root) {
        cout<<root->data<<" ";
        Preorder_traversal(root->left);
        Preorder_traversal(root->right);
    }
}

//非递归
void PreorderTraversalNotRecursion(tree* root){
    stack<tree* >nodeStack;
    tree* pointer = root;
    while (!nodeStack.empty() || pointer != NULL) {
        if (pointer != NULL) {
            cout<<pointer->data<<" ";
            if (pointer->right != NULL) {
                nodeStack.push(pointer->right);
            }
            pointer = pointer->left;
        }
        else{
            pointer = nodeStack.top();
            nodeStack.pop();
        }
    }
}
```
- **中序遍歷**
```C++
void In_order_traversal(AVLTree* root){
    if (root) {
        Preorder_traversal(root->left);
        cout<<root->data<<" ";
        Preorder_traversal(root->right);
    }
}

//非递归
void InorderTraversalNotRecursion(tree* root){
    if (root == NULL) {
        return;
    }
    stack<tree* >nodeStack;
    tree* p = root;
    nodeStack.push(root);
    while (!nodeStack.empty()) {
        if (p != NULL && p->left != NULL) {
            nodeStack.push(p->left);
            p = p->left;
        }
        else{
            p = nodeStack.top();
            nodeStack.pop();
            cout<<p->data<<" ";
            if (p != NULL && p->right != NULL) {
                nodeStack.push(p->right);
                p = p->right;
            }
            else{
                p = NULL;
            }
        }
    }
}
```
- **後序遍歷**
```C++
void Postorder_traversal(tree* root){
    if (root) {
        Postorder_traversal(root->left);
        Postorder_traversal(root->right);
        cout<<root->data<<" ";
    }
}

//非递归
void PostorderTraversalNotRecursion(tree* root){
    if (root) {
        stack<tree*> st;
        tree* p = root,*r = NULL;
        while (p || !st.empty()) {
            if (p) {
                st.push(p);
                p = p->left;
            }
            else {
                p = st.top();
                if(p->right!=NULL&&p->right != r){
                p = p->right;
            }
                else {
                    st.pop();
                    cout << p->data << " ";
                    r = p;
                    p = NULL;
                }
            }
        }
    }
}
```
---
# [二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/最近公共祖先/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

例如，给定如下二叉树:  ```root = [3,5,1,6,2,0,8,null,null,7,4]```

![picture](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/binarytree.png)

示例 1:
```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```
示例 2:
```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

说明:

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉树中。

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

TreeNode* ans;
bool dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (root == nullptr) {
        return false;
    }
    bool lson = dfs(root->left, p, q);
    bool rson = dfs(root->right, p, q);
    if ((lson && rson) || ((root->val == p->val || root->val == q->val) && (lson || rson))) {
        ans = root;
    }
    return lson || rson || (root->val == p->val || root->val == q->val);
}

TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    dfs(root, p, q);
    return ans;
}

```

































