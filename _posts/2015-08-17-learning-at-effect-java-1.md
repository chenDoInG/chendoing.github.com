---
layout: post
author: chenDoInG
title: "Learning At Effect Java 1"
description: ""
category: [java]
tags: [Effect Java]
---
# 考虑用静态方法替代构造器

## 优点:

1.他们有名称。使用具有适当名称的静态方法返回对象，代码更易于阅读

        new BigInteger(int,int,Random)->BigInteger.probablePrime(int,int,Random)
        表明返回的BigInteger可能为素数

2.不必再每次调用他们的时候都创建一个新的对象

        这使得不可变类使用预先构建好的实例，或者将构建好的实例缓存起来，重复利用，避免创建不必要的重复对象
       
3.可以返回原返回类型的任何子类型的对象

     public interface Provider {
          Service newService();
     }
   
     public class Services {

          // 不可实例化
          private Services() {
          }

          private static final Map<String, Provider> providers  = new ConcurrentHashMap<String, Provider>();
          private static final String                DEFAULT_PROVIDER_NAME = "<def>";

          public static void registerDefaultProvider(Provider p) {
              registerDefaultProvider(DEFAULT_PROVIDER_NAME, p);
          }

          public static void registerDefaultProvider(String name, Provider p) {
              providers.put(name, p);
          }

          public static Service newInstance() {
              return newInstance(DEFAULT_PROVIDER_NAME);
          }

          public static Service newInstance(String name) {
              Provider p = providers.get(name);
              if (p == null) {
                  throw new IllegalArgumentException(
                          "No providers registered with name : " + name);
              }
              return p.newService();
          }
     }

4.创建参数化实例时，使代码更加简洁

## 缺点：

1.不含有public或者protected的构造器，就不能被子类化。

        举个例子，使用cglib动态代理该类时应该就会报错
        但是同样的鼓励我们“多用组合，少用继承”
       
2.与其他的静态方法实际上没有任何区别

        查询javadoc的时候，如何实例化该类变得非常困难
       
##静态方法初始化类和构造器都有各自的用处，我们需要理解他们各自的长处，因地制宜。
         