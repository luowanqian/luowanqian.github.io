---
title: LeetCode 557 - Reverse Words in a String III
urlname: leetcode_557
date: 2018-04-13 23:44:37
tags:
 - LeetCode
 - 算法
---

## 问题描述

Given a string, you need to reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.

__Example 1:__

```
Input: "Let's take LeetCode contest"
Output: "s'teL ekat edoCteeL tsetnoc"
```

__Note:__ In the string, each word is separated by single space and there will not be any extra space in the string.

__Related Topics:__ String

原问题：[557. Reverse Words in a String III](https://leetcode.com/problems/reverse-words-in-a-string-iii/description/)

## 解决方案

题目主要意思是反转字符串中的每一个单词，因此这道题目主要是找到单词的位置，这里我们可以设定两个指针，分别命名为`start`和`end`，指针`start`指向单词的第一个字母的位置，指针`end`指向单词的最后一个字母的位置，然后将`[start, end]`里面的单词反转即可。至于指针怎么设置，我们可以先设置指针`start`指向第一个字母，然后找到空格字符的前一个字符的位置赋给指针`end`；接着找到下一个字母的位置赋给指针`start`，然后找到下一个空格字符的位置，将该空格字符的前一个字符的位置赋给指针`end`，如此循环，直到字符串被遍历完。

{% codeblock %}
#include <iostream>
#include <string>
using namespace std;

class Solution {
public:
    string reverseWords(string s)
    {
        int start, end;
        char tmp;

        start = 0;
        end = s.size();
        for (int i=0; i<s.size(); i++) {
            if (s[i] == ' ') {
                end = i - 1;

                // reverse word
                while (start < end) {
                    tmp = s[start];
                    s[start] = s[end];
                    s[end] = tmp;
                    start++;
                    end--;
                }

                while (i < s.size() && s[i] == ' ')
                    i++;
                start = i;
            }
        }

        // process last word
        end = s.size() - 1;
        while (start < end) {
            tmp = s[start];
            s[start] = s[end];
            s[end] = tmp;
            start++;
            end--;
        }

        return s;
    }
};

int main()
{
    Solution solu;
    string str1 = "Let's take LeetCode contest";

    cout << str1 << endl;
    cout << solu.reverseWords(str1) << endl;

    return 0;
}
{% endcodeblock %}
