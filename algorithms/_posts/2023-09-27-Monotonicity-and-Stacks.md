---
title: Monotonicity and Stacks
category: [Stack]
---

> This post highlights the possible usage of stacks (as a data structure) to resolve problems where monotonicity is of value.

Monotonic stacks, a powerful concept in computer science, are instrumental in solving various problems efficiently. In this blog post, we'll unravel the essence of monotonic stacks, explore their applications, and walk through practical examples to solidify our understanding.

## What is Monotonicity?

Monotonicity is a mathematical concept describing the behavior of a function or sequence with respect to the direction of change. It characterizes whether the values of the function or sequence consistently increase, decrease, or remain constant as the input variable varies.

There are two main types of monotonicity:

1. **Monotonic Increasing (Non-Decreasing):**
   - In a monotonic increasing function or sequence, the values never decrease as the input variable increases. Formally, for any two points \(x_1\) and \(x_2\) where \(x_1 \leq x_2\), the corresponding function values satisfy \(f(x_1) \leq f(x_2)\).

2. **Monotonic Decreasing (Non-Increasing):**
   - In a monotonic decreasing function or sequence, the values never increase as the input variable increases. Formally, for any two points \(x_1\) and \(x_2\) where \(x_1 \leq x_2\), the corresponding function values satisfy \(f(x_1) \geq f(x_2)\).

Monotonicity is a fundamental concept in mathematics, providing insights into the behavior of functions and sequences. It has applications in calculus, optimization, and various algorithmic designs, where understanding the direction of change is crucial for problem-solving and decision-making.

## Understanding Monotonic Stacks

A monotonic stack is a data structure that maintains either non-decreasing or non-increasing order of its elements. It proves to be particularly useful in scenarios where you need to find the next greater or smaller element in an array, track the nearest greater or smaller element on both sides, or solve problems involving increasing or decreasing sequences.

## Basic Concepts

### 1. **Monotonic Stack Properties:**
   - A monotonic stack can be either increasing or decreasing.
   - Elements are added or removed based on maintaining the desired monotonic order.

### 2. **Applications:**
   - Monotonic stacks are frequently used to solve problems involving finding the next greater/smaller element or tracking elements in increasing/decreasing order.

### 3. **Operations:**
   - The two main operations on a monotonic stack are "push" and "pop." The key is to maintain the monotonic property during these operations.

## Constructing a Monotonic Stack

Let's dive into the step-by-step construction of a monotonic stack.

1. **Initialize an Empty Stack:**
   ```python
   stack = []
   ```

2. **Push Operation:**
   ```python
   def push(stack, element):
       while stack and element < stack[-1]:
           stack.pop()
       stack.append(element)
   ```

3. **Pop Operation:**
   ```python
   def pop(stack):
       stack.pop()
   ```

## Practical Examples

### Example 1: Next Greater Element

Given an array, find the next greater element for each element in the array.

```python
def next_greater_element(nums):
    result = [-1] * len(nums)
    stack = []

    for i in range(len(nums)):
        while stack and nums[i] > nums[stack[-1]]:
            result[stack.pop()] = nums[i]
        stack.append(i)

    return result
```

### Example 2: Stock Span Problem

Calculate the span of stock prices, where the span is defined as the maximum number of consecutive days the price of a stock is less than or equal to the current day.

```python
def stock_span(prices):
    span = [0] * len(prices)
    stack = []

    for i in range(len(prices)):
        while stack and prices[i] >= prices[stack[-1]]:
            stack.pop()
        span[i] = i - stack[-1] if stack else i + 1
        stack.append(i)

    return span
```

## Conclusion

Monotonic stacks offer an elegant and efficient solution to a variety of problems encountered in algorithmic and competitive programming. By understanding the principles of maintaining monotonic order and practicing with examples, you'll be well-equipped to apply this powerful concept to real-world coding challenges. Happy coding!

---