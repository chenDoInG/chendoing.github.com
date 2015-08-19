---
layout: post
title: "Longest Palindromic Substring"
description: ""
category: [Algorithms,LeetCode,java]
tags: [Algorithms,LeetCode,java]
---
{% include JB/setup %}

# Given a string S, find the longest palindromic substring in S.

**回文特点：**以一个字符为中心两边字符全相等或者以一个空字符为中心两边字符全相等

**思路一：** 遍历每一个字符，以该字符为中心判断是否构成回文字符串。

tips：在每个字符的两边都插入一个特殊的符号，将所有可能的奇数/偶数长度的回文子串都转换成了奇数长度。比如 abba 变成 #a#b#b#a#， aba变成 #a#b#a#。原来可能需要两个判断相近的两位字符是否相等和该字符两边的字符是否相等，现在只需要一个判断该字符两边的字符是否相等，以空间换取时间。

		public String logestPalindrome(String s){
        	if(s ==null)
            	return null;
        	int length = 2*s.length()+1;
        	char[] filledStr = fillStringBySign(s.toCharArray());
        	int point4LongestPalindrome = -1;
        	int maxRadius = -1;
        	for (int i = 0; i < length; i++) {
            	int radius = 1;
            	while (i-radius>=0&&i+radius<length&&filledStr[i-radius]==filledStr[i+radius]) {
                	radius++;
            	}
            	if(radius>maxRadius){
                	maxRadius = radius;
                	point4LongestPalindrome = i;
            	}
        	}
        	char[] result = new char[maxRadius-1];
        	int j = 0;
        	for (int i = point4LongestPalindrome - maxRadius + 1; i < point4LongestPalindrome + maxRadius; i++) {
            	if (filledStr[i] != '#') {
                	result[j] = filledStr[i];
                	j++;
            	}
        	}
        	return String.valueOf(result);
    	}

		private char[] fillStringBySign(char[] s) {
        	char[] str = new char[2 * s.length + 1];
        	str[0] = '#';
        	for (int i = 0; i < s.length; i++) {
            	str[2 * i + 1] = s[i];
            	str[2 * i + 2] = '#';
        	}
        	return str;
    	}
    

**思路二：** Manacher's Algorithms

解题思路：

1. 将字符串填充（见思路一里的tips）
2. 增加一个数组记录radius[]每一个字符的回文半径
3. 遍历所有字符找到最大回文半径后返回

**Tips：该算法的核心在于怎么计算每个字符的回文半径**

**想法重点：** 假设现在已经计算出1到i个字符的回文半径radius[0,...,i-1]，求(i+1)到n个字符的回文半径

演示：假设1-i个字符里的最大回文字符的位置为id，那么最大回文半径为radius[id],那么最大的回文边界是id+radius[id]-1

		源字符串：    a   b   b   a   
		填充字符串: # a # b # b # a #
		数组下标：  0 1 2 3 4 5 6 7 8
		回文半径：  1 2 1 2 5  
	    现在的最大回文半径是radius[4]=5,那么我们计算过的当前最大回文边界是id+radius-1 = 8，
	    此时我们要计算位置5的回文半径radius[5]，
	    
	    因为位置5小于最大回文边界8，根据回文特点左右对称，那么5的对称位为3，3的回文半径是2，所以位置5的回文半径最小值就是2，也就是radius[5]=2。
	    
	    然后我们开始计算遍历位置5的可能回文地址s[5-2]是否等于s[5+2],相等的话回文半径+1，不等的话5的最大回文半径就是2了。
	    
	    这里就跳过计算5和5+2之前的字符是否相等了，因为根据回文特点我们已经计算过了（这个是决定算法复杂度是o(N)关键）
	    
![图片一]({{ site.url }}/_images/show3.jpeg)

1. 算法思想一：rad[i] - k < rad[i - k]
如图1，rad[i - k]的范围为青色。因为黑色的部分是回文的,且青色的部分超过了黑色的部分，所以rad[i + k]肯定至少为rad[i]-k,即橙色的部分。那橙色以外的部分就不是了吗?这是肯定的，因为如果橙色以外的部分也是回文的，那么根据青色和红色部分的关系，可以证明黑色部分再往外延伸一点也是一个回文子串,这肯定是不可能的，因此rad[i + k] = rad[i] - k。

![图片二]({{ site.url }}/_images/show2.jpeg)

2. 算法思想二：rad[i] - k > rad[i - k]
如图2，rad[i-k]的范围为青色，因为黑色的部分是回文的，且青色的部分在黑色的部分里面，根据定义，很容易得出:rad[i + k] = rad[i - k]。根据上面两种情况，可以得出结论:当rad[i] - k != rad[i - k]的时候,rad[i + k] = min(rad[i] - k, rad[i - k])。

![图片三]({{ site.url }}/_images/show1.jpeg)

3. 算法思想三：rad[i] - k = rad[i - k]
如图，通过和第一种情况对比之后会发现，因为青色的部分没有超出黑色的部分，所以即使橙色的部分全等，也无法像第一种情况一样引出矛盾，因此橙色的部分是有可能全等的。但是，根据已知的信息，我们不知道橙色的部分是多长，因此就需要再去尝试和判断了。

**Tips:** 算法复杂度为什么是o(N)，i之前的回文串最大半径已经知道了，计算i时，如果i在最大回文串范围内，那么找到i的半径就等于j的半径和id-i的最小值，跳过已经计算的字符（根据回文的特点）

java代码：

		public String longestPalindrome(String s) {
        	if (s == null)
            	return null;
        	int n = s.length();
        	int len = 2 * n + 1;
        	int[] radius4EachCharacter = new int[len];
        	char[] filledStr = fillStringBySign(s.toCharArray());
        	int point4LongestPalindrome = 0, end4LongestPalindrome = -1, maxRadius = -1;
        	for (int i = 0; i < len; i++) {
            	if (end4LongestPalindrome > i) {
                	radius4EachCharacter[i] = Math.min(radius4EachCharacter[2 * point4LongestPalindrome - i], end4LongestPalindrome - i);
            	} else {
                	radius4EachCharacter[i] = 1;
            	}
            	while (i - radius4EachCharacter[i] >= 0 && i + radius4EachCharacter[i] < len && filledStr[i - radius4EachCharacter[i]] == filledStr[i + radius4EachCharacter[i]]) {
                	radius4EachCharacter[i]++;
            	}
            	if (radius4EachCharacter[i] > maxRadius) {
                	end4LongestPalindrome = i + radius4EachCharacter[i] - 1;
                	point4LongestPalindrome = i;
                	maxRadius = radius4EachCharacter[i];
            	}
        	}
        	char[] result = new char[maxRadius-1];
        	int j = 0;
        	for (int i = point4LongestPalindrome - maxRadius + 1; i < point4LongestPalindrome + maxRadius; i++) {
            	if (filledStr[i] != '#') {
                	result[j] = filledStr[i];
                	j++;
            	}
        	}
        	return String.valueOf(result);
    	}

    	private char[] fillStringBySign(char[] s) {
        	char[] str = new char[2 * s.length + 1];
        	str[0] = '#';
        	for (int i = 0; i < s.length; i++) {
            	str[2 * i + 1] = s[i];
            	str[2 * i + 2] = '#';
        	}
        	return str;
   	 	}

参考资料：

[寻找字符串中最长回文——Manacher算法及其Java实现（POJ 3974）](http://blog.sina.com.cn/s/blog_3fe961ae0101iwc2.html)

[Manacher's ALGORITHM: O(n)时间求字符串的最长回文子串](http://www.felix021.com/blog/read.php?2040)
