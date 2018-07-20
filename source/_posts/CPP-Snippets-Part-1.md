---
title: C++ Snippets Part 1
date: 2018-04-22 23:27:19
urlname: cpp_snippets_part1
tags:
 - C++
 - 编程语言
---

## Introduction

C++ Snippets

## A string tokenizer

字符串分隔

```
#include <string>
#include <vector>
#include <iostream>

using namespace std;

void tokenize(const string &str, vector<string> &tokens, const string &delimiters = " ")
{
    // Skip delimiters at beginning.
    string::size_type lastPos = str.find_first_not_of(delimiters, 0);
    // Find first "non-delimiter".
    string::size_type pos = str.find_first_of(delimiters, lastPos);

    while (string::npos != pos || string::npos != lastPos) {
        // Found a token, add it to the vector.
        tokens.push_back(str.substr(lastPos, pos - lastPos));
        // Skip delimiters.  Note the "not_of"
        lastPos = str.find_first_not_of(delimiters, pos);
        // Find next "non-delimiter"
        pos = str.find_first_of(delimiters, lastPos);
    }
}

int main()
{
    vector<string> tokens;
    string str("Split me up! Word1 Word2 Word3.");

    tokenize(str, tokens);

    cout << "String: " << str << endl;
    cout << "Tokens: " << endl;
    for (auto it = tokens.begin(); it != tokens.end(); it++)
        cout << *it << endl;
    return 0;
}
```
