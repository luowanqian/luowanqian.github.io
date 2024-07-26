---
title: LeetCode 383 - Ransom Note
urlname: leetcode_383
date: 2018-04-21 09:57:31
tags:
 - LeetCode
 - 算法
---

## 问题描述

Given an arbitrary ransom note string and another string containing letters from all the magazines, write a function that will return true if the ransom note can be constructed from the magazines ; otherwise, it will return false.

Each letter in the magazine string can only be used once in your ransom note.

__Note:__

You may assume that both strings contain only lowercase letters.

```
canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true
```

__Related Topics:__ String

原问题：[383. Ransom Note](https://leetcode.com/problems/ransom-note/description/)

中文翻译版：[383. 赎金信](https://leetcode-cn.com/problems/ransom-note/description/)

## 解决方案

这道题要求字符串 ransom note 中的字符都能从字符串 magazine 中找到，而且 ransom note 中的字符的重复次数要小于等于 magazine 相对应字符的重复次数。基于上面的要求，有个很简单的解决方案，对字符串 ransom note 和字符串 magazine 分别构造一个大小为 26 的数组，存储 26 个字母在字符串中出现的次数，然后遍历数组，判断 ransom note 中的字符的重复次数是否小于等于 magazine 相对应字符的重复次数。

```
#include <iostream>
#include <cstring>
#include <string>
using namespace std;

class Solution {
public:
    bool canConstruct(string ransomNote, string magazine)
    {
        if (ransomNote.size() > magazine.size())
            return false;

        int rmap[26], mmap[26];

        memset(rmap, 0, sizeof(rmap));
        memset(mmap, 0, sizeof(mmap));

        int i = 0, j = 0;
        while (i < ransomNote.size() || j < magazine.size()) {
            if (i < ransomNote.size())
                rmap[ransomNote[i++] - 'a'] += 1;
            if (j < magazine.size())
                mmap[magazine[j++] - 'a'] += 1;
        }

        for (i=0; i<26; i++)
            if (rmap[i] > mmap[i])
                return false;

        return true;
    }
};

int main()
{
    Solution solu;

    string r1 = "ab";
    string m1 = "aba";
    cout << r1 << " " << m1 << ": " << solu.canConstruct(r1, m1);

    return 0;
}
```
