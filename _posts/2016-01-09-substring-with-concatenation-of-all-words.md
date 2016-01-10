---
layout: post
author: chenDoInG
title: "Substring with Concatenation of All Words"
description: ""
category: algorithm,java
tags: [algorithm,java,LeetCode]
---
# Substring with Concatenation of All Words


翻译自[N00tc0d3r](http://n00tc0d3r.blogspot.sg/2013/06/substring-with-concatenation-of-all.html)（请自行翻墙）

学习下技术文章到底应该怎么写，对比于百度搜索出来的各种解题思路，我只能呵呵！

##题目：
		Substring with Concatenation of All Words

		You are given a string, S, and a list of words, L, that are all of the same length. Find all starting indices of substring(s) in S that is a concatenation of each word in L exactly once and without any intervening characters.

		For example, given:
		S: "barfoothefoobarman"
		L: ["foo", "bar"]

		You should return the indices: [0,9].
		(order does not matter).
		
##解题思路：

基本想法是遍历字符串S的每个字符，判断当前字符是否是词组L中某个单词的组成部分

###怎么判断当前结点是某个单词的组成部分？

  因为我们知道词组L中的所有单词都是固定长度的，所以从字符串S的结点i开始，我们将字符串S截取成m段字符串子串（后面都直接叫***子串***），这里m是词组L中单词的总数，然后我们就可以判断每个子串是否是词组L中的单词和是否重复

###怎么判断每个子串是集合L中的单词？

  最简单的方法是拿子串和词组L中的每个单词比较。但是对于每个子串，时间复杂度是O(m)，当我们有特别多的子串或者词组中单词总数特别多时，时间开销代价特别高。实际上的时间复杂度是O(k*m) = O(m)，这里k是单词的长度。这意味着如果单词的长度不是特别短的话，时间开销的代价会更高。
  
  **我们可以将词组L的单词存储在一个hashTable里，而hashTable搜索的时间复杂度是O(1)**
  
###什么样的hashTable？怎么判断重复子串?如果词组L本身包含重复单词？

我们创建了一个包含词组L中所有单词的hashTable。每当有子串匹配上单词时，我们将该单词从这个hashTable移除。我们使用HashMap<String,Integer>来替代HashMap<String,Boolean>以便于统计词组L中每个单词的出现次数。因此每当有子串匹配上单词时，我们将HashMap中该词组的次数减一知道次数为零时，我们将该单词从HashMap中移除。

现在我们有了一个大体上的思路：

* 创建一个包含词组L中所有单词和出现次数的hashTable
* 对于字符串S中的每个字符，判断是否是某个单词的组成部分。如果满足以下的所有条件，将字符的位置记录到结果list里。


	* 后续所有长度为k的子串都是词组L中的单词。
	* 词组L中的每个单词出现仅出现一次。
	
###这个算法的复杂度是什么？

判断每个字符的时间复杂度是O(k\*M)=O(m)，我们需要判断(n-k\*m)次，所以总的时间复杂度是O((n-k\*m)\*m)。因为我们创建了一个hashTable，所以空间复杂度是取决于词组L的单词总数，也就是O(k\*m) = O(m).

	public ArrayList<Integer> findSubstring(String S, String[] L) {  
   		ArrayList<Integer> indices = new ArrayList<Integer>();  
   		if (L.length == 0) return indices;  
   
   		int total = L.length, wordLen = L[0].length();  
   
   		// store the words and frequencies in a hash table  
   		HashMap<String, Integer> words = new HashMap<String, Integer>();  
   		for (String s : L) {  
     		if (words.containsKey(s)) {  
       			words.put(s, words.get(s)+1);  
     		} else {  
       			words.put(s, 1);  
     		}  
   		}  
   
   		// find concatenations  
   		for (int i=0; i <= S.length() - total*wordLen; ++i) {  
    		 // check if it is a concatenation   
     		HashMap<String, Integer> target = new HashMap<String, Integer>(words);  
     		for (int j = i; j <= S.length() - wordLen && !target.isEmpty(); j+=wordLen) {  
       			String sub = S.substring(j, j+wordLen);  
       			if (!target.containsKey(sub)) break;  
       			if (target.get(sub) > 1) {  // reduce the frequency
         			target.put(sub, target.get(sub)-1);  
       			} else {  // remove the word if only one left
         			target.remove(sub);  
       			}
     		}  
     		if (target.isEmpty()) {  
       			indices.add(i);  
     		}  
   		}  
   
   	return indices;  
 	}  
 	


