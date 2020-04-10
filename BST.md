# 建立一棵二叉搜索樹
```c++
tree* Insert(tree* root,int key){
    if (!root) {
        //root = (tree *)malloc(sizeof(tree));
        root = new tree();
        root->data = key;
    }
    else{
        if (key < root->data) {
            root->left = Insert(root->left, key);
        }
        else
            if (key > root->data) {
                root->right = Insert(root->right, key);
            }
    }
    return root;
}
```
---
# 求一棵完全二叉樹的左子樹結點個數
```c++
//求解最容易錯的就是最底層可能有右子樹的結點，所以需要求得最底層左子樹結點的最大值進行對比

int left_child_number(int total){
    int n = log(total) / log(2);    //除了最底層以外的層數（從0開始編號）
    int bottom = total - (pow(2, n) - 1);   //最底層的結點數，其中(pow(2, n) - 1)是除了底層以外的結點數
    int left_bottom_max = pow(2, n - 1);    //左子樹最底層的最大結點數（第0層除外）
    int left_branch_number = pow(2, n - 1) - 1;     //左子樹除了最底層的結點個數
    if (bottom >= left_bottom_max) {
        return left_bottom_max + left_branch_number;
    }
    else
        return bottom + left_branch_number;
}
```
