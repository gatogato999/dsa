# 10 Segment Trees

## Description

A Segment Tree is a tree data structure used for storing information about intervals or segments. It allows querying on segments (e.g., sum, minimum, maximum) and updating elements in an array (or segment) in logarithmic time. Each node in the segment tree represents an interval, and the root node represents the entire array. The leaves of the tree represent individual elements of the array.

Key characteristics of Segment Trees:

*   **Range Queries**: Efficiently answer queries about a range (e.g., sum of elements in a range, minimum/maximum element in a range).
*   **Point Updates**: Efficiently update a single element in the underlying array.
*   **Logarithmic Time Complexity**: Both query and update operations take O(log n) time, where n is the size of the array.
*   **Space Complexity**: Requires O(n) space, typically around 2n to 4n depending on implementation details.

Segment trees are particularly useful in competitive programming and scenarios where frequent range queries and point updates are needed on a static array.

## Mentality Before Solving

Consider using a Segment Tree when:

1.  **Range Queries and Point Updates**: The problem involves a static array where you need to perform many queries over arbitrary ranges (e.g., "what is the sum of elements from index `i` to `j`?") and also frequently update individual elements.
2.  **Associative Operations**: The operation you need to perform on a range (sum, min, max, GCD, etc.) must be associative. This means `(a op b) op c` is equivalent to `a op (b op c)`. This property allows combining results from sub-intervals.
3.  **Logarithmic Performance Requirement**: If O(n) or O(sqrt(n)) solutions for range queries/updates are too slow for the given constraints, and you need O(log n) performance.
4.  **Offline vs. Online Queries**: Segment trees are suitable for online queries, meaning queries can be answered immediately without knowing all queries beforehand. If all queries are known in advance, other techniques might be applicable.
5.  **Variations**: Be aware that segment trees can be extended for more complex operations, such as range updates (lazy propagation) or 2D queries.

## Code in JavaScript/Node.js

Here's a basic Segment Tree implementation for range sum queries and point updates:

```javascript
class SegmentTree {
  constructor(arr, operation = (a, b) => a + b, initialValue = 0) {
    this.arr = arr; // The original array
    this.operation = operation; // e.g., (a, b) => a + b for sum, (a, b) => Math.min(a, b) for min
    this.initialValue = initialValue; // e.g., 0 for sum, Infinity for min
    this.tree = new Array(4 * arr.length); // Max size of segment tree array
    this.build(0, 0, arr.length - 1); // Build the tree from the root
  }

  // Build the segment tree recursively
  // nodeIndex: current node's index in the tree array
  // start: start index of the segment represented by current node
  // end: end index of the segment represented by current node
  build(nodeIndex, start, end) {
    if (start === end) {
      // Leaf node: stores the actual array element
      this.tree[nodeIndex] = this.arr[start];
    } else {
      const mid = Math.floor((start + end) / 2);
      // Recursively build left and right children
      this.build(2 * nodeIndex + 1, start, mid); // Left child
      this.build(2 * nodeIndex + 2, mid + 1, end); // Right child
      // Combine results from children
      this.tree[nodeIndex] = this.operation(
        this.tree[2 * nodeIndex + 1],
        this.tree[2 * nodeIndex + 2]
      );
    }
  }

  // Update a single element in the original array and propagate changes to the tree
  // nodeIndex: current node's index in the tree array
  // start: start index of the segment represented by current node
  // end: end index of the segment represented by current node
  // idx: index of the element to update in the original array
  // val: new value for the element
  update(nodeIndex, start, end, idx, val) {
    if (start === end) {
      // Leaf node: update the array and tree node
      this.arr[idx] = val;
      this.tree[nodeIndex] = val;
    } else {
      const mid = Math.floor((start + end) / 2);
      if (start <= idx && idx <= mid) {
        // If idx is in the left child's range, update left child
        this.update(2 * nodeIndex + 1, start, mid, idx, val);
      } else {
        // If idx is in the right child's range, update right child
        this.update(2 * nodeIndex + 2, mid + 1, end, idx, val);
      }
      // Update current node based on children's new values
      this.tree[nodeIndex] = this.operation(
        this.tree[2 * nodeIndex + 1],
        this.tree[2 * nodeIndex + 2]
      );
    }
  }

  // Query for a range
  // nodeIndex: current node's index in the tree array
  // start: start index of the segment represented by current node
  // end: end index of the segment represented by current node
  // queryL: left bound of the query range
  // queryR: right bound of the query range
  query(nodeIndex, start, end, queryL, queryR) {
    // Case 1: Current segment is completely outside the query range
    if (queryR < start || end < queryL) {
      return this.initialValue; // Return identity element for the operation
    }

    // Case 2: Current segment is completely inside the query range
    if (queryL <= start && end <= queryR) {
      return this.tree[nodeIndex];
    }

    // Case 3: Current segment partially overlaps with the query range
    const mid = Math.floor((start + end) / 2);
    const p1 = this.query(2 * nodeIndex + 1, start, mid, queryL, queryR);
    const p2 = this.query(2 * nodeIndex + 2, mid + 1, end, queryL, queryR);
    return this.operation(p1, p2);
  }

  // Public methods for convenience
  updateValue(idx, val) {
    this.update(0, 0, this.arr.length - 1, idx, val);
  }

  queryRange(queryL, queryR) {
    return this.query(0, 0, this.arr.length - 1, queryL, queryR);
  }
}

// Example Usage (Range Sum Query):
const numbers = [1, 3, 5, 7, 9, 11];
const sumSegmentTree = new SegmentTree(numbers, (a, b) => a + b, 0);

console.log("Initial array:", numbers);
console.log("Sum of range [1, 4]:", sumSegmentTree.queryRange(1, 4)); // Expected: 3 + 5 + 7 + 9 = 24

sumSegmentTree.updateValue(2, 10); // Update element at index 2 from 5 to 10
console.log("Array after update at index 2:", sumSegmentTree.arr);
console.log("Sum of range [1, 4] after update:", sumSegmentTree.queryRange(1, 4)); // Expected: 3 + 10 + 7 + 9 = 29

// Example Usage (Range Minimum Query):
const values = [5, 2, 8, 1, 9, 3];
const minSegmentTree = new SegmentTree(values, (a, b) => Math.min(a, b), Infinity);

console.log("\nInitial array for min query:", values);
console.log("Min of range [0, 5]:", minSegmentTree.queryRange(0, 5)); // Expected: 1
console.log("Min of range [1, 3]:", minSegmentTree.queryRange(1, 3)); // Expected: 1

minSegmentTree.updateValue(3, 0); // Update element at index 3 from 1 to 0
console.log("Array after update at index 3:", minSegmentTree.arr);
console.log("Min of range [1, 3] after update:", minSegmentTree.queryRange(1, 3)); // Expected: 0
```

## Edge Cases and How to Solve Them

1.  **Empty Array**: If the input array is empty, the segment tree cannot be built. The constructor should handle this by either throwing an error or initializing an empty tree and preventing further operations.

    ```javascript
    constructor(arr, operation = (a, b) => a + b, initialValue = 0) {
      if (arr.length === 0) {
        console.warn("Cannot build Segment Tree from an empty array.");
        this.arr = [];
        this.tree = [];
        this.size = 0;
        return;
      }
      // ... rest of constructor
    }
    ```

2.  **Invalid Query Ranges**: Queries with `queryL` or `queryR` outside the valid bounds of the original array (`0` to `arr.length - 1`) should be handled. The `query` method already returns `initialValue` if the segment is completely outside the query range, which implicitly handles this for non-overlapping invalid ranges. For partially overlapping invalid ranges, the `query` method will still work correctly by only considering the valid overlapping parts.

3.  **Point Update on Invalid Index**: Attempting to update an element at an index `idx` that is outside the array bounds. The `update` method should validate `idx`.

    ```javascript
    updateValue(idx, val) {
      if (idx < 0 || idx >= this.arr.length) {
        console.error("Invalid index for update.");
        return;
      }
      this.update(0, 0, this.arr.length - 1, idx, val);
    }
    ```

4.  **Non-Associative Operations**: Segment trees are designed for associative operations. If the operation is not associative (e.g., subtraction, division, or finding the mode), a segment tree cannot be directly applied. You would need a different data structure or a modified approach.

5.  **Range Updates (Lazy Propagation)**: The provided code only handles point updates. If you need to update an entire range of elements efficiently (e.g., add a value to all elements from index `i` to `j`), you need to implement **lazy propagation**. This involves storing pending updates in internal nodes and applying them only when necessary (i.e., when a query or update operation reaches that node or its children). This significantly increases the complexity of the `update` and `query` methods.

6.  **Memory Usage**: While O(n) space, for very large `n`, `4 * n` can still be a significant amount of memory. Be mindful of memory constraints for extremely large arrays.

7.  **Floating Point Precision**: When dealing with floating-point numbers and sum operations, be aware of potential precision issues. This is a general floating-point issue, not specific to segment trees, but it's worth noting.

## References

1.  [GeeksforGeeks: Segment Tree | Set 1 (Sum of given range)](https://www.geeksforgeeks.org/segment-tree-set-1-sum-of-given-range/)
2.  [Wikipedia: Segment tree](https://en.wikipedia.org/wiki/Segment_tree)
3.  [TopCoder: A tutorial on Segment Trees](https://www.topcoder.com/thrive/articles/A%20tutorial%20on%20Segment%20Trees)
4.  [CP-Algorithms: Segment Tree](https://cp-algorithms.com/data_structures/segment_tree.html)


