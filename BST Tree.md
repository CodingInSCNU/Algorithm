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
