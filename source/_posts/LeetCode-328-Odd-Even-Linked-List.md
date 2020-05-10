---
title: LeetCode 328 - Odd Even Linked List
urlname: leetcode_328
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-10 10:15:39
---

## 问题描述

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

__Example 1:__

```
Input: 1->2->3->4->5->NULL
Output: 1->3->5->2->4->NULL
```

__Example 2:__

```
Input: 2->1->3->5->6->4->7->NULL
Output: 2->3->6->7->1->5->4->NULL
```

__Note:__

* The relative order inside both the even and odd groups should remain as it was in the input.
* The first node is considered odd, the second node even and so on ...

__Related Topics:__ `Linked List`

原问题: [328. Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)

中文翻译版: [328. 奇偶链表](https://leetcode-cn.com/problems/odd-even-linked-list/)

## 解决方案

解此题主要需要两个指针 `odd` 以及 `even`，`odd` 指向奇节点，`even` 指向偶节点。`odd` 从第一个节点开始遍历，`even` 从第二节点开始遍历，两个指针每次移动都是移动两个节点，每次移动前，先将 next 字段指向下下个节点，即

```cpp
odd = odd->next->next;
even = even->next->next;
```

这样当两个指针遍历完链表时，链表刚好被分为两部分，`odd` 指向奇节点链表最后一个节点，此时将这两个链表拼在一起即可。偶节点链表头部是头节点的下一个节点，由于在指针遍历时，会破坏链表结构，因此在遍历前先保存该节点 `node`，链表拼接时只需

```cpp
odd->next = node
```

<details>
<summary>参考解题代码</summary>

```cpp
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if (head == NULL || head->next == NULL)
            return head;

        ListNode *even, *odd, *node;

        odd = head;
        even = head->next;
        node = head->next;
        while (odd != NULL && odd->next != NULL
               && even != NULL && even->next != NULL) {
            odd->next = odd->next->next;
            odd = odd->next;
            even->next = even->next->next;
            even = even->next;
        }
        odd->next = node;

        return head;
    }
};
```

</details>