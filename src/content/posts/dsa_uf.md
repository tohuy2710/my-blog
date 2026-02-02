---
title: '[DSA] Union Find'
published: 2025-08-10
description: 'Hi there!, I decided to write this series when I am revising knowledge that I learned from DSA Course in VNU-UET (INT2210) as a part of prepairing for the school official intership program in next summer. In this series, I will rewrite my note in DSA, the topics would be in order of UET official course (INT2210), I will provide any sources that I learned and problems from Leetcode that I have done. Thanks for reached there, I hope it would help you even a little ^^'
image: 'vermouth.jpg'
tags: [dsa, leetcode, uet, vnu]
category: 'life'
draft: false 
lang: ''
---

# Union-Find 

Union-Find is usually one of the first data structures you learn when studying Data Structures and Algorithms at UET. Many students find it confusing at first, but once you understand the idea, it becomes a very powerful and simple tool.

As its name suggests, Union-Find supports two core operations:

* **Find(x)** → Determine which set element `x` belongs to
* **Union(x, y)** → Merge the sets that contain `x` and `y`

Additionally, we often use:

* **Connected(x, y)** → Check if `x` and `y` are in the same set

---

## Basic Interface

```
class UF:
    void Union(p, q)
    int Find(p)
    boolean Connected(p, q)
```

---

## 1. Quick Find

**Idea:**
Each element stores the ID of the set it belongs to.

### Time Complexity

* Initialization: **O(N)**
* Find: **O(1)**
* Connected: **O(1)**
* Union: **O(N)**

### Explanation

To union two elements, we must scan the entire array and change all elements with the same ID as `p` to the ID of `q`.
This makes Union expensive.

✅ Fast Find
❌ Slow Union

---

## 2. Quick Union

**Idea:**
Each element points to a parent. The root represents the set.

### Time Complexity

* Initialization: **O(N)**
* Find: **O(N)** (worst case)
* Union: **O(N)**
* Connected: **O(N)**

### Explanation

We attach the root of one tree to the root of another tree.
However, trees can become tall, making Find slow.

❌ Trees may become unbalanced

---

## 3. Weighted Quick Union

**Idea:**
Always attach the **smaller tree** under the **larger tree**.

We store an additional array `size[]` that tracks the size of each tree.

### Steps for Union(p, q)

1. Find roots `F1 = Find(p)`, `F2 = Find(q)`
2. Compare sizes of both trees
3. Attach smaller tree under larger tree

### Time Complexity

* Union: **O(log N)**
* Find: **O(log N)**

This greatly improves performance by keeping trees shallow.

---

## 4. Path Compression

**Idea:**
While performing Find, make every visited node point **directly to the root**.

### Result

After enough operations:

* Union ≈ **O(1)**
* Find ≈ **O(1)** (amortized)

In practice, Weighted Quick Union + Path Compression is extremely fast and is the standard implementation of Union-Find.

---

## Summary

| Approach           | Union    | Find     | Connected |
| ------------------ | -------- | -------- | --------- |
| Quick Find         | O(N)     | O(1)     | O(1)      |
| Quick Union        | O(N)     | O(N)     | O(N)      |
| Weighted QU        | O(log N) | O(log N) | O(log N)  |
| + Path Compression | ~O(1)    | ~O(1)    | ~O(1)     |



