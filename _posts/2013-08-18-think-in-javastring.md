---
layout: post
author: chenDoInG
title: "Think in Java——String"
description: ""
category: 
tags: []
---
#String,StringBuffer,StringBuilder的区别

##String:字符串常量

1. **String对象是不可变的**。每当把String对象作为方法的参数时，都会复制一份引用，而该引用所指的对象其实一直待在单一的物理位置上，从未动过。
2. 每次对String类型进行改变的时候其实都生成了一个新的String对象，然后将引用指向新的String对象。因此对于经常变化的字符串最好不要使用String，比如**sql语句**，因为当内存中无法引用的对象多了以后，JVM的GC就会开始工作（可以自行查看java的内存分配回收机制），速度一定会非常慢的。

##StringBuffer:字符串变量（线程安全）

1. **线程安全的可变字符序列**。类似于String的字符串缓冲区，不能修改，但可以通过某些方法调用可以改变该序列的长度和内容。每次对StringBuffer改变时，都会对StringBuffer对象本身进行操作，而不是生成新的对象。所以在**字符串对象经常变化的情况下，推荐使用StringBuffer**
2. StringBuffer安全的用于多个线程，可以在必要时对这些方法进行同步。
2. **某些特别情况下**，String对象的字符串拼接其实是被JVM解释成StringBuffer对象的凭借，所以这些时候String对象的速度并不会比StringBuffer对象慢，比如：
        String s1 = "This " + "is " + "a" + "string";
		StringBuffer Sb = new StringBuilder(“This ”).append(“is ”).append(“a ”).append("string");

##StringBuilder:字符串变量（非线程安全）

1. **非线程安全的可变字符序列，JDK5.0新增**。与StringBuffer兼容的API，**但是不保证同步**。
2. 一般用于单线程使用的时候。

#大部分情况下，执行速度：StringBuilder > StringBuffer > String





