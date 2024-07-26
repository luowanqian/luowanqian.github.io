---
title: LeetCode 203 - Remove Linked List Elements
urlname: leetcode_203
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-10 09:43:35
---

## 问题描述

Remove all elements from a linked list of integers that have value `val`.

__Example:__

```
Input:  1->2->6->3->4->5->6, val = 6
Output: 1->2->3->4->5
```

__Related Topics:__ `Linked List`

原问题: [203. Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)

中文翻译版: [203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)

## 解决方案

链表删除基本操作的实现，使用三个指针 `prev`、`curr` 以及 `next`，`prev` 指向 `curr` 前一个节点，`curr` 指向要删除的节点，`next` 指向 `curr` 下一个节点。`curr` 从头节点开始遍历，按照链表删除元素操作依次删除值为 `val` 的节点即可

```cpp
next = curr->next;
prev->next = next;
delete curr;
curr = next;
```

这里要注意的是头节点，如果头节点的值刚好 `val`，这个删除操作和上面的代码片段有少许区别

<details>
<summary>参考解题代码</summary>

```cpp
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode *prev, *curr, *next;

        prev = NULL;
        curr = head;
        while (curr != NULL) {
            if (curr->val == val) {
                next = curr->next;
                if (prev == NULL)
                    head = next;
                else
                    prev->next = next;
                delete curr;
                curr = next;
            } else {
                prev = curr;
                curr = curr->next;
            }
        }

        return head;
    }
};
```

</details>