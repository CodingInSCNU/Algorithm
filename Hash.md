# 字母异位词
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。  

示例:
```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```
说明：  

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。
```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> result;
        unsigned long n_size = strs.size();
        unordered_map<string, int> m;
        int index = 0;
        
        for (int i = 0; i < n_size; i++) {
            string temp = strs[i];
            sort(temp.begin(), temp.end());
            if (m.count(temp)) {
                result[m[temp]].push_back(strs[i]);
            }
            else {
                m[temp] = index++;
                vector<string> vec;
                vec.push_back(strs[i]);
                result.push_back(vec);
            }
        }
    
        return result;
    }
};
```

# LFU缓存
设计并实现[最不经常使用（LFU）缓存](https://baike.baidu.com/item/缓存算法)的数据结构。它应该支持以下操作：```get``` 和 ```put```。  

```get(key)``` - 如果键存在于缓存中，则获取键的值（总是正数），否则返回 ```-1```。  
```put(key, value)``` - 如果键不存在，请设置或插入值。当缓存达到其容量时，它应该在插入新项目之前，使最不经常使用的项目无效。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，最近最少使用的键将被去除。  

进阶：  
你是否可以在 ```O(1)``` 时间复杂度内执行两项操作？
示例：
```
LFUCache cache = new LFUCache( 2 /* capacity (缓存容量) */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // 返回 1
cache.put(3, 3);    // 去除 key 2
cache.get(2);       // 返回 -1 (未找到key 2)
cache.get(3);       // 返回 3
cache.put(4, 4);    // 去除 key 1
cache.get(1);       // 返回 -1 (未找到 key 1)
cache.get(3);       // 返回 3
cache.get(4);       // 返回 4
```

## 哈希表 + 平衡二叉树
### 首先我们先定义存于平衡二叉树中的数据结构
```c++
struct Node {
    int cnt;
    int time;
    int key, value;
    Node(int _cnt, int _time, int _key, int _value):cnt(_cnt), time(_time), key(_key), value(_value) {}
    
    // 我们需要实现一个 Node 类的比较函数
    // 将 cnt（使用频率）作为第一关键字，time（最近一次使用的时间）作为第二关键字
    // 下面是 C++ 语言的一个比较函数的例子
    bool operator < (const Node& T) const {
        return cnt == T.cnt ? time < T.time : cnt < T.cnt;
    }
};
```
其中 ```cnt``` 表示缓存使用的频率，```time``` 表示缓存的使用时间，```key``` 和 ```value``` 表示缓存的键值。  

比较直观的想法就是我们用哈希表 ```key_table``` 以键 ```key``` 为索引存储缓存，建立一个平衡二叉树 ```S``` 来保持缓存根据 ```(cnt，time)``` 双关键字. 由于在 C++ 中，我们可以使用 STL 提供的 ```std::set``` 类，```set``` 背后的实现是红黑树.  
- 对于 ```get(key)``` 操作，我们只要查看一下哈希表 ```key_table``` 是否有 ```key``` 这个键即可，有的话需要同时更新哈希表和集合中该缓存的使用频率以及使用时间，否则返回 ```-1```。  
- 对于 ```put(key, value)``` 操作，首先需要查看 ```key_table``` 中是否已有对应的键值。如果有的话操作基本等同于 ```get(key)```，不同的是需要更新缓存的 ```value``` 值。如果没有的话相当于是新插入一个缓存，这时候需要先查看是否达到缓存容量 ```capacity```，如果达到了的话，需要删除最近最少使用的缓存，即平衡二叉树中最左边的结点，同时删除 ```key_table``` 中对应的索引，最后向 ```key_table``` 和 ```S``` 插入新的缓存信息即可。  

```c++
struct Node {
    int cnt;
    int time;
    int key, value;
    Node(int _cnt, int _time, int _key, int _value):cnt(_cnt), time(_time), key(_key), value(_value) {}
    bool operator < (const Node& T) const {
        return cnt == T.cnt ? time < T.time : cnt < T.cnt;
    }
};

class LFUCache {
    // 缓存容量，时间戳
    int capacity, time;
    unordered_map<int, Node> key_table;
    set<Node> S;
public:
    LFUCache(int _capacity) {
        capacity = _capacity;
        time = 0;
        key_table.clear();
        S.clear();
    }
    
    int get(int key) {
        if (capacity == 0) {
            return -1;
        }
        auto it = key_table.find(key);
        // 如果哈希表中没有键 key，返回 -1
        if (it == key_table.end()) {
            return -1;
        }
        // 从哈希表中得到旧的缓存
        Node cache = it->second;
        // 从平衡二叉树中删除旧的缓存
        S.erase(cache);
        cache.cnt++;
        cache.time = ++time;
        // 将新缓存重新放入哈希表和平衡二叉树中
        S.insert(cache);
        it->second = cache;
        return cache.value;
    }
    
    void put(int key, int value) {
        if (capacity == 0) {
            return;
        }
        auto it = key_table.find(key);
        if (it == key_table.end()) {
            // 如果到达缓存容量上限
            if (key_table.size() == capacity) {
                key_table.erase(S.begin()->key);
                S.erase(S.begin());
            }
            // 创建新的缓存
            Node cache = Node(1, ++time, key, value);
            // 将新缓存放入哈希表和平衡二叉树中
            key_table.insert(make_pair(key, cache));
            S.insert(cache);
        } else {
            // 这里和 get() 函数类似
            Node cache = it -> second;
            S.erase(cache);
            cache.cnt++;
            cache.time = ++time;
            cache.value = value;
            S.insert(cache);
            it -> second = cache;
        }
    }
};
```
复杂度分析
- 时间复杂度：```get``` 时间复杂度 ```O(logn)```, ```put``` 时间复杂度 ```O(logn)```, 操作的时间复杂度瓶颈在于平衡二叉树的插入删除均需要 ```O(logn)``` 的时间。  
- 空间复杂度：```O(capacity)```, 其中 *capacity* 为 ```LFU``` 的缓存容量。哈希表和平衡二叉树不会存放超过缓存容量的键.  























