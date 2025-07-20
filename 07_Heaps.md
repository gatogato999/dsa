# 07 Heaps (Min-Heap, Max-Heap)

## Description

A heap is a specialized tree-based data structure that satisfies the heap property. In a heap, the parent node is compared to its children nodes and its value is either greater than or equal to (in a Max-Heap) or less than or equal to (in a Min-Heap) the values of its children. This property is applied recursively to all nodes in the tree.

Heaps are typically implemented as an array, taking advantage of the fact that a binary tree can be represented efficiently in an array. For a node at index `i`:
*   Its left child is at index `2i + 1`.
*   Its right child is at index `2i + 2`.
*   Its parent is at index `floor((i - 1) / 2)`.

There are two main types of heaps:

*   **Min-Heap**: The value of each node is less than or equal to the value of its children. The smallest element is always at the root.
*   **Max-Heap**: The value of each node is greater than or equal to the value of its children. The largest element is always at the root.

Heaps are not sorted, but they are 


partially ordered, which makes them very efficient for operations like finding the minimum/maximum element and extracting it.

## Mentality Before Solving

When considering a heap, think about:

1.  **Priority Queues**: Heaps are the most common and efficient way to implement a priority queue. If your problem requires always accessing the highest or lowest priority item (e.g., shortest path in Dijkstra's algorithm, event scheduling, task management), a heap is the ideal data structure.
2.  **Finding Min/Max Efficiently**: If you frequently need to find and/or remove the minimum (Min-Heap) or maximum (Max-Heap) element from a collection, a heap provides O(1) access to the root and O(log n) for removal and re-heapifying.
3.  **Heap Sort**: Heaps are the basis for Heap Sort, an efficient, in-place comparison-based sorting algorithm with O(n log n) time complexity.
4.  **Median Finding**: Heaps can be used to efficiently find the median of a stream of numbers by maintaining two heaps (a max-heap for the lower half and a min-heap for the upper half).
5.  **K-th Largest/Smallest Element**: Heaps are very useful for finding the K-th largest or smallest element in a collection efficiently.
6.  **Dynamic Data**: Heaps are dynamic structures that can efficiently handle insertions and deletions while maintaining the heap property.

## Code in JavaScript/Node.js

Implementing a heap from scratch involves managing the array representation and performing 'heapify' operations (sift-up and sift-down) to maintain the heap property after insertions and deletions. Here's a basic Min-Heap implementation:

```javascript
class MinHeap {
  constructor() {
    this.heap = [];
  }

  // Get the parent index
  getParentIndex(i) {
    return Math.floor((i - 1) / 2);
  }

  // Get left child index
  getLeftChildIndex(i) {
    return 2 * i + 1;
  }

  // Get right child index
  getRightChildIndex(i) {
    return 2 * i + 2;
  }

  // Check if a node has a parent
  hasParent(i) {
    return this.getParentIndex(i) >= 0;
  }

  // Check if a node has a left child
  hasLeftChild(i) {
    return this.getLeftChildIndex(i) < this.heap.length;
  }

  // Check if a node has a right child
  hasRightChild(i) {
    return this.getRightChildIndex(i) < this.heap.length;
  }

  // Get parent value
  getParent(i) {
    return this.heap[this.getParentIndex(i)];
  }

  // Get left child value
  getLeftChild(i) {
    return this.heap[this.getLeftChildIndex(i)];
  }

  // Get right child value
  getRightChild(i) {
    return this.heap[this.getRightChildIndex(i)];
  }

  // Swap two elements in the heap array
  swap(indexOne, indexTwo) {
    [this.heap[indexOne], this.heap[indexTwo]] = [
      this.heap[indexTwo],
      this.heap[indexOne],
    ];
  }

  // Get the minimum element (root)
  peek() {
    if (this.heap.length === 0) {
      return null;
    }
    return this.heap[0];
  }

  // Remove and return the minimum element
  extractMin() {
    if (this.heap.length === 0) {
      return null;
    }
    if (this.heap.length === 1) {
      return this.heap.pop();
    }

    const item = this.heap[0];
    this.heap[0] = this.heap.pop(); // Move last element to root
    this.heapifyDown(); // Restore heap property
    return item;
  }

  // Add a new element
  insert(item) {
    this.heap.push(item);
    this.heapifyUp(); // Restore heap property
  }

  // Restore heap property by sifting up (after insertion)
  heapifyUp() {
    let index = this.heap.length - 1;
    while (
      this.hasParent(index) &&
      this.getParent(index) > this.heap[index]
    ) {
      this.swap(this.getParentIndex(index), index);
      index = this.getParentIndex(index);
    }
  }

  // Restore heap property by sifting down (after extraction)
  heapifyDown() {
    let index = 0;
    while (this.hasLeftChild(index)) {
      let smallerChildIndex = this.getLeftChildIndex(index);
      if (
        this.hasRightChild(index) &&
        this.getRightChild(index) < this.getLeftChild(index)
      ) {
        smallerChildIndex = this.getRightChildIndex(index);
      }

      if (this.heap[index] < this.heap[smallerChildIndex]) {
        break; // Heap property satisfied
      } else {
        this.swap(index, smallerChildIndex);
      }
      index = smallerChildIndex;
    }
  }

  // Get the current size of the heap
  size() {
    return this.heap.length;
  }

  // Check if the heap is empty
  isEmpty() {
    return this.heap.length === 0;
  }
}

// Example Usage:
const minHeap = new MinHeap();
minHeap.insert(10);
minHeap.insert(4);
minHeap.insert(15);
minHeap.insert(20);
minHeap.insert(1);

console.log("Min-Heap elements (internal array, not sorted):");
console.log(minHeap.heap); // Output: [1, 4, 15, 20, 10] (example, actual order depends on heapify)

console.log("Min element:", minHeap.peek()); // Output: 1

console.log("Extracting min:", minHeap.extractMin()); // Output: 1
console.log("Min-Heap after extraction:", minHeap.heap); // Output: [4, 10, 15, 20] (example)

minHeap.insert(2);
console.log("Min-Heap after inserting 2:", minHeap.heap); // Output: [2, 10, 15, 20, 4] (example)
console.log("New min element:", minHeap.peek()); // Output: 2
```

To implement a Max-Heap, you would simply reverse the comparison logic in `heapifyUp` and `heapifyDown` (e.g., `this.getParent(index) < this.heap[index]` for `heapifyUp` and `this.heap[index] < this.heap[largerChildIndex]` for `heapifyDown`).

## Edge Cases and How to Solve Them

1.  **Empty Heap Operations**: Attempting to `peek()` or `extractMin()` (or `extractMax()`) from an empty heap should be handled gracefully. The solution is to check `if (this.heap.length === 0)` at the beginning of these methods and return `null` or throw an error.

    ```javascript
    peek() {
      if (this.heap.length === 0) {
        return null;
      }
      return this.heap[0];
    }
    ```

2.  **Heap with One Element**: When the heap contains only one element, `extractMin()` (or `extractMax()`) is a special case. The element is simply popped, and no `heapifyDown` is needed as there are no children to compare with. The provided `extractMin` handles this by checking `if (this.heap.length === 1)`.

3.  **Duplicate Values**: Heaps correctly handle duplicate values. The heap property (less than or equal to / greater than or equal to) allows duplicates to exist in the heap structure without violating the property.

4.  **Building a Heap from an Array**: If you have an existing array and want to convert it into a heap, you can do so efficiently in O(n) time. This is done by starting from the last non-leaf node and calling `heapifyDown` (or `heapifyUp` depending on the approach) on each node up to the root. This is more efficient than inserting each element one by one (which would be O(n log n)).

5.  **Performance Considerations**: While heap operations (insert, extractMin/Max) are O(log n), the constant factor can be larger than for other data structures. For very small datasets, a simple sorted array might perform better. However, for dynamic collections where min/max extraction is frequent, heaps are superior.

6.  **Memory Usage**: Heaps implemented with arrays are memory-efficient as they don't require extra pointers per node like linked lists or trees. The memory is contiguous, which can also benefit cache performance.

## References

1.  [GeeksforGeeks: Heap Data Structure](https://www.geeksforgeeks.org/heap-data-structure/)
2.  [Wikipedia: Heap (data structure)](https://en.wikipedia.org/wiki/Heap_(data_structure))
3.  [freeCodeCamp: Heaps in JavaScript](https://www.freecodecamp.org/news/heaps-in-javascript/)


