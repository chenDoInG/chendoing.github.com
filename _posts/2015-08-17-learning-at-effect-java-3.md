---
layout: post
author: chenDoInG
title: "learning at effect java 3"
description: ""
category: [java]
tags: [Effect Java]
---
# 用私有构造器或者枚举类型强化Singleton属性

**Tips**：Singleton模式->单件模式/单例模式，有兴趣看看它的三种实现方式：急切实例化，延迟实例化，双重检查加锁实例化

## 使用单个元素的枚举类型来实现Singleton模式

        public enum Elvis{
            INSTANCE;
            public void leaveTheBuilding(){
                  System.out.println("枚举强化单件属性");
            }
        }
       
**优点**：
1.更加简洁
2.防止多次实例化,即使是在面对复杂的序列化或者反射攻击

Tips：自行Google->java序列化和反序列化