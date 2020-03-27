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
