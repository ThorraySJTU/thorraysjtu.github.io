---
layout: post
title: 数据结构：Trie树
category: Data Structure
tags: [Data Structure, Trie Tree]
---
# Trie树（字典树）

## Trie树的应用

1. 自动补全
2. 拼写检查
3. IP路由
4. T9打字预测
5. 单词游戏

前缀树用于检索字符串数据集中的键，平衡树和哈希表也可以在字符串数据集中搜索单词，哈希表可以在O(1)时间讯息好键值，但是无法完成

- 找到具有统一前缀的全部键值
- 按字典序枚举字符串的数据集

## Trie树的节点结构

- 最多R个指向子节点的连接，每个连接对应字母表数据集中的一个字母
- 布尔字段，以指定节点是对应键的结尾还是只是键前缀

```c++
// 多叉树的节点
struct TreeNode{
    VALUETYPE value;
    TreeNode * children[NUM]; // 指向孩子节点
}
```

```c++
// Trie的节点
struct TrieNode{c++
    bool isEnd;
    TreeNode* next[26]; // 字母映射表
}
```

TrieNode * next[26]保存了对当前节点而言的下一个可能出现的所有字符的连接。

```c++
for(int i = 0; i < 26; i++){
    char ch = 'a' + i;
    if(parentNode->next[i] == NULL)
        //说明父节点的后一个字母不可为ch
    else
        // 说明父节点的后一个字母可以为ch
}
```

## 常用操作方法

### 定义类

```c++
class Trie{
    private:
        bool isEnd;
        Trie* next[26];
    public:
        // 方法实现
}
```

### 插入

向Trie中插入一个单词word

```c++
void insert(string s){
    Trie* node = this;
    for(auto c:s){
        if(node->next[c-'a'] == NULL)
            node->next[c-'a'] = new Trie();
        node = node->next[c-'a'];
    }
    node->isEnd = true;
}
```

### 查找

查找Trie中是否存在单词word

```c++
bool query(string s){
    Trie* node = this;
    for(auto c:s){
        node = node->next[c-'a'];
        if(node == NULL)
	    return false;
    }
    return node->isEnd;
}
```

### 前缀匹配

查找Trie中是否有以prefix为前缀的单词

```c++
bool startWith(string prefix){
    Trie* node = this;
    for(auto c:prefix){
        node = node->next[c-'a'];
	if(node == NULL)
	    return false;
    }
    return true;
}
```



## Trie树的c++数组形式实现 (Acwing 代码模板)

```c++
const int N = 10000;
int son[N][26];
int cnt[N];
int idx;
void insert(string s){
    int next = 0; // 下一个插入的层数
    int length = s.size();
    for(int i = 0; i < length; i++){
	int u = s[i]-'a';
	if(son[next][u] == 0) son[next][u] = ++idx;
	next = son[next][u];
    }
    cnt[next]++;
}
int query(string s){
    int next = 0; // 下一个插入的层数
    int length = s.size();
    for(int i = 0; i < length; i++){
	int u = s[i]-'a'; // 计算下标
	if(son[next][u] == 0) return 0;
	next = son[next][u];
     }
     return cnt[next];
}
```

idx是新需要插入的节点的编号，trie树高效的原因是储存了公共前缀，所以前缀节点再次插入的时候，不需要额外的储存，idx只需要新储存的节点编号。

son矩阵第i行不为0，说明存在某个字母。它储存的值，是下一个新插入元素的位置。

cnt是用来对应停止符的数量（可表示以某个字符停止的单词个数）
