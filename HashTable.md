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
## 双哈希表
我们定义两个哈希表：  
- 第一个 ```freq_table``` 以频率 ```freq``` 为索引，每个索引存放一个双向链表，这个链表里存放所有使用频率为 ```freq``` 的缓存，缓存里存放三个信息，分别为键 ```key```，值 ```value```，以及使用频率 ```freq```.  
- 第二个 ```key_table``` 以键值 ```key``` 为索引，每个索引存放对应缓存在 ```freq_table``` 中链表里的内存地址(也就是```freq_table[i]```的首地址).  

这样我们就能利用两个哈希表来使得两个操作的时间复杂度均为 ```O(1)```。同时需要记录一个当前缓存最少使用的频率 ```minFreq```，这是为了删除操作服务的。   

对于 ```get(key)``` 操作，我们能通过索引 ```key``` 在 ```key_table``` 中找到缓存在 ```freq_table``` 中的链表的内存地址，如果不存在直接返回 ```-1```，否则我们能获取到对应缓存的相关信息，这样我们就能知道缓存的键值还有使用频率，直接返回 ```key``` 对应的值即可。  

但是我们注意到 ```get``` 操作后这个缓存的使用频率加一了，所以我们需要更新缓存在哈希表 ```freq_table``` 中的位置。已知这个缓存的键 ```key```，值 ```value```，以及使用频率 ```freq```，那么该缓存应该存放到 ```freq_table``` 中 ```freq + 1``` 索引下的链表中。所以我们在当前链表中 ```O(1)``` 删除该缓存对应的节点，根据情况更新 ```minFreq``` 值，然后将其 ```O(1)``` 插入到 ```freq + 1``` 索引下的链表头完成更新。这其中的操作复杂度均为 ```O(1)```. 你可能会疑惑更新的时候为什么是插入到链表头，这其实是为了保证缓存在当前链表中从链表头到链表尾的插入时间是有序的，为下面的删除操作服务。  

对于 ```put(key, value)``` 操作，我们先通过索引 ```key``` 在 ```key_table``` 中查看是否有对应的缓存，如果有的话，其实操作等价于 ```get(key)``` 操作，唯一的区别就是我们需要将当前的缓存里的值更新为 ```value```。如果没有的话，相当于是新加入的缓存，如果缓存已经到达容量，需要先删除最近最少使用的缓存，再进行插入。  

先考虑插入，由于是新插入的，所以缓存的使用频率一定是 ```1```，所以我们将缓存的信息插入到 ```freq_table``` 中 ```1``` 索引下的列表头即可，同时更新 ```key_table[key]``` 的信息，以及更新 ```minFreq = 1```。  

那么剩下的就是删除操作了，由于我们实时维护了 ```minFreq```，所以我们能够知道 ```freq_table``` 里目前最少使用频率的索引，同时因为我们保证了链表中从链表头到链表尾的插入时间是有序的，所以 ```freq_table[minFreq]``` 的链表中链表尾的节点即为使用频率最小且插入时间最早的节点，我们删除它同时根据情况更新 ```minFreq``` ，整个时间复杂度均为 ```O(1)```.  

这种解法真的比较抽象，可以看一下 [动图演示](https://leetcode-cn.com/problems/lfu-cache/solution/lfuhuan-cun-by-leetcode-solution/)，这样可能会感觉好一点，如果还是不理解，要结合代码、注解和时间来消磨。  

```c++
// 缓存的节点信息
struct Node {
    int key, val, freq;
    Node(int _key,int _val,int _freq): key(_key), val(_val), freq(_freq){}
};
class LFUCache {
    int minfreq, capacity;
    unordered_map<int, list<Node>::iterator> key_table;
    unordered_map<int, list<Node>> freq_table;
public:
    LFUCache(int _capacity) {
        minfreq = 0;
        capacity = _capacity;
        key_table.clear();
        freq_table.clear();
    }
    
    int get(int key) {
        if (capacity == 0) return -1;
        auto it = key_table.find(key);
        if (it == key_table.end()) return -1;
        list<Node>::iterator node = it -> second;
        int val = node -> val, freq = node -> freq;
        freq_table[freq].erase(node);
        // 如果当前链表为空，我们需要在哈希表中删除，且更新minFreq
        if (freq_table[freq].size() == 0) {
            freq_table.erase(freq);
            if (minfreq == freq) minfreq += 1;
        }
        // 插入到 freq + 1 中
        freq_table[freq + 1].push_front(Node(key, val, freq + 1));
        key_table[key] = freq_table[freq + 1].begin();
        return val;
    }
    
    void put(int key, int value) {
        if (capacity == 0) return;
        auto it = key_table.find(key);
        if (it == key_table.end()) {
            // 缓存已满，需要进行删除操作
            if (key_table.size() == capacity) {
                // 通过 minFreq 拿到 freq_table[minFreq] 链表的末尾节点
                auto it2 = freq_table[minfreq].back();
                key_table.erase(it2.key);
                freq_table[minfreq].pop_back();
                if (freq_table[minfreq].size() == 0) {
                    freq_table.erase(minfreq);
                }
            }
            freq_table[1].push_front(Node(key, value, 1));
            key_table[key] = freq_table[1].begin();
            minfreq = 1;
        } else {
            // 与 get 操作基本一致，除了需要更新缓存的值
            list<Node>::iterator node = it -> second;
            int freq = node -> freq;
            freq_table[freq].erase(node);
            if (freq_table[freq].size() == 0) {
                freq_table.erase(freq);
                if (minfreq == freq) minfreq += 1;
            }
            freq_table[freq + 1].push_front(Node(key, value, freq + 1));
            key_table[key] = freq_table[freq + 1].begin();
        }
    }
};
```
复杂度分析
- 时间复杂度：```get``` 时间复杂度 ```O(1)```，```put``` 时间复杂度 ```O(1)```。由于两个操作从头至尾都只利用了哈希表的插入删除还有链表的插入删除，且它们的时间复杂度 ```O(1)```，所以保证了两个操作的时间复杂度均为 ```O(1)```。  
- 空间复杂度：```O(capacity)```，其中 ```capacity``` 为 ```LFU``` 的缓存容量。哈希表中不会存放超过缓存容量的键值对。  

# 设计推特
设计一个简化版的推特(Twitter)，可以让用户实现发送推文，关注/取消关注其他用户，能够看见关注人（包括自己）的最近十条推文。你的设计需要支持以下的几个功能：
```
postTweet(userId, tweetId): 创建一条新的推文
getNewsFeed(userId): 检索最近的十条推文。每个推文都必须是由此用户关注的人或者是用户自己发出的。推文必须按照时间顺序由最近的开始排序。
follow(followerId, followeeId): 关注一个用户
unfollow(followerId, followeeId): 取消关注一个用户
```

示例:
```
Twitter twitter = new Twitter();

// 用户1发送了一条新推文 (用户id = 1, 推文id = 5).
twitter.postTweet(1, 5);

// 用户1的获取推文应当返回一个列表，其中包含一个id为5的推文.
twitter.getNewsFeed(1);

// 用户1关注了用户2.
twitter.follow(1, 2);

// 用户2发送了一个新推文 (推文id = 6).
twitter.postTweet(2, 6);

// 用户1的获取推文应当返回一个列表，其中包含两个推文，id分别为 -> [6, 5].
// 推文id6应当在推文id5之前，因为它是在5之后发送的.
twitter.getNewsFeed(1);

// 用户1取消关注了用户2.
twitter.unfollow(1, 2);

// 用户1的获取推文应当返回一个列表，其中包含一个id为5的推文.
// 因为用户1已经不再关注用户2.
twitter.getNewsFeed(1);
```
## 哈希表 + 链表
根据题意我们知道，对于每个推特用户，我们需要存储他关注的用户 ```Id```，以及自己发的推文 ```Id``` 的集合，为了使每个操作的复杂度尽可能的低，我们需要根据操作来决定存储这些信息的数据结构。注意，由于题目中没有说明用户的 ```Id``` 是否连续，所以我们需要用一个以用户 ```Id``` 为索引的哈希表来存储用户的信息。

对于操作 ```3``` 和操作 ```4```，我们只需要用一个哈希表存储，即可实现插入和删除的时间复杂度都为 ```O(n)```.  

对于操作 1 和操作 2，由于操作 2 要知道此用户关注的人和用户自己发出的最近十条推文，因此我们可以考虑对每个用户用链表存储发送的推文。每次创建推文的时候我们在链表头插入，这样能保证链表里存储的推文的时间是从最近到最久的。那么对于操作 2，问题其实就等价于有若干个有序的链表，我们需要找到它们合起来最近的十条推文。由于链表里存储的数据都是有序的，所以我们将这些链表进行线性归并即可得到最近的十条推文。这个操作与 [合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/) 基本等同。

![picture](https://assets.leetcode-cn.com/solution-static/355_fig1.png)  

如果我们直接照搬「合并K个排序链表」的解法来进行合并，那么无疑会造成空间的部分浪费，因为这个题目不要求你展示用户的所有推文，所以我们只要动态维护用户的链表，存储最近的 ```recentMax``` 个推文 ```Id``` 即可（题目中的 ```recentMax``` 为 ```10```）。那么对于操作 ```1```，当发现链表的节点数等于 ```recentMax``` 时，我们按题意删除链表末尾的元素，再插入最新的推文 ```Id```。对于操作 ```2```，在两个链表进行线性归并的时候，只要已合并的数量等于 ```recentMax```，代表已经找到这两个链表合起来后最近的 ```recentMax 条推文```，直接结束合并即可。

```c++
class Twitter {
private:
    struct Node {
        set<int> followee; // 存储关注的人的 ID
        list<int> tweet; // 存储 tweetID
    };
    // getNewsFeed 检索的推文的上限以及 tweetId 的时间戳
    int recentMax, time;
    // tweetId 对应发送的时间
    unordered_map<int, int> tweetTime;
    // 每个用户存储的信息
    unordered_map<int, Node> user;
public:
    /** Initialize your data structure here. */
    Twitter() {
        time = 0;
        recentMax = 10;
        user.clear();
    }
    
    void init(int userId) {
        user[userId].followee.clear();
        user[userId].tweet.clear();
    }
    
    /** Compose a new tweet. */
    void postTweet(int userId, int tweetId) {
        if (user.find(userId) == user.end()) {
            init(userId);
        }
        // 达到限制，剔除链表末尾元素
        if (user[userId].tweet.size() == recentMax) {
            user[userId].tweet.pop_back();
        }
        user[userId].tweet.push_front(tweetId);
        tweetTime[tweetId] = ++time;
    }
    
    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    vector<int> getNewsFeed(int userId) {
        vector<int> ans; ans.clear();
        for (list<int>::iterator it = user[userId].tweet.begin(); it != user[userId].tweet.end(); ++it) {
            ans.emplace_back(*it);
        }
        for (int followeeId: user[userId].followee) {
            if (followeeId == userId) continue; // 可能出现自己关注自己的情况
            vector<int> res; res.clear();
            list<int>::iterator it = user[followeeId].tweet.begin();
            int i = 0;
            // 线性归并
            while (i < (int)ans.size() && it != user[followeeId].tweet.end()) {
                if (tweetTime[(*it)] > tweetTime[ans[i]]) {
                    res.emplace_back(*it);
                    ++it;
                } else {
                    res.emplace_back(ans[i]);
                    ++i;
                }
                // 已经找到这两个链表合起来后最近的 recentMax 条推文
                if ((int)res.size() == recentMax) {
                    break;
                }
            }
            for (; i < (int)ans.size() && (int)res.size() < recentMax; ++i) {
                res.emplace_back(ans[i]);
            }
            for (; it != user[followeeId].tweet.end() && (int)res.size() < recentMax; ++it) { res.emplace_back(*it);
            }
            ans.assign(res.begin(),res.end());
        }
        return ans;
    }
    
    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    void follow(int followerId, int followeeId) {
        if (user.find(followerId) == user.end()) {
            init(followerId);
        }
        if (user.find(followeeId) == user.end()) {
            init(followeeId);
        }
        user[followerId].followee.insert(followeeId);
    }
    
    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    void unfollow(int followerId, int followeeId) {
        user[followerId].followee.erase(followeeId);
    }
};
```
复杂度分析
- 时间复杂度：
  - 操作 ```1```：```O(1)```，链表的插入删除为 ```O(1)``` 的复杂度。  
  - 操作 ```2``` ：```O（recentMax * num)``` 其中 ```recentMax = 10```， num 为用户关注的人加上自己的数量和。因为链表里最多存储 recentMax 个节点，因此每次合并两个链表最多比较 recentMax 次后能得到两个链表最近的 recentMax 个推文，一共需要合并 num 次，因此总时间复杂度为 ```O（recentMax * num)```。  
  - 操作 ```3``` ：```O(1)```，哈希表插入为 ```O(1)``` 的复杂度。  
  - 操作 ```4``` ：```O(1)```，哈希表插入为 ```O(1)``` 的复杂度。  
- 空间复杂度：```O(recentMax * tot)```，其中 ```recentMax = 10```，```tot``` 为推特总用户数。即对于每个用户我们不会存储超过 ```recentMax``` 个推特 ```Id```，所以空间上限为 ```O(recentMax * tot)```。 








































