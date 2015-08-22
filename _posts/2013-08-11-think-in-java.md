---
layout: post
author: chenDoInG
title: "Think in Java——Arrays.asList"
description: ""
category: [java]
tags: [java]
---
#Arrays.asList

    List<Integer> list = Arrays.asList(1,2,3,4,5);
    list.set(1,99);
    //list.add(6);
Array.asList可以作为list输出，但是他的底层表示还是Array，是不能调整大小的。调用add()或delete()，运行时 都会获得_"Unsupported Operation"_错误