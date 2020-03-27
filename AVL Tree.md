# AVL Tree
### There are four situation of rotation
- **LL Rotation**
![LL](https://images0.cnblogs.com/i/497634/201403/281626153129361.jpg)
```C++
AVLTree* left_left_rotation(AVLTree* k2){
    AVLTree* k1;
    k1 = k2->left;
    k2->left = k1->right;
    k1->right = k2;
    k2->height = Max(HEIGHT(k2->left), HEIGHT(k2->right)) + 1;
    k1->height = Max(HEIGHT(k1->left), k2->height) + 1;
    return k1;
}
```
- **RR Rotation**
![RR](https://images0.cnblogs.com/i/497634/201403/281626410316969.jpg)
```C++
AVLTree* right_right_rotation(AVLTree* k1){
    AVLTree* k2;
    k2 = k1->right;
    k1->right = k2->left;
    k2->left = k1;
    k1->height = Max( HEIGHT(k1->left), HEIGHT(k1->right)) + 1;
    k2->height = Max( HEIGHT(k2->right), k1->height) + 1;
    return k2;
}

```
- **LR Rotation**
![LR](https://images0.cnblogs.com/i/497634/201403/281627088127150.jpg)
```C++
AVLTree* left_right_rotation(AVLTree* k3){
    
    k3->left = right_right_rotation(k3->left);
    return left_left_rotation(k3);
}
```
- **RL Rotation**
![RL](https://images0.cnblogs.com/i/497634/201403/281628118447060.jpg)
```C++
AVLTree* right_left_rotation(AVLTree* k1){
    
    k1->right = left_left_rotation(k1->right);
    return right_right_rotation(k1);
}
```
### Some functions
```C++
struct AVLTree {
    int data;
    int height = 0;
    AVLTree* left = NULL;
    AVLTree* right = NULL;
};

int HEIGHT(AVLTree* root){
    if (!root) {
        return -1;
    }
    return root->height;
}

int Max(int a, int b){
    return a > b ? a : b;
}
```
### Insert operation
```C++
AVLTree* Insert(AVLTree* root, int key){
    if (root == NULL) {
        root = new AVLTree();
        root->data = key;
    }
    else
        if (key < root->data) {
            root->left = Insert(root->left, key);  //插入結點後，AVLTree失去平衡，需作出相應調整
            if (HEIGHT(root->left) - HEIGHT(root->right) == 2) {
                if (key < root->left->data) {
                    root = left_left_rotation(root);
                }
                else
                    root = left_right_rotation(root);
            }
        }
    else
        if (key > root->data) {
            root->right = Insert(root->right, key);
            if (HEIGHT(root->right) - HEIGHT(root->left) == 2) {
                if (key > root->right->data) {
                    root = right_right_rotation(root);
                }
                else
                    root = right_left_rotation(root);
            }
        }
    root->height = Max(HEIGHT(root->left), HEIGHT(root->right)) + 1;
    
    return root;
}
```
