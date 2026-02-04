---
title: '[DSA] Union Find'
published: 2025-08-10
description: 'Hi there!, I decided to write this series when I am revising knowledge that I learned from DSA Course in VNU-UET (INT2210) as a part of prepairing for the school official intership program in next summer. In this series, I will rewrite my note in DSA, the topics would be in order of UET official course (INT2210), I will provide any sources that I learned and problems from Leetcode that I have done. Thanks for reached there, I hope it would help you even a little ^^'
image: 'vermouth.jpg'
tags: [dsa, leetcode, uet, vnu]
category: 'dsa'
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


## LeetCode Practices

There are some LeetCode problems that I recommend you to practice, I provide my solution I 've written with ([algs4 library](https://algs4.cs.princeton.edu/code/)) (as a part of UET Course)

### [684. Redundant Connection (Med)](https://leetcode.com/problems/redundant-connection/description/)

**Solution**

```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        int[] id = new int[n + 1];
        WeightedQuickUnionUF uf = new WeightedQuickUnionUF(n + 1);
        for (int[] edge : edges) {
            if (uf.connected(edge[0], edge[1])) {
                return edge;
            }
            uf.union(edge[0], edge[1]);
        }
        return edges[0];
    }

    public class WeightedQuickUnionUF {
        private int[] parent;   // parent[i] = parent of i
        private int[] size;     // size[i] = number of elements in subtree rooted at i
        private int count;      // number of components

        /**
         * Initializes an empty union-find data structure with
         * {@code n} elements {@code 0} through {@code n-1}.
         * Initially, each element is in its own set.
         *
         * @param  n the number of elements
         * @throws IllegalArgumentException if {@code n < 0}
         */
        public WeightedQuickUnionUF(int n) {
            count = n;
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }

        /**
         * Returns the number of sets.
         *
         * @return the number of sets (between {@code 1} and {@code n})
         */
        public int count() {
            return count;
        }

        /**
         * Returns the canonical element of the set containing element {@code p}.
         *
         * @param  p an element
         * @return the canonical element of the set containing {@code p}
         * @throws IllegalArgumentException unless {@code 0 <= p < n}
         */
        public int find(int p) {
            validate(p);
            while (p != parent[p])
                p = parent[p];
            return p;
        }

        /**
         * Returns true if the two elements are in the same set.
         *
         * @param  p one element
         * @param  q the other element
         * @return {@code true} if {@code p} and {@code q} are in the same set;
         *         {@code false} otherwise
         * @throws IllegalArgumentException unless
         *         both {@code 0 <= p < n} and {@code 0 <= q < n}
         * @deprecated Replace with two calls to {@link #find(int)}.
         */
        @Deprecated
        public boolean connected(int p, int q) {
            return find(p) == find(q);
        }

        // validate that p is a valid index
        private void validate(int p) {
            int n = parent.length;
            if (p < 0 || p >= n) {
                throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));
            }
        }

        /**
         * Merges the set containing element {@code p} with the set
         * containing element {@code q}.
         *
         * @param  p one element
         * @param  q the other element
         * @throws IllegalArgumentException unless
         *         both {@code 0 <= p < n} and {@code 0 <= q < n}
         */
        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;

            // make smaller root point to larger one
            if (size[rootP] < size[rootQ]) {
                parent[rootP] = rootQ;
                size[rootQ] += size[rootP];
            }
            else {
                parent[rootQ] = rootP;
                size[rootP] += size[rootQ];
            }
            count--;
        }
    }
}
```

### [685. Redundant Connection II (Hard)](https://leetcode.com/problems/redundant-connection-ii/description/)

```java
class Solution {
    public int[] findRedundantDirectedConnection(int[][] edges) {
        int n = edges.length;
        int[] parent = new int[n + 1];
        int[] first = null;
        int[] second = null;

        // Step 1: Check for a node with two parents
        for (int[] edge : edges) {
            int u = edge[0], v = edge[1];
            if (parent[v] != 0) { // dang bi 2 thg choc cung 1 luc
                first = new int[]{parent[v], v}; 
                second = new int[]{u, v};
                edge[1] = 0; // vo hieu hoa canh
            } else {
                parent[v] = u;
            }
        }

        // Step 2: Use Union-Find to detect cycles
        WeightedQuickUnionUF uf = new WeightedQuickUnionUF(n + 1);
        for (int[] edge : edges) {
            if (edge[1] == 0) continue; //vo hieu hoa thi bo qua
            if (uf.connected(edge[0], edge[1])) {
                // neu o nhung canh ko bi vo hieu hoa phat hien chu trinh
                return first == null ? edge : first; //tra ve first (thang bi 2 choc), neu first null thi tra ve thang dang bi cycle
            }
            uf.union(edge[0], edge[1]);
        }

        // Step 3: Return the second edge if no cycle exists
        return second; //
    }

    public class WeightedQuickUnionUF {
        private int[] parent;
        private int[] size;

        public WeightedQuickUnionUF(int n) {
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }

        public int find(int p) {
            while (p != parent[p]) {
                parent[p] = parent[parent[p]]; // Path compression
                p = parent[p];
            }
            return p;
        }

        public boolean connected(int p, int q) {
            return find(p) == find(q);
        }

        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;

            // Weighted union
            if (size[rootP] < size[rootQ]) {
                parent[rootP] = rootQ;
                size[rootQ] += size[rootP];
            } else {
                parent[rootQ] = rootP;
                size[rootP] += size[rootQ];
            }
        }
    }
}
```

### [1319. Number of Operations to Make Network Connected (Med)](https://leetcode.com/problems/number-of-operations-to-make-network-connected/description/)

```java
class Solution {
    public int makeConnected(int n, int[][] conn) {
        UF uf = new UF(n);
        int cnt = 0;

        for (int[] c : conn) {
            if (!uf.connected(c[0], c[1])) {
                uf.union(c[0], c[1]);
            } else cnt++;
        }
        System.out.println(cnt + " " + uf.count);

        return (cnt >= uf.count - 1) ? uf.count - 1 : -1;
    }

    public class UF {

        private int[] parent;  // parent[i] = parent of i
        private byte[] rank;   // rank[i] = rank of subtree rooted at i (never more than 31)
        public int count;     // number of components

        /**
        * Initializes an empty union-find data structure with
        * {@code n} elements {@code 0} through {@code n-1}.
        * Initially, each element is in its own set.
        *
        * @param  n the number of elements
        * @throws IllegalArgumentException if {@code n < 0}
        */
        public UF(int n) {
            if (n < 0) throw new IllegalArgumentException();
            count = n;
            parent = new int[n];
            rank = new byte[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                rank[i] = 0;
            }
        }

        /**
        * Returns the canonical element of the set containing element {@code p}.
        *
        * @param  p an element
        * @return the canonical element of the set containing {@code p}
        * @throws IllegalArgumentException unless {@code 0 <= p < n}
        */
        public int find(int p) {
            validate(p);
            while (p != parent[p]) {
                parent[p] = parent[parent[p]];    // path compression by halving
                p = parent[p];
            }
            return p;
        }

        /**
        * Returns the number of sets.
        *
        * @return the number of sets (between {@code 1} and {@code n})
        */
        public int count() {
            return count;
        }

        /**
        * Returns true if the two elements are in the same set.
        *
        * @param  p one element
        * @param  q the other element
        * @return {@code true} if {@code p} and {@code q} are in the same set;
        *         {@code false} otherwise
        * @throws IllegalArgumentException unless
        *         both {@code 0 <= p < n} and {@code 0 <= q < n}
        * @deprecated Replace with two calls to {@link #find(int)}.
        */
        @Deprecated
        public boolean connected(int p, int q) {
            return find(p) == find(q);
        }

        /**
        * Merges the set containing element {@code p} with the set
        * containing element {@code q}.
        *
        * @param  p one element
        * @param  q the other element
        * @throws IllegalArgumentException unless
        *         both {@code 0 <= p < n} and {@code 0 <= q < n}
        */
        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;

            // make root of smaller rank point to root of larger rank
            if      (rank[rootP] < rank[rootQ]) parent[rootP] = rootQ;
            else if (rank[rootP] > rank[rootQ]) parent[rootQ] = rootP;
            else {
                parent[rootQ] = rootP;
                rank[rootP]++;
            }
            count--;
        }

        // validate that p is a valid index
        private void validate(int p) {
            int n = parent.length;
            if (p < 0 || p >= n) {
                throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));
            }
        }
    }
}
```

### [1579. Remove Max Number of Edges to Keep Graph Fully Traversable](https://leetcode.com/problems/remove-max-number-of-edges-to-keep-graph-fully-traversable/description/)

```java
class Solution {
    public int maxNumEdgesToRemove(int n, int[][] edges) {
        UF a = new UF(n), b = new UF(n);

        Arrays.sort(edges, (e1, e2) -> Integer.compare(e2[0], e1[0]));

        int cnt = 0;
        for (int[] e : edges) {
            if (e[0] == 3 && !a.connected(e[1] - 1, e[2] - 1) 
            && !b.connected(e[1] - 1, e[2] - 1)) {
                a.union(e[1] - 1, e[2] - 1); b.union(e[1] - 1, e[2] - 1); cnt++;
            } else if (e[0] == 1 && !a.connected(e[1] - 1, e[2] - 1)) {
                a.union(e[1] - 1, e[2] - 1); cnt++;
            } else if (e[0] == 2 && !b.connected(e[1] - 1, e[2] - 1)) {
                b.union(e[1] - 1, e[2] - 1); cnt++;
            }
        }
        if (a.count() != 1 || b.count != 1) return -1;
        return edges.length - cnt;
    }

    public class UF {

        private int[] parent;  // parent[i] = parent of i
        private byte[] rank;   // rank[i] = rank of subtree rooted at i (never more than 31)
        private int count;     // number of components

        /**
        * Initializes an empty union-find data structure with
        * {@code n} elements {@code 0} through {@code n-1}.
        * Initially, each element is in its own set.
        *
        * @param  n the number of elements
        * @throws IllegalArgumentException if {@code n < 0}
        */
        public UF(int n) {
            if (n < 0) throw new IllegalArgumentException();
            count = n;
            parent = new int[n];
            rank = new byte[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                rank[i] = 0;
            }
        }

        /**
        * Returns the canonical element of the set containing element {@code p}.
        *
        * @param  p an element
        * @return the canonical element of the set containing {@code p}
        * @throws IllegalArgumentException unless {@code 0 <= p < n}
        */
        public int find(int p) {
            validate(p);
            while (p != parent[p]) {
                parent[p] = parent[parent[p]];    // path compression by halving
                p = parent[p];
            }
            return p;
        }

        /**
        * Returns the number of sets.
        *
        * @return the number of sets (between {@code 1} and {@code n})
        */
        public int count() {
            return count;
        }

        /**
        * Returns true if the two elements are in the same set.
        *
        * @param  p one element
        * @param  q the other element
        * @return {@code true} if {@code p} and {@code q} are in the same set;
        *         {@code false} otherwise
        * @throws IllegalArgumentException unless
        *         both {@code 0 <= p < n} and {@code 0 <= q < n}
        * @deprecated Replace with two calls to {@link #find(int)}.
        */
        @Deprecated
        public boolean connected(int p, int q) {
            return find(p) == find(q);
        }

        /**
        * Merges the set containing element {@code p} with the set
        * containing element {@code q}.
        *
        * @param  p one element
        * @param  q the other element
        * @throws IllegalArgumentException unless
        *         both {@code 0 <= p < n} and {@code 0 <= q < n}
        */
        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;

            // make root of smaller rank point to root of larger rank
            if      (rank[rootP] < rank[rootQ]) parent[rootP] = rootQ;
            else if (rank[rootP] > rank[rootQ]) parent[rootQ] = rootP;
            else {
                parent[rootQ] = rootP;
                rank[rootP]++;
            }
            count--;
        }

        // validate that p is a valid index
        private void validate(int p) {
            int n = parent.length;
            if (p < 0 || p >= n) {
                throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));
            }
        }
    }
}
```

