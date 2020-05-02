---
title: LeetCode 160 - Intersection of Two Linked Lists
urlname: leetcode_160
tags:
  - LeetCode
  - 算法
  - Alogrithm
date: 2020-05-02 09:39:59
---

## 问题描述

Write a program to find the node at which the intersection of two singly linked lists begins.

For example, the following two linked lists:

{% asset_img 160_statement.png 450 %}

begin to intersect at node `c1`.

__Example 1:__

{% asset_img 160_example_1.png 450 %}

```
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
Output: Reference of the node with value = 8
Input Explanation: The intersected node's value is 8 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads as [5,0,1,8,4,5]. There are 2 nodes before the intersected node in A; There are 3 nodes before the intersected node in B.
```

__Example 2:__

{% asset_img 160_example_2.png 400 %}

```
Input: intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
Output: Reference of the node with value = 2
Input Explanation: The intersected node's value is 2 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [0,9,1,2,4]. From the head of B, it reads as [3,2,4]. There are 3 nodes before the intersected node in A; There are 1 node before the intersected node in B.
```

__Example 3:__

{% asset_img 160_example_3.png 250 %}

```
Input: intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
Output: null
Input Explanation: From the head of A, it reads as [2,6,4]. From the head of B, it reads as [1,5]. Since the two lists do not intersect, intersectVal must be 0, while skipA and skipB can be arbitrary values.
Explanation: The two lists do not intersect, so return null.
```

__Notes:__

* If the two linked lists have no intersection at all, return `null`.
* The linked lists must retain their original structure after the function returns.
* You may assume there are no cycles anywhere in the entire linked structure.
* Your code should preferably run in `O(n)` time and use only `O(1)` memory.

__Related Topics:__ `Linked List`

原问题: [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

中文翻译版: [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

## 解决方案

这里假设两条链表有相交节点，如下图所示：

{% asset_img equation.png 400 %}

图中 `AD` 线段代表链表1，线段 `CB` 加 线段 `BD` 代表链表2，链表1要长于链表2，两条链表相交于节点 `B`，链表1长度为 `|AD| = |AB| + |BD| = p + n`，链表2长度为 `|CB| + |BD| = m + n`（__注明__：这里长度定义为从线段开始节点遍历到结束节点所要移动的节点数）

现在开始同时遍历链表1和链表2，由于链表2比链表1要短，所以链表2最先遍历完，此时链表1遍历到节点 `E`，因此 `|AE| = m + n`，继续遍历链表2直到遍历结束，从节点 `E` 到节点 `D` 的长度为 `ED = q` 

根据图中表示，我们可以得到一个等式，那就是

```
    |AB| + |BD| = |AE| + |ED|
==>  p + n = m + n + q
==>  p - m = q
```

从上面等式可以得到 `|AB| - |CB| = q`，等式说明了链表1头节点 `A` 到相交节点 `B` 的长度比链表2头节点 `C` 到相交节点 `B` 长度要长 `q`，这个 `q` 是已知量，说明链表1第 `q` 个节点到节点 `B` 的距离要等于链表2节点 `C` 到节点 `B` 的距离。这里就可以得出该题的一个解题思路：

```
设定两个指针p1和p2，分别用于遍历链表1和链表2，指针p1先移动到链表1的第q个节点，
然后指针p2开始遍历链表2，直到 p1 == p2，此时 p1 为两个链表相交节点
```

<details>
<summary>参考解题代码</summary>

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (NULL == headA || NULL == headB)
            return NULL;
        if (headA == headB)
            return headA;

        ListNode *prevA, *prevB;
        ListNode *currA, *currB;
        ListNode *posA, *posB;

        prevA = prevB = NULL;
        posA = currA = headA;
        posB = currB = headB;
        while ((currA != NULL) || (currB != NULL)) {
            if (currA != NULL) {
                prevA = currA;
                currA = currA->next;
            } else {
                posB = posB->next;
            }
            if (currB != NULL) {
                prevB = currB;
                currB = currB->next;
            } else {
                posA = posA->next;
            }
        }
        // have intersection
        if (prevA == prevB) {
            while (posA != posB) {
                posA = posA->next;
                posB = posB->next;
            }
            return posA;
        }

        return NULL;
    }
};
```

</details>