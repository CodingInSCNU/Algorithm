# 并查集是什么？
[并查集百度百科](https://baike.baidu.com/item/并查集/9388442?fr=aladdin)  
并查集是一种用来管理元素分组情况的数据结构。并查集可以高效地进行如下操作。不过需要注意并查集虽然可以进行合并操作，但是却无法进行分割操作。  

- 查询元素a和元素b是否属于同一组
- 合并元素a和元素b所在的组

![loading](https://upload-images.jianshu.io/upload_images/17401788-c2b781ba2d881471.png?imageMogr2/auto-orient/strip|imageView2/2/w/361)  

# 并查集的结构
并查集也是使用树形结构实现的，不过不是二叉树。  
![laoding](https://upload-images.jianshu.io/upload_images/17401788-c0f909363105725a.png?imageMogr2/auto-orient/strip|imageView2/2/w/349)  
每个元素对应一个节点，每个组对应一棵树。在并查集中，哪个节点是哪个节点的父亲以及树的形状等信息无需多家关注，整体组成一个树形结构才是重要的。  
## (1) 初始化
我们准备n个节点来表示n个元素。最开始时没有边。  
![laoding](https://upload-images.jianshu.io/upload_images/17401788-245735ac7e30b02b.png?imageMogr2/auto-orient/strip|imageView2/2/w/373)  

## (2) 合并
像下图一样，从一个组的根向另一个组的根连边，这样两棵树就变成了一棵树，也就是把两个组合并为了一个组。  
![loading](https://upload-images.jianshu.io/upload_images/17401788-35accc67a98be24f.png?imageMogr2/auto-orient/strip|imageView2/2/w/426)  

## (3) 查询
为了查询两个节点是否属于同一组，我们需要沿着树向上走，来查询包含这个元素的树的根是谁。如果两个节点走到了同一个根，那么就可以知道他们属于同一组。
在下图，2 和 5 都走到了 1 ，因此他们为同一组。另一方面，由于 7 走到的是 6 ，因此与 2 和 5 属于不同组.  
![laoding](https://upload-images.jianshu.io/upload_images/17401788-ee51d8867624b915.png?imageMogr2/auto-orient/strip|imageView2/2/w/365)  

# 并查集实现中的注意点
在树形数据结构中，如果发生了退化的情况，那么复杂度就会变得很高。因此有必要避免退化.  

- 对于每棵树，记录这棵树的高度（rank）  
- 合并时如果两个数的rank不同，那么从rank小的向rank大的连边。  
![loading](https://upload-images.jianshu.io/upload_images/17401788-62eff46e3405a952.png?imageMogr2/auto-orient/strip|imageView2/2/w/390)  

此外，通过路径压缩，可以使得并查集更加高效。对于每个节点，一旦向上走到了一次根节点，就把这个点到父亲的边改为直接连向根。  
![loading](https://upload-images.jianshu.io/upload_images/17401788-63e252b622f7f32d.png?imageMogr2/auto-orient/strip|imageView2/2/w/376)

在此之上，不仅仅是所查询的节点，在查询过程中向上经过的所有节点，都改为直接连到根上。这样再次查询这些节点时，就可以很快知道根是谁了。  
![loading](https://upload-images.jianshu.io/upload_images/17401788-f5d4ce99dc050656.png?imageMogr2/auto-orient/strip|imageView2/2/w/374)  

# 并查集的实现
## 初始化
```c++
int fa[MAXN];
void init(int n)
{
    for (int i = 1; i <= n; ++i)
    {
        fa[i] = i;
        rank[i] = 1;
    }
}
```
假如有编号为1, 2, 3, ..., n的n个元素，我们用一个数组fa[]来存储每个元素的父节点（因为每个元素有且只有一个父节点，所以这是可行的）。一开始，我们先将它们的父节点设为自己。  

## 查询
```c++
//普通查找
int find(int x)
{
    if(fa[x] == x)
        return x;
    else
        return find(fa[x]);
}

//路径压缩
int find(int x)
{
    if(x == fa[x])
        return x;
    else{
        fa[x] = find(fa[x]);  //父节点设为根节点
        return fa[x];         //返回父节点
    }
}
```
我们用递归的写法实现对代表元素的查询：一层一层访问父节点，直至根节点（根节点的标志就是父节点是本身）。要判断两个元素是否属于同一个集合，只需要看它们的根节点是否相同即可。  

## 合并
```c++
void merge(int i, int j)
{
    int x = find(i), y = find(j);    //先找到两个根节点
    if (rank[x] <= rank[y])
        fa[x] = y;
    else
        fa[y] = x;
    if (rank[x] == rank[y] && x!=y)
        rank[y]++;                   //如果深度相同且根节点不同，则新的根节点的深度+1
}
```

合并操作也是很简单的，先找到两个集合的代表元素，然后将前者的父节点设为后者即可。当然也可以将后者的父节点设为前者，这里暂时不重要。本文末尾会给出一个更合理的比较方法。  



























