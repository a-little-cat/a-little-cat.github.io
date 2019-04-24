---
tag: [leetcode]
---

# Description
>Input: "babad"
>
>Output: "bab"
>
>Note: "aba" is also a valid answer.
<!--more-->
# Example
>Input: "babad"
>
>Output: "bab"
>
>Note: "aba" is also a valid answer.

# 分析
如果直接使用暴力循环,是O(n^2)的复杂度.

有大神开发出了**manacher**算法,算法核心部分还算好理解,但是开始的转换不能想通,仅记录如下.

输入:
```c++
    s = "12212321";
```
转换,便于处理边界情况和奇偶情况 不明觉厉:
```c++
    s = "$#1#2#2#1#2#3#2#1#";
```
建立数组p,p[i]表示以i为中心的最长回文子串向左/右扩张的长度,包括s[i],最小为1.
    S  #  1  #  2  #  2  #  1  #  2  #  3  #  2  #  1  #
    P  1  2  1  2  5  2  1  4  1  2  1  6  1  2  1  2  1
    (p.s. 可以看出，P[i]-1正好是原字符串中回文串的总长度）
接下来是以O(n)计算p[i]

记id为已知右边界最长的回文串的中心,起始情况下id=0,

mx为对应的右边界,起始情况下mx=0.

算法的核心如下:
```c++
    p[i] = mx > i ? min(p[2 * id - i], mx - i) : 1;
```
可以拆分如下:

(以下代码中i的移动范围是从id向后移)
```c++
    j = 2 * id - i;//j是i关于id的对称点
    if (i < mx)
        p[i] = min(p[j] ,mx - i)//此处实际含义是大于等于
    else
        p[i] = 1;//此处实际含义是大于等于
    while (s[i + p[i]] == s[i - p[i]]) 
        p[i]++;
```
# 完整代码
```c++
#include <vector>
#include <iostream>
#include <string>
#include <algorithm>

using namespace std;
string Manacher(string s);
int main()
{
    string s1 = "12212";
    cout << Manacher(s1) << endl;
    string s2 = "122122";
    cout << Manacher(s2) << endl;
    string s = "waabwswfd";
    cout << Manacher(s) << endl;
}
string Manacher(string s)
{
    // Insert '#'
    string t = "$#";
    for (int i = 0; i < int(s.size()); ++i)
    {
        t += s[i];
        t += "#";
    }
    // Process t
    vector<int> p(t.size(), 0);
    int mx = 0, id = 0, resLen = 0, resCenter = 0;
    for (int i = 1; i < int(t.size()); ++i)
    {
        p[i] = mx > i ? min(p[2 * id - i], mx - i) : 1;
        while (t[i + p[i]] == t[i - p[i]])
            ++p[i];
        if (mx < i + p[i])
        {
            mx = i + p[i];
            id = i;
        }
        if (resLen < p[i])
        {
            resLen = p[i];
            resCenter = i;
        }
    }
    return s.substr((resCenter - resLen) / 2, resLen - 1);
}
```