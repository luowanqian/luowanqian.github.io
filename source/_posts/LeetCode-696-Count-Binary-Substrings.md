---
title: LeetCode 696 - Count Binary Substrings
urlname: leetcode_696
tags:
  - LeetCode
  - 算法
date: 2018-04-21 22:35:26
---

## 问题描述

Give a string `s`, count the number of non-empty (contiguous) substrings that have the same number of 0's and 1's, and all the 0's and all the 1's in these substrings are grouped consecutively.

Substrings that occur multiple times are counted the number of times they occur.

__Example 1:__

```
Input: "00110011"
Output: 6
Explanation: There are 6 substrings that have equal number of consecutive 1's and 0's: "0011", "01", "1100", "10", "0011", and "01".

Notice that some of these substrings repeat and are counted the number of times they occur.

Also, "00110011" is not a valid substring because all the 0's (and 1's) are not grouped together.
```

__Example 2:__

```
Input: "10101"
Output: 4
Explanation: There are 4 substrings: "10", "01", "10", "01" that have equal number of consecutive 1's and 0's.
```

__Note:__

* s.length will be between 1 and 50,000.


* s will only consist of "0" or "1" characters.

__Related Topics:__ String

原问题: [696. Count Binary Substrings](https://leetcode.com/problems/count-binary-substrings/description/)

中文翻译版: [696. 计数二进制子串](https://leetcode-cn.com/problems/count-binary-substrings/description/)

## 解决方案

如果使用暴力方法进行解决，很容易超时，因为字符串 `s` 最长为 50000，因此需要考虑一些取巧的方法。这里可以创建一个数组，数组元素存储每个连续子串的长度，例如，将字符串 `1111000011010001011` 转化为数组 `[4, 4, 2, 1, 1, 3, 1, 1, 2]`，根据这个数组，我们可以很方便地获得满足条件的子串个数，只需要将数组中相邻元素之间最小的那个求和即可，即

```
n = min(4, 4) + min(4, 2) + min(2, 1) + min(1, 1)
	+ min(1, 3) + min(3, 1) + min(1, 1) + min(1, 2)
  = 4 + 2 + 1 + 1 + 1 + 1 + 1 + 1
  = 12
```

实现代码如下：

```
#include <iostream>
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    int countBinarySubstrings(string s)
    {
        vector<int> trans;
        int num_bs = 0;
        int count;

        count = 0;
        for (int i=0; i<s.size(); i++) {
            count++;
            if (i+1 < s.size() && s[i] != s[i+1]) {
                trans.push_back(count);
                count = 0;
                continue;
            }
        }
        if (count > 0)
            trans.push_back(count);

        for (int i=0; i<trans.size()-1; i++)
            num_bs += trans[i] > trans[i+1] ? trans[i+1] : trans[i];

        return num_bs;
    }
};

int main()
{
    Solution solu;

    string str1 = "00110011";
    cout << str1 << ": " << solu.countBinarySubstrings(str1) << endl;

    string str2 = "10101";
    cout << str2 << ": " << solu.countBinarySubstrings(str2) << endl;

    return 0;
}
```
