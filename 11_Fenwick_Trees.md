# 11 Fenwick Trees (Binary Indexed Trees)

## Description

A Fenwick Tree, also known as a Binary Indexed Tree (BIT), is a data structure that can efficiently update elements and calculate prefix sums in a table of numbers. It achieves this by representing numbers as a tree structure, where each node stores the sum of a range of elements in the original array. Unlike a segment tree, a Fenwick tree is simpler to implement and uses less memory, but it is limited to prefix sum queries and point updates.

Key characteristics of Fenwick Trees:

*   **Prefix Sum Queries**: Efficiently calculate the sum of elements from the beginning of the array up to a given index (prefix sum).
*   **Point Updates**: Efficiently update a single element in the underlying array.
*   **Logarithmic Time Complexity**: Both query and update operations take O(log n) time, where n is the size of the array.
*   **Space Complexity**: Requires O(n) space, similar to the original array.
*   **Simpler Implementation**: Generally simpler to implement than segment trees.

The core idea behind Fenwick trees is to use the binary representation of an index to determine which elements contribute to its prefix sum and which nodes need to be updated when an element changes. The `lowbit` function (or `i & -i`) is crucial, as it isolates the rightmost set bit, which corresponds to the size of the range covered by a node.

## Mentality Before Solving

Consider using a Fenwick Tree when:

1.  **Prefix Sums and Point Updates**: The problem specifically requires calculating prefix sums (or sums of arbitrary ranges, which can be derived from prefix sums: `sum(i, j) = prefix_sum(j) - prefix_sum(i-1)`) and performing point updates on a static array.
2.  **Logarithmic Performance Requirement**: Similar to segment trees, if O(n) solutions for these operations are too slow, and you need O(log n) performance.
3.  **Simplicity and Memory Efficiency**: If the problem can be solved with prefix sums and point updates, a Fenwick tree is often preferred over a segment tree due to its simpler implementation and lower constant factor for memory and time.
4.  **One-Dimensional Data**: Fenwick trees are primarily used for one-dimensional arrays. While extensions to 2D exist, they become more complex.
5.  **Associative Operations**: Like segment trees, the operation (sum, min, max, etc.) must be associative. However, Fenwick trees are most commonly used for sum operations. For min/max queries, segment trees are generally more versatile.

## Code in JavaScript/Node.js

Here's a basic Fenwick Tree implementation for sum queries and point updates:

```javascript
class FenwickTree {
  constructor(size) {
    this.tree = new Array(size + 1).fill(0); // 1-indexed tree
    this.size = size;
  }

  // Helper function to get the last set bit (lowbit)
  // This is equivalent to `i & (-i)`
  _lowbit(i) {
    return i & -i;
  }

  // Update the value at a given index (point update)
  // Adds `delta` to the element at `index`
  update(index, delta) {
    // Fenwick tree is 1-indexed, so adjust index
    index++; 
    while (index <= this.size) {
      this.tree[index] += delta;
      index += this._lowbit(index);
    }
  }

  // Get the prefix sum up to a given index
  // Returns sum of elements from index 0 to `index` (inclusive)
  query(index) {
    // Fenwick tree is 1-indexed, so adjust index
    index++;
    let sum = 0;
    while (index > 0) {
      sum += this.tree[index];
      index -= this._lowbit(index);
    }
    return sum;
  }

  // Get the sum of elements in a range [left, right]
  // This is derived from prefix sums: sum(left, right) = query(right) - query(left - 1)
  queryRange(left, right) {
    if (left > right) {
      return 0;
    }
    return this.query(right) - this.query(left - 1);
  }

  // Build the Fenwick tree from an initial array
  // This is more efficient than calling update for each element if the array is known upfront
  build(arr) {
    for (let i = 0; i < arr.length; i++) {
      this.update(i, arr[i]);
    }
  }
}

// Example Usage:
const initialArray = [1, 2, 3, 4, 5];
const ft = new FenwickTree(initialArray.length);
ft.build(initialArray);

console.log("Initial array:", initialArray);
console.log("Prefix sum up to index 2 (0-indexed, i.e., sum of 1, 2, 3):");
console.log(ft.query(2)); // Output: 6 (1+2+3)

console.log("Sum of range [1, 3] (0-indexed, i.e., sum of 2, 3, 4):");
console.log(ft.queryRange(1, 3)); // Output: 9 (2+3+4)

ft.update(2, 10); // Add 10 to element at index 2 (original value 3 becomes 13)
console.log("Array conceptually after update at index 2 (value 3 becomes 13):");
// The internal array is not directly visible, but sums reflect the change

console.log("Prefix sum up to index 2 after update:");
console.log(ft.query(2)); // Output: 16 (1+2+13)

console.log("Sum of range [1, 3] after update:");
console.log(ft.queryRange(1, 3)); // Output: 19 (2+13+4)
```

## Edge Cases and How to Solve Them

1.  **0-indexed vs. 1-indexed**: Fenwick trees are typically implemented as 1-indexed arrays for simpler `_lowbit` calculations. If your problem uses 0-indexed arrays, you need to consistently convert indices (e.g., `index + 1` for updates and queries, and `index - 1` for results if needed). The provided code handles this by adding 1 to the input `index`.

2.  **Empty Array**: If the initial array is empty, the Fenwick tree should be initialized with size 0. Operations on it should return 0 or handle gracefully.

    ```javascript
    constructor(size) {
      if (size < 0) {
        throw new Error("Size cannot be negative.");
      }
      this.tree = new Array(size + 1).fill(0);
      this.size = size;
    }
    // query and update methods will naturally handle size 0 correctly
    ```

3.  **Negative Values**: Fenwick trees can handle negative values in the array, as addition and subtraction operations work correctly with them. The prefix sums will simply reflect the sum of positive and negative numbers.

4.  **Invalid Indices for Update/Query**: Attempting to update or query an index outside the valid range (`0` to `size - 1` for 0-indexed arrays). The `update` and `query` methods should validate the input `index`.

    ```javascript
    update(index, delta) {
      if (index < 0 || index >= this.size) {
        console.error("Invalid index for update.");
        return;
      }
      // ... rest of update logic
    }

    query(index) {
      if (index < -1 || index >= this.size) { // -1 is valid for queryRange(0, x)
        console.error("Invalid index for query.");
        return 0; // Or throw error
      }
      // ... rest of query logic
    }
    ```

5.  **Range Queries with `left > right`**: The `queryRange(left, right)` method should handle cases where `left` is greater than `right` (e.g., by returning 0 or throwing an error). The provided code returns 0.

6.  **Non-Sum Operations**: While Fenwick trees are primarily for sums, they can be adapted for other associative operations like XOR sums. However, they are generally not suitable for operations like minimum or maximum queries, because removing an element (which is implicitly done when updating) cannot be easily undone to find the next minimum/maximum. For min/max range queries, Segment Trees are more appropriate.

## References

1.  [GeeksforGeeks: Fenwick Tree (Binary Indexed Tree)](https://www.geeksforgeeks.org/fenwick-tree-binary-indexed-tree/)
2.  [Wikipedia: Fenwick tree](https://en.wikipedia.org/wiki/Fenwick_tree)
3.  [CP-Algorithms: Fenwick tree](https://cp-algorithms.com/data_structures/fenwick.html)


