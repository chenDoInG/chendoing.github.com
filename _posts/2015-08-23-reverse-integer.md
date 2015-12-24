---
layout: post
author: chenDoInG
title: "Reverse Integer"
description: ""
category: [Algorithms,Leetcode,java]
tags: [Algorithms,LeetCode,java]
---
## Reserve Integer

Reverse digits of an integer.

Example1: x = 123, return 321

Example2: x = -123, return -321

### 思路：利用integer的整除和取余数，321/10 = 32, 321%10 = 1,那么1就是我们需要的翻转数最高位，主要问题是翻转之后是否会溢出，因为integer的范围是-2^31到2^31-1

**Tips** 这个翻转特别简单，为啥要写出来呢？因为看到这个问题，我第一想法是用堆栈，而用堆栈的话，需要先将integer拆分，并且还要记录是否负数等，没有很好的利用数的特点。警戒自己以后遇到问题：**根据实际情况，选择最优处理方式**

java代码如下：
	
	public int reverse(int x) {
        int result = 0;
        while (x!=0){
            if(result>Integer.MAX_VALUE/10||result<Integer.MIN_VALUE/10)
                return  0;
            result = result*10 + x%10;
            x= x /10;
        }
        return  result;
    }
