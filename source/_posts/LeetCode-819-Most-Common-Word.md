---
title: LeetCode 819 - Most Common Word
urlname: leetcode_819
tags:
  - LeetCode
  - 算法
date: 2018-04-21 11:08:30
---

## 问题描述

Given a paragraph and a list of banned words, return the most frequent word that is not in the list of banned words.  It is guaranteed there is at least one word that isn't banned, and that the answer is unique.

Words in the list of banned words are given in lowercase, and free of punctuation.  Words in the paragraph are not case sensitive.  The answer is in lowercase.

__Example:__

```
Input:
paragraph = "Bob hit a ball, the hit BALL flew far after it was hit."
banned = ["hit"]

Output: "ball"

Explanation:
"hit" occurs 3 times, but it is a banned word.
"ball" occurs twice (and no other word does), so it is the most frequent non-banned word in the paragraph.
Note that words in the paragraph are not case sensitive,
that punctuation is ignored (even if adjacent to words, such as "ball,"),
and that "hit" isn't the answer even though it occurs more because it is banned.
```

__Note:__

* `1 <= paragraph.length <= 1000`.
* `1 <= banned.length <= 100`.
* `1 <= banned[i].length <= 10`.
* The answer is unique, and written in lowercase (even if its occurrences in `paragraph` may have uppercase symbols, and even if it is a proper noun.)
* `paragraph` only consists of letters, spaces, or the punctuation symbols `!?',;.`
* Different words in `paragraph` are always separated by a space.
* There are no hyphens or hyphenated words.
* Words only consist of letters, never apostrophes or other punctuation symbols.

__Related Topics:__ String

原问题: [819. Most Common Word](https://leetcode.com/problems/most-common-word/description/)

中文翻译版: [819. Most Common Word](https://leetcode-cn.com/problems/most-common-word/description/)

## 解决方案

题目要求在字符串 `paragraph` 中找到出现次数最多的单词，并且单词不能是列表 `banned` 中的单词。解决该题可以设定两个哈希表，第一个哈希表的 key 为列表 `banned` 中的单词，用于判断单词是否在列表中，第二个哈希表的 key 为字符串 `paragraph` 中的单词，value 为该单词的出现次数。依次取出字符串 `paragraph` 中的单词，由于题目要求单词都是小写，所以要将单词转换成小写，然后在第一个哈希表中查找该单词是否存在，如果不存在，将第二个哈希表中该单词的出现次数加1，否则取字符串下一个单词，依次重复这个操作直到遍历完字符串 `paragraph` 的所有单词，接着根据第二个哈希表找出出现次数最多的单词。

```
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <unordered_set>
#include <unordered_map>
using namespace std;

class Solution {
public:
    bool delimter(char ch)
    {
        if (ch == ' ' || ch == '!' ||
            ch == '?' || ch == '\'' ||
            ch == ',' || ch == ';' || ch == '.')
            return true;
        else
            return false;
    }

    void lowercase(string& str)
    {
        transform(str.begin(), str.end(), str.begin(), ::tolower);
    }

    string mostCommonWord(string paragraph, vector<string>& banned)
    {
        unordered_set<string> banned_list;
        for (int i=0; i<banned.size(); i++)
            banned_list.insert(banned[i]);

        int times, max_times;
        string str, common_str;
        int start, end;
        unordered_map<string, int> word_counts;

        start = 0;
        max_times = 0;
        common_str = "";
        for (int i=0; i<paragraph.size(); i++) {
            if (delimter(paragraph[i])) {
                end = i;

                str = string(paragraph, start, end-start);
                lowercase(str);

                if (banned_list.find(str) == banned_list.end()) {
                    auto it = word_counts.find(str);
                    if (it == word_counts.end()) {
                        word_counts.insert(make_pair(str, 1));
                        times = 1;
                    } else {
                        it->second = it->second + 1;
                        times = it->second;
                    }
                    if (times > max_times) {
                        common_str = str;
                        max_times = times;
                    }
                }

                while (i<paragraph.size() && delimter(paragraph[i]))
                    i++;

                start = i;
            }
        }

        // the last word
        end = paragraph.size();
        if (start < end) {
            str = string(paragraph, start, end-start);
            lowercase(str);
            if (banned_list.find(str) == banned_list.end()) {
                auto it = word_counts.find(str);
                if (it == word_counts.end()) {
                    word_counts.insert(make_pair(str, 1));
                    times = 1;
                } else {
                    it->second = it->second + 1;
                    times = it->second;
                }
                if (times > max_times) {
                    common_str = str;
                    max_times = times;
                }
            }
        }

        return common_str;
    }
};

int main()
{
    Solution solu;
    string paragraph = "Bob hit a ball, the hit BALL flew far after it was hit.";
    vector<string> banned = {"hit"};

    cout << solu.mostCommonWord(paragraph, banned) << endl;

    return 0;
}
```
