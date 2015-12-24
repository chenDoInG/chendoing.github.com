---
layout: post
author: chenDoInG
title: "Learning at Effect Java 43"
description: ""
category: Effect Java
tags: [Effect Java]
---
## 返回零长度的数组或者集合，而不是null

常态：当数组或者集合为空时，返回一个null
	
	private final List<Cheese> cheesesInStock = …
	
	public Cheese[] getCheeses(){
		if (CheesesInStock.size() == 0 )
			return null;
	}
	
**这样做的缺点是：**任何调用getCheeses()方法的程序员都必须判断cheeses是不是null，假如某个程序员粗心大意没有判断就会抛出空指针异常（这个好像也是常态，至今我只见过的少部分程序员会判断大部分的输入值）

	调用者代码：
		Cheese[] cheeses = shop.getCheeses();
		if( cheeses != null && Arrays.asList(cheeses).contains(Cheese.STILTON))
			…
		

### 改进方案：返回一个空数组或者空集合
	private final List<Cheese> cheesesInStock = …
	
	private static final Cheese[] EMPTY_CHEESES = new Cheese[0];
	public Cheese[] getCheeses(){
		return CheesesInStock.toArray(EMPTY_CHEESES);
	}
	
	或者
	
	public List<Cheese> getCheeses(){
		if(cheesesInStock.isEmpty)
			return Collections.emptyList();
		else
			return new ArrayList<Cheese>(cheesesInStock);
	}
	
	调用者现在的代码：
		Cheese[] cheeses = shop.getCheeses();
		if(Arrays.asList(cheeses).contains(Cheese.STILTON))
			…
	不用再做额外的判断了

Tips:没看这篇文章之前，我写的方法基本上都是返回null，然后调用的地方全都加上判断xxx != null，看完这个以后发现原来我们写的代码可以让别人用的更舒服，只要我们多做一点点^_^

**心得：**好代码不是写的算法思想多好，只要优化一点点，让别人做的更少一些，就可以让代码思路更清晰，阅读起来迅速理解代码意图。不会给别人带来额外的负担，这样就是好代码

