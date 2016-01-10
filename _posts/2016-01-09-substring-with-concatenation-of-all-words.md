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
		
		举例：给定一个字符串S，和一个包含m个单词的词组L，每个单词长度为k，如果字符串S里包含由词组L中所有单词组成的字符串子串，返回该子串的起始位置
		
##解题思路：

基本想法是遍历字符串S的每个字符，判断当前字符是否是词组L中某个单词的开头

###怎么判断当前字符是某个单词的开头？

  因为我们知道词组L中的所有单词都是固定长度的，所以从字符串S的结点i开始，我们将字符串S截取成m段字符串子串（后面都直接叫***子串***），这里m是词组L中单词的总数，然后我们就可以判断每个子串是否是词组L中的单词和是否重复

###怎么判断每个子串是集合L中的单词？

  最简单的方法是拿子串和词组L中的每个单词比较。但是对于每个子串，时间复杂度是O(m)，当我们有特别多的子串或者词组中单词总数特别多时，时间开销代价特别高。实际上的时间复杂度是O(k*m) = O(m)，这里k是单词的长度。这意味着如果单词的长度不是特别短的话，时间开销的代价会更高。
  
  **我们可以将词组L的单词存储在一个hashTable里，而hashTable搜索的时间复杂度是O(1)**
  
###什么样的hashTable？怎么判断重复子串?如果词组L本身包含重复单词？

我们创建了一个包含词组L中所有单词的hashTable。每当有子串匹配上单词时，我们将该单词从这个hashTable移除。我们使用HashMap<String,Integer>来替代HashMap<String,Boolean>以便于统计词组L中每个单词的出现次数。因此每当有子串匹配上单词时，我们将HashMap中该词组的次数减一直到次数为零时，我们将该单词从HashMap中移除。

现在我们有了一个大体上的思路：

* 创建一个包含词组L中所有单词和出现次数的hashTable
* 对于字符串S中的每个字符，判断是否是某个单词的组成部分。如果满足以下的所有条件，将字符的位置记录到结果list里。


	* 后续所有长度为k的子串都是词组L中的单词。
	* 词组L中的每个单词仅出现一次。
	
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
 	
观察上面的算法，我们发现对于每个子串都有被重复检查。我们有必要重复检查每个字符后的所有子串么？答案是“没必要”。

我们可以使用和[KMP字符串匹配算法](http://en.wikipedia.org/wiki/Knuth–Morris–Pratt_algorithm)相似的的策略：

**提前计算下一个可能是子串开头的地方，而不是从头开始计算。**

回顾题目我们在查找一个由词组L中所有单词组成的字符串并且单词的长度是固定的。为了覆盖所有的可能性，我们可以从字符串S的任意字符S[0,1,...,k-1]开始搜索所有的结果。也就是我们需要检查0,k,2k,...,n-1;然后1,k+1,2k+1,...,n-1;直到k-1,2k-1,3k-1,...,n-1。

从字符串S的第一个字符(begin = 0)，截取一个长度为k的字符串子串，

* 如果该子串不是词组L中的单词，搜索下一个子串(begin + = k);
* 如果该子串是词组L中的单词但是已经被搜索过了(比如，重复的单词),向后移动begin直到前一个单词从当前的计算窗口移除了
* 否则，将该子串加入当前集合。如果我们计算完所有的单词(例如，得到一个正确的结果),向后移动begin检查下一个字符串子串

###现在这个算法的复杂度是什么？

我们检查每个字符串最多两次，一次将字符串加到集合中，另外一次将字符串从集合中移除。因此时间复杂度是O(n)，这里n是字符串的长度。检查每个字符串是否是词组L中的单词，复杂度O(k)=O(1)。所以实际上的时间复杂度是O(n*k) = O(n)。

	private void addWord(String w, HashMap<String, Integer> words) {  
   		if (words.containsKey(w)) {  
     		words.put(w, words.get(w)+1);  
   		} else {  
     		words.put(w, 1);  
   		}  
 	}  
   
 	private void removeWord(String w, HashMap<String, Integer> words) {  
   		if (!words.containsKey(w)) return;  
   		if (words.get(w) > 1) {  
     		words.put(w, words.get(w)-1);  
   		} else {  
     		words.remove(w);  
   		}  
 	}  
   
 	private int slideWindow(String S, int begin, int wordLen, HashMap<String, Integer> words) {  
   		String old = S.substring(begin, begin+wordLen);  
   		addWord(old, words);  
   		return begin+wordLen;  
 	}  
   
 	public ArrayList<Integer> findSubstring(String S, String[] L) {  
   		ArrayList<Integer> indices = new ArrayList<Integer>();  
   		if (L.length == 0) return indices;  
   
   		int total = L.length, wordLen = L[0].length();  
   
   		// store the words and frequencies in a hash table  
   		HashMap<String, Integer> expectWords = new HashMap<String, Integer>();  
   		for (String w : L) {  
     		addWord(w, expectWords);  
   		}  
   
   		// find concatenations  
   		for (int i=0; i < wordLen; ++i) {  
     		// check if there are any concatenations  
     		int count = 0;  
     		HashMap<String, Integer> collectWords = new HashMap<String, Integer>(expectWords);  
     		for (int j = i, begin = i; j <= S.length() - (total-count)*wordLen && begin <= S.length() - total*wordLen;) {  
      			String sub = S.substring(j, j+wordLen);  
       			if (!expectWords.containsKey(sub)) { // if not an expect word, reset  
         			begin = j + wordLen;  
         			j = begin;  
         			count = 0;  
         		collectWords.putAll(expectWords);  
       			} else if (!collectWords.containsKey(sub)) { // if duplicate, forward begin by 1  
         			begin = slideWindow(S, begin, wordLen, collectWords);  
       			} else {  
         			removeWord(sub, collectWords);  
         			j += wordLen;  
         			++count;  
         			if (collectWords.isEmpty()) {  
           				indices.add(begin);  
           				begin = slideWindow(S, begin, wordLen, collectWords);  
           				--count;  
         			}  
       			}  
     		}  
   		}  
   
   		return indices;  
 		}  
