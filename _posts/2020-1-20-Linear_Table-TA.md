---
layout: post
title: 线性表
category: TA
tags: [TA，Data structure，C++]
---

# 线性表

## 线性表

- 线性表是0个或者多个元素的有穷序列（操作灵活，长度易于增长、缩短）
- 线性结构，有唯一一个开始结点（没有前驱，有唯一的直接后继），一个唯一终止结点（有唯一直接前驱，没有后继），其他内部结点（均有唯一直接前驱和唯一直接后继）
- 前驱/后继关系有反对称性和传递性
- 特点：对于同以线性表的各数据元素必定具有相同的数据类型和长度；各数据元素在线性表中有自己的位置，且数据元素之间的相对位置是线性的。
- 线性结构
  - 按复杂程度划分：线性表、栈、队列、散列表 / 广义表、多维数组、文件
  - 按访问方式划分：直接访问、顺序访问（链表）、目录索引（字典、散列表）
- 线性表的逻辑结构
  - 主要属性：线性表的长度、表头、表尾、当前位置
  - 线性表的运算：建立线性表、清楚线性表、插入一个新元素、删除某个元素、修改某个元素、排序、检索
- 线性表的存储结构
  - 顺序表：按索引值从小到大存放在一片相邻的连续区域；节凑结构，存储密度为1
  - 链表：通过指针连接的方式表达逻辑序的关系（单链、双链、循环链）

```c++
template <class T> class List{
	void clear();//置空线性表
	bool isEmpty();//线性表为空时，返回true
	bool append(const T value);//表尾添加一个元素value
	bool insert(const int p, const T value);//在位置p上插入一个元素value
	bool delete(const int p);//删除位置p上的元素
	bool getPos(int & p, const T value);//查找值为value的元素返回其位置
	bool getValue(const int p, T& value);//把位置p元素值返回到变量value中
	bool setValue(const int p, const T value);//用value修改位置p的元素值
}
```



## 顺序表

- 顺序表也称向量，采用定长的一维数组存储结构。
- 主要特征：元素类型相同；元素顺序存储在连续存储空间中，每一个元素有唯一索引值

```c++
clas arrlist : public List<T>{
	private:
		T * alist;
		int maxSize;
		int curLen;
		int position;
    public:
    	arrlist(const int size){
    		maxSize = size;
    		alist = new T[maxSize];
    		curLen = position = 0;
    	}
    	~arrlist(){
    		delete [] alist;
    	}
    	void clear(){
    		delete [] alist;
    		curLen = position = 0;
    		alist = new T[maxSize];
    	}
    	int length;
    	bool append(const T value);
		bool insert(const int p, const T value);
		bool delete(const int p);
		bool getPos(int & p, const T value);
		bool getValue(const int p, T& value);
		bool setValue(const int p, const T value);

}
```

顺序表的插入：将待插入位置之后的元素每一个都向后移一位，将插入元素填入所移出的空位来

```c++
template <class T> bool arrlist<T> :: insert(const int p, const T value)
{
	int i;
    //顺序表是否溢出
	if(curLen >= maxSize){
		cout<<"This list is overflow"<<endl; 
		return false;
	}
    //插入位置是否合法
	if(p < 0 || p > curLen){
		cout<<"Insertion point is illegal"<<endl;
		return false;
	}
	for(i = curLen; i > p ; i--)
		alist[i] = alist[i-1];
	alist[p] = value;
	curLen++;//关键步骤
	return true;
}
```

顺序表的删除

```c++
template <class T> bool arrlist<T> :: insert(const int p, const T value)
{
	int i;
    //顺序表是否为空
	if(curLen <= 0){
		cout<<"No element to delete"<<endl; 
		return false;
	}
    //检查删除位置是否合法
	if(p < 0 || p > curLen-1){
		cout<<"Delete point is illegal"<<endl;
		return false;
	}
	for(i = p; i < curLen-1 ; i++)
		alist[i] = alist[i+1];
	curLen--;//关键步骤
	return true;
}
```

Q1：顺序表的优缺点 ？Q2：插入删除的时间代价为O(n)的计算 ？

## 链表

- 通过指针把它的一串存储系欸但链接成一个链，存储结点由两部分组成（数据域+指针域）
- 链表分为：单链、双链、循环链

### 单链表

| 简单的单链表            | 带头结点的单链表                     |
| ----------------------- | ------------------------------------ |
| 整个单链表：head        | 整个单链表：head                     |
| 第一个结点：head        | 第一个结点：head->next, head != NULL |
| 空表判断： head == NULL | 空表判断： head->next == NULL        |
| 当前结点： curr         | 当前结点： fence->next               |

单链表的结点类型：

```c++
template <class T> class link{
	public:
		T data;
		link<T> * next;
		link(const T info, const link<T> * nextValue = NULL){
			data = info;
			next = nextValue;
		}
		link(const link<T> * nextValue){
			next = nextValue;
		}
}
```

单链表类定义

```c++
template <class T> class linklist : public link<T>{
	private:
		link<T> * head, * tail;
		link<T> * setPos(const int p);
	public:
		initlist(int s);
		~linklist();
		bool isEmpty();
		void clear();
		int length();
		bool append(const T value);
		bool insert(const int p, const T value);
		bool delete(const int p);
		bool getValue(const int p, T& value);
		bool getPos(int & p, const T value);
}
```

查找单链表中第i个结点

```c++
template <class T> link<T> * linklist<T>::setPos(int i){
	int count = 0;
	if( i == -1 )
		return head;//返回虚的头结点
	link<T> *p = head->next;//从第一个有效结点出发
	while( p != NULL && count < i)
	{
		p = p->next;
		count++;
	}
	return p;
}
```

单链表的插入：创建一个新元素；新节点指向右边的结点；左边的结点指向新节点

```c++
template <class T> bool linklist<T>::insert(const int i, const T value){
	link<T> *p, *q;
	if((p = setPos(i-1)) == NULL){//p是第i个结点的前驱
		cout<<"Invalid"<<endl;
		return false;
	}
	q = new link<T>(value, p->next);
	p->next = q;
	if(p == tail)
		tail = q;
	return true;
}
```

单链表的删除：用p指向元素x的结点的前驱结点；删除元素为x的结点；释放x占据的空间

```c++
template <class T> bool linklist<T>::delete(const int i){
	link<T> *p, *q;
	if((p = setPos(i-1)) == NULL|| p == tail){//p是第i个结点的前驱
		cout<<"Invalid"<<endl;
		return false;
	}
	q = p->next;
	if(q == tail){
		tail = p;
		p->next = NULL;
	}
	else
		p->next = q->next;
	delete q;
	return true;
}
```

### 双链表

单链表的next字段仅仅指向后继结点，不能有效地找到前驱；故在双链表中我们增加一个前驱指针。

双链表及其结点类型的说明

```c++
template <class T> class link{
	public:
		T data;
		link<T> * next;
		link<T> * prev;
		link(const T info, link<T> * nextValue = NULL, link<T> * preValue = NULL){
			data = info;
			next = nextValue;
			prev = preValue;
		}
		link(link<T> * nextValue = NULL, link<T> * preValue = NULL){
			next = nextValue;
			prev = preValue;
		}
}
```

### 循环链表

将单链表或者双链表的头尾结点链接起来，不存在额外存储花销

### 链表的边界条件

- 特殊点的处理：头指针处理 / 非循环链表尾结点的指针域保持为NULL / 循环链表尾结点的指针回指头结点
- 链表处理：空链表的特殊处理 / 插入或者删除时指针操作的顺序

## 顺序表和链表的比较

主要优点

| 顺序表                     | 链表                               |
| -------------------------- | ---------------------------------- |
| 没有指针，不用花费额外开销 | 无需事先了解线性表的长度           |
| 线性表元素的读访问便利     | 允许线性表的长度动态变化           |
|                            | 能够适应经常插入删除内部元素的情况 |

顺序表和链表的选择

| 顺序表                             | 链表                               |
| ---------------------------------- | ---------------------------------- |
| 结点总数目大概可以估计             | 结点数无法预知                     |
| 线性表中结点比较稳定（插入删除少） | 线性表中结点动态变化（插入删除多） |

