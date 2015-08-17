---
layout: post
title: "learning at effect java 6"
description: ""
category: [java]
tags: [Effect Java]
---
{% include JB/setup %}
# 消除过期的对象引用

## **只要是自己管理内存，就应该警惕内存泄露问题**。一旦元素被释放掉，该元素中包含的任何对象引用都应该被清空。

Tips：自行了解对象和对象引用

Tips：比如自己实现的数组、堆栈、队列等经常会用到内存，使用时应该及其注意

### 举个例子

        public class Stack{
            private Object[] elements;
            private int size = 0;
            private static final int DEFAULT_INITIAL_CAPACITY = 16;
            public Stack(){
                 elements = new Object[DEFAULT_INITIAL_CAPACITY];
            }
       
             public void push(Object o){
                 ensureCapacity();
                 elements[size++] = o;
             }
       
             public Object pop(){
                if (size == 0) {
                    throw new EmptyStackException();
                }
                return elements[--size];
             }
            private void ensureCapacity(){
                if(elements.length == size)
                    elements = Arrays.copyOf(elements, 2*size + 1);
            }
        }
       
对象出栈后没有被清空引用，那么该栈会一直保留着这些已经出栈元素的引用，而且永远不会被解除引用。

        修改：
        public Object pop(){
            if (size == 0) {
                throw new EmptyStackException();
            }
            Object result = elements[--size];
            elements[size] = null;//清空引用，该引用已经不会给引用到了
            return result;
        }
       
内存泄露通常不会表现成明显的失败，只有通过仔细检查代码，或者借助jvm内存管理工具分析内存泄露问题。

**清空对象引用应该是一种例外，而不是一种规范行为。**消除过期引用最好的方法是让包含该引用的变量结束其生命周期。比如：让局部变量的作用域范围最小化。