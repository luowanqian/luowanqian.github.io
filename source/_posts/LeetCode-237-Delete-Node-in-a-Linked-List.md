---
title: LeetCode 237 - Delete Node in a Linked List
urlname: leetcode_237
date: 2018-03-23 16:11:27
tags:
 - 算法
 - LeetCode
---

## 问题描述

> Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.
>
> Supposed the linked list is `1 -> 2 -> 3 -> 4` and you are given the third node with value 3, the linked list should become `1 -> 2 -> 4` after calling your function.

题目的意思是给单链表的节点指针 (不包括尾节点)，然后删除该节点。

## 解决方案

如果按照常规单链表删除节点的操作，首先要找到要删除节点的前一个节点，然后再删除节点，但是这里只给定了待删除节点的指针，因此按照常规删除方法是不可行的，虽然待删除节点的前一节点我们不可知，但是我们可以知道该节点的下一个节点，如果我们把下一节点的内容复制到需要删除的节点上覆盖原有的内容，再把下一节点删除，那是不是就相当于把当前需要删除的节点删除了？根据题目描述，题目提供的节点不包括尾节点，因此不用考虑下一节点是 NULL 的情况。按照上面思路，可以有如下代码：

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */

class Solution
{
public:
    /*
     * the node is not the last node
     */
    void deleteNode(ListNode* node)
    {
        if (node == NULL)
            return;

        ListNode *next_node = node->next;
        node->val = next_node->val;
        node->next = next_node->next;
        delete next_node;
    }
};
```
