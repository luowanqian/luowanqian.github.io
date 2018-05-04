---
title: LeetCode 13 - Roman to Integer
urlname: leetcode_13
tags:
  - LeetCode
  - 算法
date: 2018-04-21 10:23:51
---

## 问题描述

Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

```
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, two is written as `II` in Roman numeral, just two one's added together. Twelve is written as, `XII`, which is simply `X` + `II`. The number twenty seven is written as `XXVII`, which is `XX` + `V` + `II`.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as `IV`. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as `IX`. There are six instances where subtraction is used:

* `I` can be placed before `V` (5) and `X` (10) to make 4 and 9.
* `X` can be placed before `L` (50) and `C` (100) to make 40 and 90.
* `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.

__Example 1:__

```
Input: "III"
Output: 3
```

__Example 2:__

```
Input: "IV"
Output: 4
```

__Example 3:__

```
Input: "IX"
Output: 9
```

__Example 4:__

```
Input: "LVIII"
Output: 58
Explanation: C = 100, L = 50, XXX = 30 and III = 3.
```

__Example 5:__

```
Input: "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

__Related Topics:__ Math, String

原问题: [13. Roman to Integer](https://leetcode.com/problems/roman-to-integer/description/)

中文翻译版: [13. 罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/description/)

## 解决方案

题目难度不大，根据题目描述可以写出代码

```
#include <iostream>
#include <string>
using namespace std;

class Solution {
public:
    int romanToInt(string s)
    {
        int num = 0;
        int j;
        char ch;

        for (int i=0; i<s.size(); i++) {
            ch = s[i];
            j = i + 1;
            switch (ch) {
                case 'I':   // value 1
                    if (j < s.size() && (s[j] == 'V' || s[j] == 'X'))
                        num -= 1;
                    else
                        num += 1;
                    break;
                case 'V':   // value 5
                    num += 5;
                    break;
                case 'X':   // value 10
                    if (j < s.size() && (s[j] == 'L' || s[j] == 'C'))
                        num -= 10;
                    else
                        num += 10;
                    break;
                case 'L':   // value 50
                    num += 50;
                    break;
                case 'C':   // value 100
                    if (j < s.size() && (s[j] == 'D' || s[j] == 'M'))
                        num -= 100;
                    else
                        num += 100;
                    break;
                case 'D':   // value 500
                    num += 500;
                    break;
                case 'M':   // value 1000
                    num += 1000;
                    break;
            }
        }

        return num;
    }
};

int main()
{
    Solution solu;
    string str1 = "MCMXCIV";

    cout << str1 << ": " << solu.romanToInt(str1) << endl;

    return 0;
}
```
