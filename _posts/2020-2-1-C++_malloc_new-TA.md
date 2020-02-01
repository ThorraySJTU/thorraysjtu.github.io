---
layout: post
title: TA-4：C++中malloc/free与new/delete浅析
category: TA
tags: [TA，C++]
---

# C++中malloc/free与new/delete浅析

## 摘要

new/delete通常来说是操作符，可以进行重载。malloc/free是C++/C语言的标准函数库。具体内容将从四个方面比较、分析malloc/free，new/delete的区别。

- 自动地分配所需空间
- 自动返回指针类型
- 初始化不同
- 源码实现不同

## 内容

### 操作对象范围不同

new/delete 是 C++ 里才有的，而 new/delete 与 malloc/free 一个显著的区别在于，new 是建造一个对象，并调用对象的构造函数来初始化对象，其实在所有的 new 操作过程中，总是分为两步的：第一步是**申请内存**，第二步则是**调用构造函数初始化对象**（也有文章指出，第三步是返回指针所指向对象的类型和地址）。同样，在调用 delete 的时候，需要先调用析构函数，然后在销毁堆内存。换言之 ， 对于非内部数据类型的对象而言，光用 malloc/free 无法满足动态对象的要求。对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。由于 **malloc/free 是库函数而不是运算符**，不在编译器控制权限之内，**不能够把执行构造函数和析构函数的任务强加于 malloc/free** 。如果用 free 释放“ new 创建的动态对象”，那么该对象因无法执行析构函数而可能导致程序出错。如果用 delete 释放“ malloc 申请的动态内存”，理论上讲程序不会出错，但是该程序的可读性很差。C++ 默认的 new/delete 操作符内部，其实也调用了 malloc/free 这两个函数。

### 使用方法上的区别

1. new能够按照变量类型自动地分配所需空间长度，不必使用宏sizeof。
2. new能够自动返回正确的指针类型。
3. new能将单个变量初始化。

**malloc**：类型转换+sizeof

`int *p = (int *) malloc (sizeof(int) * length); `

**malloc函数**

`void * malloc(size_t size)`

- malloc返回值的类型是 void *，所以在调用 malloc 时要显式地进行类型转换，将 void * 转换成所需要的指针类型。

- malloc 函数本身并不识别要申请的内存是什么类型，它只关心内存的总字节数。

**free函数**

`void * free(void * memblock)`

free(p)中指针p的类型以及它所指的内存内容是知道的，所以free(p)可以正确释放内存。如果p是NULL指针，那么free(p)操作多少次都不会出错；如果p不是NULL指针，那么free对p连续两次操作就会导致程序运行错误。

**new**

`int *p = new int[length]`

new 内置了sizeof、类型转换和类型安全检查功能。对于非内部数据类型的对象而言，new 在创建动态对象的同时完成了初始化工作。

如果用new创建对象数组，只能使用对象的无参数构造函数，不可在创建的同时进行赋值。

**delete**

在用delete释放对象数组时，不要丢掉符号[ ]。

`delete []objects;//正确用法`

`delete objects;//错误用法`  相当于delete objects[0]

