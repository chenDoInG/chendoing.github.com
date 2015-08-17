---
layout: post
title: "Learning at Effect Java 2"
description: ""
category: [java]
tags: [Effect Java]
---
{% include JB/setup %}
# 遇到多个构造器参数时考虑用构建器

## 原因

### 静态工厂和构造器有个共同的局限性：不能很好地扩展到大量的可选参数。

现行解决方法

1.使用重叠构造器：
       
        public class NutritionFacts{
            private NutritionFacts(int servings){ ...//初始化}
            private NutritionFacts(int servings, int calories){ ...}
            private NutritionFacts(int servings, int calories, int fat){ ...}
        }
       
但是这样的构造器多了许多你不想要设置的参数，还不得不为它们传递值。

缺点：非必填参数比较多时，客户端代码会很难编写，并且难以阅读。而且如果客户端不小心颠倒了其中两个参数的顺序，编译器不会出错，但是程序运行时会出现错误。

2.使用JavaBean，调用一个无参构造器来创建对象，调用setter方法来设置每个必要的参数，以及可选参数：

          public class NutritionFacts {
            private int servings = -1;
            public NutritionFacts(){}
            public setServings(int val){ servings = val;}
        }
     
缺点：在构建过程中JavaBean可能处于不一致的状态（不一致的状态：我的理解是必填参数可能没有设置，导致调用过程中导致报错，如果有人理解这里说的不一致的状态可以和我讲解下）。另外JavaBean阻止了把**类做成不可变的可能**，需要付出额外的努力来确保它的线程安全（有兴趣的可以去了解下什么叫线程安全和非线程安全）。

3.使用构建器模式：不直接生成想要的对象，让客户端利用所有必要的参数调用构造器，得到一个builder对象；然后在builder对象上调用类似于setter的方法，来设置每个相关的可选参数；最后在客户端调用无参的build方法来生成不可变对象。

          public class NutritionFacts {
            private final int servings;
            private final int calories;
            private final int fat;
           
            public static class Builder {
                private int servings = 0;
                     private int calories = 0;
                private int fat = 0;
                //传入必填参数
                public Builder(int servings,int calories){
                    this.servings = servings;
                    this.calories = calories;
                }
                //非必填参数
                public Builder fat(int val){
                    this.fat = val;
                    return this;
                }
                //返回一个不可变的NutritionFacts
                public NutritionFacts build(){
                    return new NutritionFacts(this);
                }
           }
          
           private NutritionFacts(Builder builder){
                 servings = builder.servings;
                 calories = builder.calories;
                 fat = builder.fat;
           }
     }
    
**优点**：builder像构造器一样，可以对参数强加约束条件。有多少个可变参数，就可以根据需要设置多少可变参数

缺点：构建器模式的开销比构造器大，在某些是否注重性能的情况下，可能成为问题。

总结：如果类的构造器或者静态工厂中具有多个参数，设计这种类时，Builder模式就是种不错的选择，特别是当大多数参数都是可选的时候。与使用传统的重叠构造器相比，使用Builder模式的代码更易于阅读和编写，也比JavaBean更加安全。

**Tip**s：为什么代码易于阅读和编写是个优点，编写代码很大情况是再理解别人写的代码逻辑和意图，甚至是自己写的，所以易于阅读的代码能提高你的编码或者别人的编码效率，推荐大家看《clean code》这本书。