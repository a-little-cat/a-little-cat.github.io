---
tags: [leetcode,哈希表]
---

# Description
>Given a roman numeral, convert it to an integer.
>Input is guaranteed to be within the range from 1 to 3999.
<!--more-->

# Example
* input: "DCXXI"
* 621

# 分析
难度不在时间复杂度,在规则提取.

我自己写的一版,首先将不同位数的罗马数字提取,然后进行计算,很繁琐.

vera_qing给出了这份代码,在罗马数字规则的梳理上让我看到了自己的卑微..
>BASIC Character
I
V
X
L
C
D
M
>
>Arabic numerals
1
5
10
50
100
500
1000
>
>the rule is complex,but in this condition. It can be tell as:
>
>we start from the end of String.
>
>if the char before the current char we are reading.
>
>plus it
>
>if not
>
>reduce it。
>

```c++
int romanToInt(string s) {
		unordered_map<char, int> charToInt = { { 'I', 1 }, { 'V', 5 }, { 'X', 10 }, { 'L', 50 }, { 'C', 100 }, { 'D', 500 }, { 'M', 1000 } };
		int result = 0;
		int index = s.length() - 1;
		int preInt = 0;
		while (index >= 0)
		{
			char ch = s[index];
			int curInt = charToInt[ch];
			if (curInt >= preInt)
				result += curInt;
			else
				result -= curInt;
		
			preInt = curInt;
			index--;
		}
			
		return result;
	}
```