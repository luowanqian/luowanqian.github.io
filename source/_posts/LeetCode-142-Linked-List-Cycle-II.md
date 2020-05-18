---
title: LeetCode 142 - Linked List Cycle II
urlname: leetcode_142
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-18 21:45:40
---

## 问题描述

Given a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

To represent a cycle in the given linked list, we use an integer `pos` which represents the position (0-indexed) in the linked list where tail connects to. If `pos` is `-1`, then there is no cycle in the linked list.

Note: Do not modify the linked list.

__Example 1:__

```
Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

{% asset_img 1.png 350 %}

__Example 2:__

```
Input: head = [1,2], pos = 0
Output: tail connects to node index 0
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

{% asset_img 2.png 170 %}

__Example 3:__

```
Input: head = [1], pos = -1
Output: no cycle
Explanation: There is no cycle in the linked list.
```

{% asset_img 3.png 50 %}

__Follow-up:__

Can you solve it without using extra space?

__Related Topics:__ `Linked List`, `Two Pointers`

原问题: [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)

中文翻译版: [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

## 解决方案

### 解决方案1

用一个 `Set` 保存已访问的节点，此时可以遍历整个链表并返回第一个出现重复的节点。时间复杂度为 `O(n)`，空间复杂度为 `O(n)`

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
    ListNode *detectCycle(ListNode *head) {
        set<ListNode*> node_set;
        ListNode *node;

        node = head;
        while (node != NULL) {
            if (node_set.find(node) == node_set.end())
                node_set.insert(node);
            else
                return node;
            node = node->next;
        }

        return node;
    }
};
```

</details>

### 解决方案2

方案2是 LeetCode 141 解题思路的进一步改进，算法分为两个阶段：

* 阶段1 : 首先使用快指针 `fast` 和慢指针 `slow` 遍历链表，如果链表有环，两指针最终会相遇到某个节点
* 阶段2 : 初始化额外的两个指针，`ptr1` 指向链表的头节点，`ptr2` 指向相遇节点。然后，我们每次将它们往前移动一步，直到它们相遇，它们相遇的点就是环的入口，返回这个节点

关于阶段2的实现原理，这里提供一个不错的解答说明：[LeetCode: 环形链表 II 官方解答](https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/huan-xing-lian-biao-ii-by-leetcode/)

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
    ListNode *detectCycle(ListNode *head) {
        if (NULL == head || NULL == head->next)
            return NULL;

        ListNode *slow, *fast;

        slow = fast = head;
        while (fast != NULL && fast->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast)
                break;
        }
        // no cycle
        if (slow != fast)
            return NULL;

        slow = head;
        while (slow != fast) {
            slow = slow->next;
            fast = fast->next;
        }

        return fast;
    }
};
```

</details>