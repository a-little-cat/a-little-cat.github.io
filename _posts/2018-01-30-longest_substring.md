---
tag: [leetcode,easy]
description: 最长子串
---

# Description
> Given a string, find the length of the longest substring without repeating characters.
<!--more-->
# Example
>Given "abcabcbb", the answer is "abc", which the length is 3.

>Given "bbbbb", the answer is "b", with the length of 1.

>Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

# code
共n版
## 自写第一版
晚上失眠构思,按只有26个英文字母设计(无碍).

使用一个子串保存当前滑动到的串,使用一个26元素数组记录每个字母是否被子串包含.

每次向后滑动一位,更新子串和数组,由于数组需要按子串顺序更新,时间复杂度为O(2n)
```cpp
class Solution
{
  public:
    int lengthOfLongestSubstring(string s)
    {
        unordered_map<char, int> map;
        int max_length = 0;
        string substr;
        for (int i = 0; i < s.size(); i++)
        {
            if (map.find(s.at(i)) != map.end())
            {
                substr.append(1, s.at(i));
                for (int j = 0; substr.at(0) != s.at(i); j++)
                {
                    map.erase(substr.at(0)); 
                    substr.assign(substr, 1, substr.size() - 1);
                }
                substr.assign(substr, 1, substr.size() - 1);
                cout << "substr  " << substr << endl;
            }
            else
            {
                substr.append(1, s.at(i));
                map[s.at(i)] = 1;
                cout << "substr  " << substr << endl;
                if (max_length < substr.size())
                    max_length++;
            }
        }
        return max_length;
    }
};
```
## 官方solution
数组中保存的是丢弃重复部分后要跳到的位置,时间复杂度O(n)

以**iamjustalittlecat**进行测试.
```c++
class Solution
{
  public:
    int lengthOfLongestSubstring(string s)
    {
        int n = s.length(), ans = 0;
        int index[26] = {0}; // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++)
        {
            i = max(index[s.at(j) - 'a'], i);
            ans = max(ans, j - i + 1);
            index[s.at(j) - 'a'] = j + 1;
        }
        return ans;
    }
};
```
索引数组
```
 0  0  0  0  0  0  0  0  1  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
 2  0  0  0  0  0  0  0  1  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
 2  0  0  0  0  0  0  0  1  0  0  0  3  0  0  0  0  0  0  0  0  0  0  0  0  0
 2  0  0  0  0  0  0  0  1  4  0  0  3  0  0  0  0  0  0  0  0  0  0  0  0  0
 2  0  0  0  0  0  0  0  1  4  0  0  3  0  0  0  0  0  0  0  5  0  0  0  0  0
 2  0  0  0  0  0  0  0  1  4  0  0  3  0  0  0  0  0  6  0  5  0  0  0  0  0
 2  0  0  0  0  0  0  0  1  4  0  0  3  0  0  0  0  0  6  7  5  0  0  0  0  0
 8  0  0  0  0  0  0  0  1  4  0  0  3  0  0  0  0  0  6  7  5  0  0  0  0  0
 8  0  0  0  0  0  0  0  1  4  0  9  3  0  0  0  0  0  6  7  5  0  0  0  0  0
 8  0  0  0  0  0  0  0 10  4  0  9  3  0  0  0  0  0  6  7  5  0  0  0  0  0
 8  0  0  0  0  0  0  0 10  4  0  9  3  0  0  0  0  0  6 11  5  0  0  0  0  0
 8  0  0  0  0  0  0  0 10  4  0  9  3  0  0  0  0  0  6 12  5  0  0  0  0  0
 8  0  0  0  0  0  0  0 10  4  0 13  3  0  0  0  0  0  6 12  5  0  0  0  0  0
 8  0  0  0 14  0  0  0 10  4  0 13  3  0  0  0  0  0  6 12  5  0  0  0  0  0
 8  0 15  0 14  0  0  0 10  4  0 13  3  0  0  0  0  0  6 12  5  0  0  0  0  0
16  0 15  0 14  0  0  0 10  4  0 13  3  0  0  0  0  0  6 12  5  0  0  0  0  0
16  0 15  0 14  0  0  0 10  4  0 13  3  0  0  0  0  0  6 17  5  0  0  0  0  0
```
当前子串
```
i
ia
iam
iamj
iamju
iamjus
iamjust
mjusta
mjustal
mjustali
alit
t
tl
tle
tlec
tleca
lecat
```
## 神级代码
看不懂 sad
```c++
static const auto ______ = [](){ 
    std::ios::sync_with_stdio(false); 
    cin.tie(nullptr);
    return nullptr; 
}();

class Solution 
{
public:
    int lengthOfLongestSubstring(string s) 
    {
        int flags[256];
        for (int i = 0; i < 256; i++)
            flags[i] = -1;
        
        int longest = 0;
        int i = 0;
        int collision = -1;
        while (s[i] != 0)
        {
            if (flags[s[i]] != -1)
                collision = (collision > flags[s[i]] ? collision : flags[s[i]]);
            flags[s[i]] = i;
            longest = (i - collision > longest ? i - collision : longest);
            i++;
        }
        
        return longest;
    }
};
```