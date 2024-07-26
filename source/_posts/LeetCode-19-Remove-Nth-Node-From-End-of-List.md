---
title: LeetCode 19 - Remove Nth Node From End of List
urlname: leetcode_19
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-02 14:52:24
---

## 问题描述

Given a linked list, remove the n-th node from the end of list and return its head.

__Example:__

```
Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

__Note:__

Given n will always be valid.

__Follow up:__

Could you do this in one pass?

__Related Topics:__ `Linked List`, `Two Pointers`

原问题: [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

中文翻译版: [19. 删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

## 解决方案

这道题可以使用双指针方法解决，设定两个指针 `p1` 和 `p2`，先让指针 `p1` 遍历 `n` 个节点，然后指针 `p2` 开始和指针 `p1` 同步遍历链表，当指针 `p1` 遍历完链表后，指针 `p2` 刚好指向倒数第 `n` 个节点，此时删除该节点即可。

<details>
<summary>参考解题代码</summary>

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (head == NULL)
            return NULL;

        ListNode *pos1, *pos2, *prev;
        int step;

        prev = NULL;
        pos1 = pos2 = head;
        step = 0;
        while (pos2 != NULL) {
            pos2 = pos2->next;
            step += 1;
            if (step > n) {
                prev = pos1;
                pos1 = pos1->next;
            }
        }
        if (prev == NULL)
            head = head->next;
        else
            prev->next = pos1->next;

        return head;
    }
};
```

</details>