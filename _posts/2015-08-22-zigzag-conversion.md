---
layout: post
auth: chenDoInG
title: "ZigZag Conversion"
description: ""
category: [Algorithms,Leetcode,java]
tags: []
---
# ZigZag Conversion 

## The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

		P   A   H   N
		A P L S I I G
		Y   I   R
## And then read line by line: "PAHNAPLSIIGYIR"

### 思路：每个转弯长度离下一个转弯距离是固定的，除了首行和末行是每个转弯是一个字符外，其他行都是两个字符

		P   | A   | H   | N     
		A P | L S | I I | G 
		Y   | I   | R   |

也就是说P->A->H->N,A->L->I->G,Y->I->R 他们的距离∆T = 2*rows-2

对于非首行和末行有两个字符的，如A->P,L->S,I->I   ∆C = 2*(rows-rowIndex)

	用数组下表来表示他们的关系：
		0   | 4   | 8
		1 3 | 5 7 | 9
		2   | 6   | 10
    计算时取数组位置就是0->4->8->1->3->5->7->9->2->6->10
    
**Tips** 特殊情况：row为1时，肯定是返回原字符串，也就是∆T=1

具体java代码如下：

		public String convert(String s, final int numRows) {
        	StringBuilder sb = new StringBuilder();
        	int deltaT = 2 * (numRows - 1);
        	if(deltaT == 0)
            	deltaT = 1;
        	for(int rowIndex = 1, counter = 0; rowIndex <=numRows; rowIndex++, counter++)
        	{
            	int deltaC = 2 * (numRows-rowIndex);
            	int index = counter;
            	while (index < s.length()){
                	sb.append(s.charAt(index));
                	if(rowIndex != numRows && rowIndex != 1 && (index + deltaC) < s.length())
                	{
                    	sb.append(s.charAt(index + deltaC));
                	}
                	index += deltaT;
            	}
       	 	}
        	return sb.toString();
    	}
		


