---
title: LeetCode 155 - Min Stack
urlname: leetcode_155
tags:
  - LeetCode
  - 算法
  - Algorithm
date: 2020-05-18 20:32:16
---

## 问题描述

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

* push(x) -- Push element x onto stack.
* pop() -- Removes the element on top of the stack.
* top() -- Get the top element.
* getMin() -- Retrieve the minimum element in the stack.

__Example 1:__

```
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```

__Constraints:__

* Methods `pop`, `top` and `getMin` operations will always be called on __non-empty__ stacks.

__Related Topics:__ `Stack`, `Design`

原问题: [155. Min Stack](https://leetcode.com/problems/min-stack/)

中文翻译版: [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)

## 解决方案

可以使用两个栈解决该题，一个栈stack存储push进来的数据，另一个栈min_stack存储现有数据的最小值，两个栈存储同等数量的元素。关键的操作 `push` 实现如下：

* `push` : push的数据为x，如果x小于min_stack栈顶的值，说明x比现用元素值都要小，则将x压入min_stack，否则将min_stack栈顶的值压入min_stack

<details>
<summary>参考解题代码</summary>

```cpp
class MinStack {
private:
    stack<int> m_stack;
    stack<int> m_min_stack;

public:
    /** initialize your data structure here. */
    MinStack() {

    }

    void push(int x) {
        if (m_stack.empty()) {
            m_stack.push(x);
            m_min_stack.push(x);
        } else {
            int top = m_min_stack.top();

            if (top > x)
                m_min_stack.push(x);
            else
                m_min_stack.push(top);

            m_stack.push(x);
        }
    }

    void pop() {
        m_stack.pop();
        m_min_stack.pop();
    }

    int top() {
        return m_stack.top();
    }

    int getMin() {
        return m_min_stack.top();
    }
};
```

</details>