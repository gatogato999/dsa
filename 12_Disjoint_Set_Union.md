# 12 Disjoint Set Union (DSU)

## Description

The Disjoint Set Union (DSU) data structure, also known as Union-Find, is a data structure that keeps track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets. It provides two primary operations:

*   **Find (or FindSet)**: Determines which subset a particular element belongs to. It returns a 


representative (usually the root) of the set containing the element.
*   **Union (or UnionSets)**: Merges two subsets into a single subset. It takes two elements and unites the sets containing them.

DSU also often includes an `isSameSet` operation to check if two elements belong to the same set.

To optimize these operations, two techniques are commonly used:

*   **Path Compression (for Find)**: During a `Find` operation, every node on the path from the given element to the root is re-pointed directly to the root. This flattens the tree, making future `Find` operations faster.
*   **Union by Rank/Size (for Union)**: When uniting two sets, the smaller tree is made a child of the root of the larger tree. This helps to keep the trees relatively flat, minimizing their height and thus the time complexity of `Find` operations.

With both optimizations, the amortized time complexity for `Find` and `Union` operations is nearly constant, specifically O(α(n)), where α is the inverse Ackermann function, which grows extremely slowly and is practically less than 5 for any realistic input size `n`.

## Mentality Before Solving

Consider using a Disjoint Set Union data structure when:

1.  **Grouping Elements**: The problem involves grouping elements into disjoint sets and performing operations to merge these sets or check if two elements belong to the same set. Examples include connected components in a graph, network connectivity, or clustering problems.
2.  **Dynamic Connectivity**: When you need to efficiently track connectivity or equivalence relations among a collection of elements that change over time (i.e., new connections are formed).
3.  **Graph Problems**: DSU is a fundamental component in several graph algorithms, most notably Kruskal's algorithm for finding the Minimum Spanning Tree (MST) and for detecting cycles in a graph.
4.  **Optimizing Union/Find Operations**: If a naive approach of iterating through sets would be too slow (e.g., O(N) for each union/find), DSU provides a highly optimized solution.

## Code in JavaScript/Node.js

Here's an implementation of DSU with Path Compression and Union by Rank:

```javascript
class DisjointSetUnion {
  constructor(n) {
    // Parent array: parent[i] stores the parent of element i
    // Initially, each element is its own parent (forms its own set)
    this.parent = Array.from({ length: n }, (_, i) => i);
    // Rank array: rank[i] stores the rank (approximate height) of the tree rooted at i
    // Used for union by rank optimization
    this.rank = Array(n).fill(0);
    // Number of disjoint sets
    this.numSets = n;
  }

  // Find operation with Path Compression
  // Returns the representative (root) of the set containing element i
  find(i) {
    if (this.parent[i] === i) {
      return i;
    }
    // Path compression: set parent[i] directly to the root
    this.parent[i] = this.find(this.parent[i]);
    return this.parent[i];
  }

  // Union operation with Union by Rank
  // Merges the sets containing elements i and j
  union(i, j) {
    const rootI = this.find(i);
    const rootJ = this.find(j);

    if (rootI !== rootJ) {
      // If they are already in the same set, do nothing
      // Union by rank: attach smaller rank tree under root of higher rank tree
      if (this.rank[rootI] < this.rank[rootJ]) {
        this.parent[rootI] = rootJ;
      } else if (this.rank[rootI] > this.rank[rootJ]) {
        this.parent[rootJ] = rootI;
      } else {
        // If ranks are same, pick one as root and increment its rank
        this.parent[rootJ] = rootI;
        this.rank[rootI]++;
      }
      this.numSets--; // Decrement the number of disjoint sets
      return true; // Sets were merged
    }
    return false; // Sets were already the same
  }

  // Check if two elements are in the same set
  isSameSet(i, j) {
    return this.find(i) === this.find(j);
  }

  // Get the total number of disjoint sets
  countSets() {
    return this.numSets;
  }
}

// Example Usage:
const dsu = new DisjointSetUnion(5); // Elements 0, 1, 2, 3, 4

console.log("Initial sets:", dsu.parent); // Output: [0, 1, 2, 3, 4]
console.log("Number of sets:", dsu.countSets()); // Output: 5

console.log("Union(0, 1):");
dsu.union(0, 1);
console.log("Parent array:", dsu.parent);
console.log("Number of sets:", dsu.countSets()); // Output: 4

console.log("Union(2, 3):");
dsu.union(2, 3);
console.log("Parent array:", dsu.parent);
console.log("Number of sets:", dsu.countSets()); // Output: 3

console.log("isSameSet(0, 1):");
console.log(dsu.isSameSet(0, 1)); // Output: true
console.log("isSameSet(0, 2):");
console.log(dsu.isSameSet(0, 2)); // Output: false

console.log("Union(0, 2):");
dsu.union(0, 2);
console.log("Parent array:", dsu.parent);
console.log("Number of sets:", dsu.countSets()); // Output: 2

console.log("isSameSet(1, 3):");
console.log(dsu.isSameSet(1, 3)); // Output: true (because 0 and 1 are united, and 0 and 2 are united, and 2 and 3 are united)

// After multiple finds, path compression will flatten the trees
dsu.find(4);
dsu.find(3);
console.log("Parent array after finds:", dsu.parent); // Notice how parents might change due to path compression
```

## Edge Cases and How to Solve Them

1.  **Invalid Element Indices**: The DSU implementation assumes that element indices are valid (i.e., within the range `0` to `n-1`, where `n` is the size provided to the constructor). Passing negative indices or indices greater than or equal to `n` will result in out-of-bounds array access errors.

    *   **Solution**: Add input validation to `find` and `union` methods to check if `i` and `j` are within the valid range.

    ```javascript
    find(i) {
      if (i < 0 || i >= this.parent.length) {
        throw new Error("Invalid element index.");
      }
      // ... rest of find logic
    }
    ```

2.  **Union of Elements Already in the Same Set**: The `union` operation should gracefully handle cases where the two elements `i` and `j` are already in the same set. In this scenario, no actual merge is needed, and the `numSets` count should not be decremented. The provided code correctly handles this by checking `if (rootI !== rootJ)`.

3.  **Initialization with Zero Elements**: If `n` (the number of elements) is 0 or negative during construction, the DSU should handle this. A `n=0` DSU would have no elements and `numSets` would be 0. Negative `n` should probably throw an error.

    ```javascript
    constructor(n) {
      if (n < 0) {
        throw new Error("Number of elements cannot be negative.");
      }
      this.parent = Array.from({ length: n }, (_, i) => i);
      this.rank = Array(n).fill(0);
      this.numSets = n;
    }
    ```

4.  **Performance with Only One Optimization**: While path compression and union by rank/size are both very effective, using only one of them still provides significant performance improvements over a naive DSU. However, using both together yields the nearly constant amortized time complexity. If one optimization is omitted, the worst-case time complexity can degrade (e.g., to O(log n) with only union by rank, or O(n) with only path compression in specific adversarial cases).

5.  **Large Number of Elements**: For extremely large `n`, the `parent` and `rank` arrays can consume significant memory. However, for typical competitive programming constraints (up to 10^5 or 10^6 elements), this is usually not an issue.

## References

1.  [GeeksforGeeks: Disjoint Set Union (Union-Find) | Set 1 (Introduction and Find Operation)](https://www.geeksforgeeks.org/disjoint-set-union-union-find/)
2.  [Wikipedia: Disjoint-set data structure](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)
3.  [CP-Algorithms: Disjoint Set Union](https://cp-algorithms.com/data_structures/disjoint_set_union.html)
4.  [Visualizing Algorithms: Union Find](https://www.cs.usfca.edu/~galles/visualization/DisjointSets.html)


