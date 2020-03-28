# Definition 
>又称单词查找树，Trie树，是一种树形结构，是一种哈希树的变种。典型应用是用于统计，排序和保存大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。它的优点是：利用字符串的公共前缀来减少查询时间，最大限度地减少无谓的字符串比较，查询效率比哈希树高。  
[Trie Tree](https://baike.baidu.com/item/字典树/9825209?fr=aladdin)  
Trie tree 通常分为三种操作：插入、查找、删除，删除通常是删除整一棵树，以下就不做叙述了。  

# 声明
每个节点有26个孩子节点(对应26个字母)
```c
struct Node {
    Node* next[26];
    Node() {
        for (int i = 0; i < 26; i++) {
            next[i] = NULL;
        }
    }
};
```

# Insertion
step1：初始化

Trie树为空，只包含一个孤零零的根结点(根结点是不放值的)  
![p1](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809160016864-1312171223.png)


step2：插入单词（注：这里假设所有单词仅由小写字母构成）

插入一个单词的步骤如下：

（1）对于Trie树，我们从根结点开始，设该节点为P；对于这个单词，我们从第一个字母开始，设此字符为s

（2）扫描P下方的所有边，看s有没有出现过

如果出现了，设s与P→Q这条边上的字符相同，则P=Q

如果没有出现，另建一条边，使该边上的字母为s，新节点为Q，然后P=Q

（3）s变为该单词的下一个字符，重复步骤2，直到扫描完整个单词为止

首先我们要插入abc这个单词

（1）P为根结点，s为'a'  
发现根结点下方无'a'，所以新建一条边  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809161934325-1402015562.png)  

（2）P为a下方那个结点，s为'b'  
发现P下方无'b'，所以新建一条边  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809162130285-201372290.png)

（3）P为b下方那个结点，s为'c'  
发现P下方无'c'，所以新建一条边  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809162240340-287737996.png)

（4）发现abc这个单词插入完成，所以在当前的s做个标记，表示abc是一个出现过的单词  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809162343734-709153216.png)  

接着我们按照相同的步骤插入bcd、efg和hij  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809162758875-316399594.png)  

接下来插入abcd  

（1）P为根结点，s为'a'  
发现根结点下方有'a'，所以P变为'a'下方的结点  

（2）P为a下方那个结点，s为'b'  
发现P下方有'b'，所以P变为'b'下方的结点  

（3）P为b下方那个结点，s为'c'  
发现P下方有'c'，所以P变为'c'下方的结点  

（4）P为c下方那个红色的结点，s为'd'  
发现P下方无'd'，所以所以新建一条边  

（5）单词abcd插入完毕，将当前的s做标记  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180809163213337-543840241.png)

下面按照相同的方法，继续插入单词abd和b    

插入完成后Trie树如下：  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180813132818017-351825802.png)

```c
Node *root;
void Insert(char *str){
    Node *loc = root, *temp;
    for(int i = 0; str[i] != '\0'; i++){
        //id的数值其实就是字母编号
        int id = str[i] - 'a';
        if(loc->next[id] != NULL){
            loc = loc->next[id];
        } else {
            tmp = new Node;
            loc->next[id] = temp;
            loc = loc->next[id];
        }
    }
}
```
# Search
词典有了，接下来就可以查词了

在trie树中查单词就跟查字典一样。先查首字母，然后第二个，第三个……

查找过程跟插入的过程很像：

（1）对于Trie树，我们从根结点开始，设该节点为P；对于这个单词，我们从第一个字母开始，设此字符为s

（2）扫描P下方的所有边，看s有没有出现过

如果出现了，设s与P→Q这条边上的字符相同，则P=Q

如果没有出现，则该单词没有出现过，直接返回false

（3）s变为该单词的下一个字符，重复步骤2，直到扫描完整个单词为止

（4）扫描完成后，判断节点P有没有被标记（是不是某个出现过的单词的结尾）。如果标记了，那么该单词出现过，返回true；如果没有标记，那么该单词是词典中这个单词的前缀，返回false。

对于（4）讲解一下：

还是这棵Trie树：  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180813132818017-351825802.png)  
我们查找ef这个单词：

（1）P为根结点，s为'e'

发现根结点下方有'e'，所以P变为'e'下方的结点

（2）P为e下方那个结点，s为'f'

发现P下方有'f'，所以P变为'f'下方的结点

（3）查找完成，发现P这里没有标记，所以ef是词典中单词efg的前缀，而不是直接出现在了词典里，返回false  
![p](https://images2018.cnblogs.com/blog/979924/201808/979924-20180820151521884-1890466196.png)  
再举一个例子，我们查找abd这个单词：

（1）P为根结点，s为'a'

发现根结点下方有'a'，所以P变为'a'下方的结点

（2）P为a下方那个结点，s为'b'

发现P下方有'b'，所以P变为'b'下方的结点

（3）P为b下方那个结点，s为'd'

发现P下方有'd'，所以P变为'd'下方的结点

（3）查找完成，发现P这里有标记，所以abd是词典中的单词，返回true

```c
int SearchTrie(char *str){
    Node *loc = root;
    for(int i = 0; str[i] != '\0'; i++){
        int id = str[i] - 'a';
        if (loc->next[id] != NULL)
            loc = loc->next[id];
        else
            return 0;
    }
    return 1;
}
```
# Babelfish(前缀树)
## Description

You have just moved from Waterloo to a big city. The people here speak an incomprehensible dialect of a foreign language.   Fortunately, you have a dictionary to help you understand them.  
## Input

Input consists of up to 100,000 dictionary entries, followed by a blank line, followed by a message of up to 100,000 words. Each dictionary entry is a line containing an English word, followed by a space and a foreign language word. No foreign word appears more than once in the dictionary. The message is a sequence of words in the foreign language, one word on each line. Each word in the input is a sequence of at most 10 lowercase letters.
## Output

Output is the message translated to English, one word per line. Foreign words not in the dictionary should be translated as "eh".
## Sample Input

dog ogday  
cat atcay  
pig igpay  
froot ootfray  
loops oopslay  

atcay  
ittenkay  
oopslay  
## Sample Output

cat  
eh  
loops  
## Hint

Huge input and output,scanf and printf are recommended.

按照上述操作将每一个陌生单词插入到字典树中，然后每个标记为红色的节点中存储英语单词的值，以便完成查找
```c
#include<stdio.h>
#include<string.h>

struct Node {
    char English[15];
    Node *next[26];
    Node() {
        for(int i = 0; i < 26; i++)
            next[i] = NULL;
    }
};

Node *root;
void Insert(char *English, char *new_words){
    Node *loc = root, *temp;
    for(int i = 0; new_words[i] != '\0'; i++){
        int id = new_words[i] - 'a';
        if(loc->next[id] != NULL){
            loc = loc->next[id];
        } else {
            temp = new Node;
            loc->next[id] = temp;
            loc = loc->next[id];
        }
    }
    strcpy(loc->English, English);
}

int SearchTrie(char *new_words, char *English){
    Node *loc = root;
    for(int i = 0; new_words[i] != '\0'; i++){
        int id = new_words[i] - 'a';
        if (loc->next[id] != NULL)
            loc = loc->next[id];
        else
            return 0;
    }
    strcpy(English, loc->English);
    return 1;
}

int main(){
    void Insert(char *English, char *new_words);
    int SearchTrie(char *new_words, char *English);
    root = new Node;
    char English[15],new_words[15];
    char buff[40];
    while(gets(buff) && strcmp(buff, "") != 0) {
        sscanf(buff, "%[^ ]%[^\n]", English, new_words);
        Insert(English, new_words);
    }
    while(gets(new_words)){
        int flag = SearchTrie(new_words, English);
        if(flag)
            printf("%s\n", English);
        else
            printf("eh\n");
    }
    return 0;
}
``` 
---
# 单词的压缩编码(后缀树)
给定一个单词列表，我们将这个列表编码成一个索引字符串 ```S``` 与一个索引列表 ```A```。

例如，如果这个列表是 ```["time", "me", "bell"]```，我们就可以将其表示为 ```S = "time#bell#"``` 和 ```indexes = [0, 2, 5]```。

对于每一个索引，我们可以通过从字符串 ```S``` 中索引的位置开始读取字符串，直到 ```"#"``` 结束，来恢复我们之前的单词列表。

那么成功对给定单词列表进行编码的最小字符串长度是多少呢？

示例：
```
输入: words = ["time", "me", "bell"]
输出: 10
说明: S = "time#bell#" ， indexes = [0, 2, 5] 。
```
提示：
```
1 <= words.length <= 2000
1 <= words[i].length <= 7
每个单词都是小写字母 。
```
thinkings:从后往前的话，有一些单词就会被覆盖，就像下图一样，```me``` 会被 ```time``` 覆盖，以达到题目的要求.  
![p](https://assets.leetcode-cn.com/solution-static/820_2.jpg)  
```c++
struct TrieNode {
    TrieNode* next[26];
    TrieNode() {
        memset(next, NULL, sizeof(next));
    }
};

void Insert(TrieNode*& root, string s) {
    TrieNode* local = root, *temp;
    int len = s.size();
    for (int i = len - 1; i >= 0; i--) {
        int id = s[i] - 'a';
        if (local->next[id] != NULL) {
            local = local->next[id];
        } else {
            temp = new TrieNode();
            local->next[id] = temp;
            local = local->next[id];
        }
    }
}

int CountLeaveHeight(TrieNode* root, int height) {
    bool is_leaf = true;
    for (int i = 0; i < 26; i++) {
        if (root->next[i] != NULL) {
            is_leaf = false;
            break;
        }
    }
    if (is_leaf) {
        return height;
    }
    int sum = 0;
    for (int i = 0; i < 26; i++) {
        if (root->next[i]) {
            sum += CountLeaveHeight(root->next[i], height + 1);
        }
    }
    return sum;
}

int minimumLengthEncoding(vector<string>& words) {
    TrieNode* root = new TrieNode();
    for (int i = 0; i < words.size(); i++) {
        Insert(root, words[i]);
    }
    return CountLeaveHeight(root, 1);
}
```

























