---
layout: post
title: 栈与队列
category: TA
tags: [TA，Data structure，C++]
---

# 栈与队列

## 栈

- 运算只在表的一端进行
- 先进后出（FILO）一种限制访问端口的线性表
- 主要操作：进栈（push）出栈（pop）
- 应用：表达式求值、消除递归、深度优先搜索（**递归**）

栈的抽象数据类型

```c++
template <class T>
class Stack{
public:
	void clear();
	bool push(const T item);
	bool pop(T& item);
	bool top(T& item);
	bool isEmpty();
	bool isFull();
}
```

栈的实现方法

- 顺序栈：使用向量实现；确定哪一端作为栈顶；上溢、下溢问题
- 链式栈：用单链表方式存储，其中指针的方向事从栈顶向下链接

顺序栈的类定义

```c++
template <class T> class arrStack : public Stack <T>{
private:
	int mSize;
	int top;
	T * st;
public:
	arrStack(int size){
		mSize = size; 
		top = -1;
		st = new T[mSize];
	}
	arrStack(){
		top = -1;
	}
	~arrStack(){
		delete [] st;
	}
	void clear(){
		top = -1;
	}
}
```

压入栈顶（判断上溢问题）

```c++
bool arrStack<T>::push(const T item){
	if(top == mSize-1){
		cout << "栈满溢出" << endl;
		return false;
	}
	else{
		st[++top] = item;
		return true;
	}
}
```

链式栈的类定义

```
template <class T> class lnkStack : public Stack <T>{
private:
	Link<T> * top;
	int size;
public:
	lnkStack(int defsize){
		top = NULL;
		size = 0;
	}
	~lnkStack(){
		clear();
	}
}
```

压入栈顶

```c++
bool lnkStack<T>:: push(const T item){
	Link<T> * tmp = new Link<T>(item, top);
	top = tmp;
	size++;
	return true;
}
Link(const T info, Link * nextValue){
	data = info;
	next = nextValue;
}
```

**栈不允许读取内部内容**

中缀表达式 & [后缀表达式](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)（易考点：后缀表达式的转化）

后缀表达式的求值过程：遇到操作数，则压入栈顶；遇到的是一个运算符，就从栈中两次取出栈顶，按照运算符对操作数进行计算。

## 栈的应用

递归到非递归的转换

## 队列

- 运算只在表的两端进行
- 先进先出（FIFO），主要操作：入队、出队

队列的抽象数据类型

```c++
template <class T>
class Queue{
public:
	void clear();
	bool enQueue(const T item);
	bool deQueue(T& item);
	bool getFront(T& item);
	bool isEmpty();
	bool isFull();
}
```

队列的实现方法：顺序队列以及链式队列（[循环队列问题](https://leetcode-cn.com/problems/design-circular-queue/)）

Q：顺序队列和链式队列的类定义如何通过代码实现？

队列的应用：**广度优先搜索**

