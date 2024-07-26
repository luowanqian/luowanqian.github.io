---
title: LeetCode 206 - Reverse Linked List
urlname: leetcode_206
list_number: false
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-16 09:38:22
---

## 1. 问题描述

Reverse a singly linked list.

__Example__:

```
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
```

__Follow up__:

A linked list can be reversed either iteratively or recursively. Could you implement both?

__Related Topics:__ `Linked List`

原问题: [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

中文翻译版: [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

## 2. 解决方案

### 2.1 非递归

非递归的解决方案是按原始顺序迭代节点，并将它们逐个移动到链表的头部。以下举个例子进行说明

{% asset_img no_recur_1.png 400 %}

黑色节点23是原始头节点。

1. 首先，我们将黑色节点的下一个节点（即节点6）移动到链表的头部：

    {% asset_img no_recur_2.png 500 %}

2. 然后，我们将黑色节点的下一个节点（即节点15）移动到链表的头部：

    {% asset_img no_recur_3.png 500 %}

3. 黑色结点的下一个结点现在是空。因此，我们停止这一过程并返回新的头结点15

从以上流程可以知道该算法的时间复杂度为 `O(N)`，空间复杂度为 `O(1)`

<details>
<summary>参考解题代码</summary>

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode *cur, *next;

        cur = head;
        while (cur != NULL && cur->next != NULL) {
            next = cur->next;
            cur->next = next->next;
            next->next = head;
            head = next;
        }

        return head;
    }
};
```

</details>

### 2.2 递归

用递归解此题时，此时递归的函数返回的是反转链表后的新的头节点 `new_head`，进行整合时，只需要将头节点 `head` 添加到反转链表的尾部即可。拿前面例子来说，头节点23后部分都已经反转完毕，此时链表构造为

```
23 -> 6 <- 15
```

此时 `new_head` 指向节点15，`head` 指向节点23，要将节点23添加到反转链表尾部，执行以下操作即可：

```cpp
head->next->next = head
head->next = NULL
```

经过以上操作以后，链表构造变为

```
NULL <- 23 <- 6 <- 15
```

最后返回新的头节点 `new_head` 即可

<details>
<summary>参考解题代码</summary>

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == NULL || head->next == NULL)
            return head;

        ListNode *new_head;

        new_head = reverseList(head->next);
        head->next->next = head;
        head->next = NULL;

        return new_head;
    }
};
```

</details>