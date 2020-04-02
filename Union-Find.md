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
在下图，2 和 5 都走到了 1 ，因此他们为同一组。另一方面，由于 7 走到的是 6 ，因此与 2 和 5 属于不同组  
![laoding](https://upload-images.jianshu.io/upload_images/17401788-ee51d8867624b915.png?imageMogr2/auto-orient/strip|imageView2/2/w/365)  
































