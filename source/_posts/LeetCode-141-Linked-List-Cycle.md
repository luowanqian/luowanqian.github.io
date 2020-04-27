---
title: LeetCode 141 - Linked List Cycle
urlname: leetcode_141
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-04-27 20:43:26
---

## 问题描述

Given a linked list, determine if it has a cycle in it.

To represent a cycle in the given linked list, we use an integer `pos` which represents the position (0-indexed) in the linked list where tail connects to. If `pos` is `-1`, then there is no cycle in the linked list.

__Example 1:__

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

{% asset_img circularlinkedlist.png 350 %}

__Example 2:__

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

{% asset_img circularlinkedlist_test2.png 180 %}

__Example 3:__

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

{% asset_img circularlinkedlist_test3.png 60 %}

__Follow up:__

Can you solve it using `O(1)` (i.e. constant) memory?

__Related Topics:__ `Linked List`, `Two Pointers`

原问题: [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

中文翻译版: [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

## 解决方案

该题可以使用双指针方法进行解决，设定快指针 `fast` 和慢指针 `slow`，两指针同时从头节点 `head` 出发，慢指针每前进一个节点，快指针就前进两个节点，如果链表有环，由于两指针前进速度不同，最终两指针会汇聚在同一个节点，即 `fast == slow`，否则，快指针会最先到达链表节点，两指针不会汇聚在一起。

<details>
<summary>参考解题代码</summary>

```cpp
#include <iostream>
#include "List.h"
using namespace std;

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
    bool hasCycle(ListNode *head) {
        if (head == NULL)
            return false;

        ListNode *fast, *slow;
        fast = slow = head;
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast)
                return true;
        }

        return false;
    }
};

int main()
{
    ListNode *node1 = create_list_node(1);
    ListNode *node2 = create_list_node(2);
    ListNode *node3 = create_list_node(3);
    ListNode *node4 = create_list_node(4);
    connect_list_nodes(node1, node2);
    connect_list_nodes(node2, node3);
    connect_list_nodes(node3, node4);
    connect_list_nodes(node4, node2);

    Solution solu;
    cout << solu.hasCycle(node1) << endl;

    return 0;
}
```

</details>