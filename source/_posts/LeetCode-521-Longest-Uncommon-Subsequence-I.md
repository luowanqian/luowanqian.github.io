---
title: LeetCode 521 - Longest Uncommon Subsequence I
urlname: leetcode_521
date: 2018-04-15 22:26:45
tags:
 - LeetCode
 - 算法
---

## 问题描述

Given a group of two strings, you need to find the longest uncommon subsequence of this group of two strings. The longest uncommon subsequence is defined as the longest subsequence of one of these strings and this subsequence should not be __any__ subsequence of the other strings.

A __subsequence__ is a sequence that can be derived from one sequence by deleting some characters without changing the order of the remaining elements. Trivially, any string is a subsequence of itself and an empty string is a subsequence of any string.

The input will be two strings, and the output needs to be the length of the longest uncommon subsequence. If the longest uncommon subsequence doesn't exist, return -1.

__Example 1:__

```
Input: "aba", "cdc"
Output: 3
Explanation: The longest uncommon subsequence is "aba" (or "cdc"),
because "aba" is a subsequence of "aba",
but not a subsequence of any other strings in the group of two strings.
```

__Note:__

1. Both strings' lengths will not exceed 100.
2. Only letters from a ~ z will appear in input strings.

__Related Topics:__ String

原问题：[521. Longest Uncommon Subsequence I](https://leetcode.com/problems/longest-uncommon-subsequence-i/description/)

中文翻译版：[521. 最长特殊序列 Ⅰ](https://leetcode-cn.com/problems/longest-uncommon-subsequence-i/description/)

## 解决方案

这道题粗看以为是一道很难的题目，但是解决该问题只需要考虑三种情况：

1. $a = b$
  If both the strings are identical, it is obvious that no subsequence will be uncommon. Hence, return -1.
2. $length(a) = length(b)$ and $a \ne b$
  Example: $abc$ and $abd$. In this case we can consider any string i.e. $abc$ or $abd$ as a required subsequence, as out of these two strings one string will never be a subsequence of other string. Hence, return $length(a)$ or $length(b)$.
3. $length(a) \ne length(b)$
  Example: $abcd$ and $abc$. In this case we can consider bigger string as a required subsequence because bigger string can't be a subsequence of smaller string. Hence, return $\max{(length(a), length(b))}$.

贴上实现代码

```
#include <iostream>
using namespace std;

class Solution {
public:
    int findLUSlength(string a, string b)
    {
        int i = 0, j = 0;

        if (a.size() == b.size()) {
            while (i < a.size() && j < a.size() && a[i] == b[j]) {
                i++;
                j++;
            }
            if (i < a.size() || j < b.size())
                return a.size();
            else
                return -1;
        } else {
            return a.size() > b.size() ? a.size() : b.size();
        }
    }
};

int main()
{
    Solution solu;
    string str1 = "abcd", str2 = "abc";

    cout << solu.findLUSlength(str1, str2) << endl;

    return 0;
}
```
