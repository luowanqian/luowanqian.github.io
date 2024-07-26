---
title: LeetCode 657 - Judge Route Circle
urlname: leetcode_657
tags:
  - LeetCode
  - 算法
date: 2018-04-21 10:40:53
---

## 问题描述

Initially, there is a Robot at position (0, 0). Given a sequence of its moves, judge if this robot makes a circle, which means it moves back to __the original place__.

The move sequence is represented by a string. And each move is represent by a character. The valid robot moves are `R` (Right), `L` (Left), `U` (Up) and `D` (down). The output should be true or false representing whether the robot makes a circle.

__Example 1:__

```
Input: "UD"
Output: true
```

__Example 2:__

```
Input: "LL"
Output: false
```

__Related Topics:__ String

原问题: [657. Judge Route Circle](https://leetcode.com/problems/judge-route-circle/description/)

中文翻译版: [657. 判断路线成圈](https://leetcode-cn.com/problems/judge-route-circle/description/)

## 解决方案

根据题目描述，可以设定一个二维坐标 (x, y)，根据指令做相应的改变：

* Right: x = x + 1
* Left: x = x - 1
* Up: y = y + 1
* Down: y = y - 1

最后判断二维坐标 (x, y) 是否为原点 (0, 0) 即可。实现代码如下：

```
#include <iostream>
using namespace std;

class Solution {
public:
    bool judgeCircle(string moves)
    {
        int x = 0, y = 0;

        for (int i=0; i<moves.size(); i++) {
            switch (moves[i]) {
                case 'U':
                    y++;
                    break;
                case 'D':
                    y--;
                    break;
                case 'L':
                    x--;
                    break;
                case 'R':
                    x++;
                    break;
            }
        }

        return (x == 0 && y == 0);
    }
};

int main()
{
    Solution solu;
    string str1 = "UD";
    cout << str1 << ": " << solu.judgeCircle(str1) << endl;
    string str2 = "LL";
    cout << str2 << ": " << solu.judgeCircle(str2) << endl;

    return 0;
}
```
