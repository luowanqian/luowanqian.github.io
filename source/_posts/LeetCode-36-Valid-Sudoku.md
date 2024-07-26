---
title: LeetCode 36 - Valid Sudoku
urlname: leetcode_36
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-04-24 20:27:55
---

## 问题描述

Determine if a 9x9 Sudoku board is valid. Only the filled cells need to be validated __according to the following rules__:

1. Each row must contain the digits `1-9` without repetition.
2. Each column must contain the digits `1-9` without repetition.
3. Each of the 9 `3x3` sub-boxes of the grid must contain the digits `1-9` without repetition.

{% asset_img 1.png 250 %}

A partially filled sudoku which is valid.

The Sudoku board could be partially filled, where empty cells are filled with the character `'.'`.

__Example 1:__

```
Input:
[
  ["5","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
Output: true
```

__Example 2:__

```
Input:
[
  ["8","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
Output: false
Explanation: Same as Example 1, except with the 5 in the top left corner being 
    modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.
```

__Note:__

* A Sudoku board (partially filled) could be valid but is not necessarily solvable.
* Only the filled cells need to be validated according to the mentioned rules.
* The given board contain only digits `1-9` and the character `'.'`.
* The given board size is always `9x9`.

__Related Topics:__ `Hash Table`

原问题: [36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)

中文翻译版: [36. 有效的数独](https://leetcode-cn.com/problems/valid-sudoku/)

## 解决方案

### 方案1

根据题目说明，一个有效的数独，满足三个条件：

1. 每行数字有重复数字
2. 每列不能有重复数字
3. 每个 `3x3` 块中不能有重复数字

怎么判断一行、一列或者一个小块中是否有重复数字，此时我们可以给用哈希表进行快速查找判断。首先我们分别给每一行、每一列以及每一小块建立一个哈希表，然后我们遍历所有数字，当遍历到某个数字时，我们根据该数字所处的行、列以及小块找到对应的哈希表，查找该数字是否在哈希表中出现，如果出现，说明该数独是无效的，否则我们将该数字存入哈希表，继续遍历。

<details>
<summary>参考解题代码1</summary>

```cpp
#include <vector>
#include <unordered_set>
#include <iostream>
using namespace std;


class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        vector<unordered_set<char>> row_sets(9);
        vector<unordered_set<char>> column_sets(9);
        vector<unordered_set<char>> block_sets(9);

        char ch;
        int block_id;
        for (auto i=0; i<9; i++) {
            for (auto j=0; j<9; j++) {
                ch = board[i][j];

                if (ch == '.')
                    continue;

                if (row_sets[i].find(ch) == row_sets[i].end())
                    row_sets[i].insert(ch);
                else
                    return false;

                if (column_sets[j].find(ch) == column_sets[j].end())
                    column_sets[j].insert(ch);
                else
                    return false;

                block_id = int(i / 3.0) * 3 + int(j / 3.0);
                if (block_sets[block_id].find(ch) == block_sets[block_id].end())
                    block_sets[block_id].insert(ch);
                else
                    return false;
            }
        }

        return true;
    }
};

int main()
{
    vector<vector<char>> board = {
            {'5', '3', '.', '.', '7', '.', '.', '.', '.'},
            {'6', '.', '.', '1', '9', '5', '.', '.', '.'}, 
            {'.', '9', '8', '.', '.', '.', '.', '6', '.'}, 
            {'8', '.', '.', '.', '6', '.', '.', '.', '3'}, 
            {'4', '.', '.', '8', '.', '3', '.', '.', '1'}, 
            {'7', '.', '.', '.', '2', '.', '.', '.', '6'}, 
            {'.', '6', '.', '.', '.', '.', '2', '8', '.'}, 
            {'.', '.', '.', '4', '1', '9', '.', '.', '5'}, 
            {'.', '.', '.', '.', '8', '.', '.', '7', '9'}
    };

    for (auto i=0; i<board.size(); i++) {
        for (auto j=0; j<board[i].size(); j++) {
            cout << board[i][j] << " ";
        }
        cout << endl;
    }

    Solution solu;
    cout << "Is valid: " << solu.isValidSudoku(board) << endl;

    return 0;
}
```

</details>

### 方案2

同方案1的思想，只不过此时一行、一列以及一小块对应的哈希表分别用一个整数进行替代，通过该整数的某一位是否为1来进行重复数字判断，主要使用的是位与运算 `&` 和位或运算 `|`。当遍历到某个数字 `x` 时，该数字所在行对应的整数为 `y`，此时判断该数字是否重复可以进行如下操作：

```c
y & (1 << x)
```

如果该表达式值非0，说明 `y` 的第 `x` 位是1，这说明该数字之前出现过，否则该表达式值为0。如果该数字未重复出现，则 `y` 设为：

```cpp
y = y | (1 << x) 
```

<details>
<summary>参考解题代码2</summary>

```cpp
#include <iostream>
#include <cmath>
#include <vector>
using namespace std;


class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int row_status[board.size()];
        int col_status[board.size()];
        int cell_status[board.size()];
        int digit, cell, block_size, num_blocks;

        for (int i=0; i<board.size(); i++) {
            row_status[i] = 0;
            col_status[i] = 0;
            cell_status[i] = 0;
        }

        block_size = int(sqrt(board.size()));
        num_blocks = board.size() / block_size;
        for (int i=0; i<board.size(); i++) {
            for (int j=0; j<board[i].size(); j++) {
                if (board[i][j] == '.')
                    continue;

                digit = 1 << (board[i][j] - '0');
                cell = (i / block_size) * num_blocks + (j / block_size);
                if ((row_status[i] & digit) != 0)
                    return false;
                if ((col_status[j] & digit) != 0)
                    return false;
                if ((cell_status[cell] & digit) != 0)
                    return false;
                row_status[i] |= digit;
                col_status[j] |= digit;
                cell_status[cell] |= digit;
            }
        }

        return true;
    }
};

int main()
{
    vector<vector<char>> board = {
            {'5', '3', '.', '.', '7', '.', '.', '.', '.'},
            {'6', '.', '.', '1', '9', '5', '.', '.', '.'},
            {'.', '9', '8', '.', '.', '.', '.', '6', '.'},
            {'8', '.', '.', '.', '6', '.', '.', '.', '3'},
            {'4', '.', '.', '8', '.', '3', '.', '.', '1'},
            {'7', '.', '.', '.', '2', '.', '.', '.', '6'},
            {'.', '6', '.', '.', '.', '.', '2', '8', '.'},
            {'.', '.', '.', '4', '1', '9', '.', '.', '5'},
            {'.', '.', '.', '.', '8', '.', '.', '7', '9'}
    };

    for (auto i=0; i<board.size(); i++) {
        for (auto j=0; j<board[i].size(); j++) {
            cout << board[i][j] << " ";
        }
        cout << endl;
    }

    Solution solu;
    cout << "Is valid: " << solu.isValidSudoku(board) << endl;

    return 0;
}
```

</details>