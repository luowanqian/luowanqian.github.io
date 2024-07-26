---
title: LeetCode 387 - First Unique Character in a String
urlname: leetcode_387
tags:
  - LeetCode
  - 算法
date: 2018-04-21 21:23:56
---

## 问题描述

Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.

__Examples:__

```
s = "leetcode"
return 0.

s = "loveleetcode",
return 2.
```

__Note:__ You may assume the string contain only lowercase letters.

__Related Topics:__ Hash Table, String

原问题: [387. First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/description/)

中文翻译版: [387. 字符串中的第一个唯一字符](https://leetcode-cn.com/problems/first-unique-character-in-a-string/description/)

## 解决方案

本题需要找到第一个不重复出现的字符的位置，因此在遍历字符串时需要保存字符的位置。由于字符串中只有小写字母，因此可以创建一个大小为 26 的数组，存储 26 个字母在字符串中出现位置，由于还需要判断该字母是否重复出现，这时可以用一个特殊值表示该字母在字符串中重复出现。数组元素的值分三种情况：

* -1 表示该字母已经重复出现
* 0 表示该字母未在字符串中出现
* 正整数表示该字母第一次在字符串中出现的位置 (位置从 1 开始)

根据输入的字符串构造出这个数组后，我们可以很快找到第一个不重复出现的字符的位置 (即数组元素中值最小的那个，不包括 -1 和 0)。实现代码如下：

```
#include <iostream>
#include <string>
#include <cstring>
using namespace std;

class Solution {
public:
    int firstUniqChar(string s)
    {
        if (s.size() == 0)
            return -1;
        else if (s.size() == 1)
            return 0;

        /* letters[i]
         * -1 means letter repeat
         * 0 means letter not appear
         * > 0 means position of letter in string (index starts from 1)
         */
        int letters[26];
        int pos;

        memset(letters, 0, sizeof(letters));
        for (int i=0; i<s.size(); i++) {
            pos = s[i] - 'a';
            if (letters[pos] > 0)
                letters[pos] = -1;
            else if (letters[pos] == 0)
                letters[pos] = i + 1;
        }

        int ret = -1;
        for (int i=0; i<26; i++) {
            if (letters[i] > 0) {
                if (ret == -1)
                    ret = letters[i] - 1;
                else if (ret > letters[i] - 1)
                    ret = letters[i] - 1;
            }
        }

        return ret;
    }
};

int main()
{
    Solution solu;

    string str1 = "aadadaad";
    cout << str1 << ": " << solu.firstUniqChar(str1) << endl;

    string str2 = "loveleetcode";
    cout << str2 << ": " << solu.firstUniqChar(str2) << endl;

    return 0;
}
```
