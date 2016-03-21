---
title: C++中memset的用法
date: 2015-10-21 00:50:43
tags:
categories: C++基础
---

> 最近沉浸在刷西西里算法题中不可自拔，其中经常有用到memset这个函数，用它来给数组进行清零实在有些方便，但是有一次我尝试使用这个函数给int数组赋值时却缺出现奇怪的错误，这时我发现虽然我有经常用这个函数，但是对它的用法一无所知，于是在次重新研究下这个函数的用法。

## 错误先行

就先讲讲我用这个函数给数组赋值时出现的错误先吧，下面是出错代码：

    #include <iostream>
    #include <cstring>

    using namespace std;

    int main() {
        int a[5];
        memset(a, 1, 5);
        cout << a[0] << endl;
    }

这里我原本的期望输出是`1`，但是结果确是`16843009`。这是为什么呢？且听下面讲解。

## 官方介绍

[C++文档](http://www.cplusplus.com/reference/cstring/memset/?kw=memset)是这样描述的

> `void * memset ( void * ptr, int value, size_t num );`

> __Fill block of memory__
> Sets the first num bytes of the block of memory pointed by `ptr` to the specified `value` (interpreted as an unsigned char).

## 个人理解

其实这个函数的官方介绍还是很好理解的，就是用一个`value`在内存空间内填充指定的数组，这个都没有什么特殊的。但是细心的人应该注意到了最后括号内的说法。没错！这就是破解最上面的错误的关键。

文档中还有对各个参数的讲解，其中对第二个参数`value`参数的描述是这样的：

> __value__
> Value to be set. The value is passed as an int, but the function fills the block of memory using the unsigned char conversion of this value.

没错！它的意思就是这里的value虽然在参数中是int类型，但是它是被当成char类型的。要知道int类型有32位而char类型就只有8位！这样我输入的1是被当成`0x01`而不是`0x00000001`。这样也可以知道为什么会输出`16843009`了，我将内存连续设置为`0x01`，这样我读取时读取了一个int类型共32位，实际读到的就是`0x01010101`转化成十进制就是`16843009`。

## 个人感悟

还是太嫩了！
