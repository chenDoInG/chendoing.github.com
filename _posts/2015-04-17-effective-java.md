---
layout: post
author: chenDoInG
title: "Effective Java"
description: ""
category: 
tags: []
---
#Compare to使用注意技巧

		￼public int compareTo(That that){
			return this.a - that.a;
		}
		
必须确认最小和最大的可能值域之差小于或者等于INTEGER.MAX_VALUE(2^23-1),否则不要使用这种方法。如果i是一个很大的正数，而j是一个很大的负数，那么i-j将会溢出，并返回一个负值。这样就使得compareTo方法将对某些参数返回错误的结果。而且这样的错误将难于调试，因为这样的方法对于大多数的输入值都能正常工作。