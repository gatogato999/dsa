# 01 Searching Algorithms

## Description

Searching algorithms are algorithms designed to retrieve information stored within some data structure, or calculated in the search space. The efficiency of a search algorithm is often measured by the number of comparisons or operations it performs to find the target element.

We will cover two fundamental searching algorithms:

*   **Linear Search (Sequential Search)**: A straightforward method that checks each element in the list sequentially until the desired element is found or the list ends.
*   **Binary Search**: A much more efficient algorithm that works on sorted arrays. It repeatedly divides the search interval in half.

## Mentality Before Solving

When considering a search algorithm, ask yourself:

1.  **Data Structure**: What data structure is the data stored in? Linear search works on any linear data structure (arrays, linked lists), while binary search requires a sorted array.
2.  **Data Order**: Is the data sorted? If not, and you need efficient searching, consider sorting it first (if it's a static dataset) or using a hash table for O(1) average time complexity.
3.  **Frequency of Searches**: How often will you be searching? For infrequent searches on small, unsorted data, linear search might be acceptable. For frequent searches on large datasets, binary search or hash tables are preferred.
4.  **Space Constraints**: Both linear and binary search are in-place algorithms, requiring minimal extra space.

## Code in JavaScript/Node.js

### 1. Linear Search

*   **Description**: Iterates through each element of the array from the beginning until the target element is found. If the element is found, its index is returned; otherwise, -1 is returned.
*   **Time Complexity**: O(n) in worst and average case, O(1) in best case (element is at the beginning).
*   **Space Complexity**: O(1).

```javascript
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) {
      return i; // Element found at index i
    }
  }
  return -1; // Element not found
}

// Example:
// console.log("Linear Search (found):");
// console.log(linearSearch([10, 20, 30, 40, 50], 30)); // Output: 2
// console.log("Linear Search (not found):");
// console.log(linearSearch([10, 20, 30, 40, 50], 60)); // Output: -1
```

### 2. Binary Search

*   **Description**: A highly efficient search algorithm for sorted arrays. It works by repeatedly dividing the search interval in half. If the value of the search key is less than the item in the middle of the interval, narrow the interval to the lower half. Otherwise, narrow it to the upper half. Repeatedly check until the value is found or the interval is empty.
*   **Time Complexity**: O(log n) in worst, average, and best case.
*   **Space Complexity**: O(1) (iterative) or O(log n) (recursive due to call stack).

```javascript
// Iterative Binary Search
function binarySearchIterative(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      return mid; // Element found
    } else if (arr[mid] < target) {
      left = mid + 1; // Target is in the right half
    } else {
      right = mid - 1; // Target is in the left half
    }
  }
  return -1; // Element not found
}

// Recursive Binary Search
function binarySearchRecursive(arr, target, left, right) {
  if (left > right) {
    return -1; // Base case: element not found
  }

  const mid = Math.floor((left + right) / 2);

  if (arr[mid] === target) {
    return mid; // Element found
  } else if (arr[mid] < target) {
    return binarySearchRecursive(arr, target, mid + 1, right); // Search right half
  } else {
    return binarySearchRecursive(arr, target, left, mid - 1); // Search left half
  }
}

// Example:
// const sortedArr = [10, 20, 30, 40, 50, 60, 70, 80, 90];
// console.log("\nBinary Search Iterative (found):");
// console.log(binarySearchIterative(sortedArr, 50)); // Output: 4
// console.log("Binary Search Iterative (not found):");
// console.log(binarySearchIterative(sortedArr, 35)); // Output: -1

// console.log("\nBinary Search Recursive (found):");
// console.log(binarySearchRecursive(sortedArr, 70, 0, sortedArr.length - 1)); // Output: 6
// console.log("Binary Search Recursive (not found):");
// console.log(binarySearchRecursive(sortedArr, 5, 0, sortedArr.length - 1)); // Output: -1
```

## Edge Cases and How to Solve Them

### Linear Search Edge Cases

1.  **Empty Array**: If the input array is empty, the loop will not run, and `linearSearch` will correctly return -1.

    ```javascript
    console.log(linearSearch([], 5)); // Output: -1
    ```

2.  **Element at First/Last Position**: The algorithm correctly handles cases where the target element is at the very beginning or very end of the array.

3.  **Multiple Occurrences**: If the target element appears multiple times, `linearSearch` will return the index of the *first* occurrence. If you need all occurrences, you would modify the function to collect all matching indices.

### Binary Search Edge Cases

1.  **Unsorted Array**: Binary search *requires* the input array to be sorted. If the array is unsorted, the algorithm will produce incorrect results or fail to find the element even if it exists.

    *   **Solution**: Ensure the array is sorted before applying binary search. If the array is dynamic and frequently changes, consider using a data structure that maintains order (like a Binary Search Tree) or a hash table.

2.  **Empty Array**: If the input array is empty, `left` will be 0 and `right` will be -1 (for `arr.length - 1`), so `left <= right` will be false, and the loop won't run. The function will correctly return -1.

    ```javascript
    console.log(binarySearchIterative([], 5)); // Output: -1
    console.log(binarySearchRecursive([], 5, 0, -1)); // Output: -1
    ```

3.  **Array with One Element**: The algorithm correctly handles an array with a single element.

    ```javascript
    console.log(binarySearchIterative([42], 42)); // Output: 0
    console.log(binarySearchIterative([42], 10)); // Output: -1
    ```

4.  **Target Element Not Present**: If the target element is not in the array, the `while` loop condition `left <= right` will eventually become false (or `left > right` in recursive base case), and the function will correctly return -1.

5.  **Target Element at Boundaries**: The algorithm correctly finds elements at the very beginning (`arr[0]`) or very end (`arr[arr.length - 1]`) of the array.

6.  **Integer Overflow for `mid` (in other languages)**: In some languages (like C++ or Java), `(left + right) / 2` can lead to integer overflow if `left` and `right` are very large. A safer calculation is `left + (right - left) / 2`. JavaScript numbers are floating-point, so this is less of a concern, but it's good practice.

7.  **Multiple Occurrences**: Binary search will find *an* index of the target element if it exists. It does not guarantee finding the first or last occurrence if there are duplicates. To find the first or last occurrence, you would modify the search logic slightly after finding a match to continue searching in the appropriate half.

    ```javascript
    // To find the first occurrence of a target in a sorted array with duplicates
    function binarySearchFirstOccurrence(arr, target) {
      let left = 0;
      let right = arr.length - 1;
      let result = -1;

      while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        if (arr[mid] === target) {
          result = mid; // Found a potential match, try to find an earlier one
          right = mid - 1;
        } else if (arr[mid] < target) {
          left = mid + 1;
        } else {
          right = mid - 1;
        }
      }
      return result;
    }
    // Example: binarySearchFirstOccurrence([1, 2, 2, 2, 3], 2) // Output: 1
    ```

## References

1.  [GeeksforGeeks: Searching Algorithms](https://www.geeksforgeeks.org/searching-algorithms/)
2.  [Wikipedia: Search algorithm](https://en.wikipedia.org/wiki/Search_algorithm)
3.  [GeeksforGeeks: Linear Search](https://www.geeksforgeeks.org/linear-search/)
4.  [GeeksforGeeks: Binary Search](https://www.geeksforgeeks.org/binary-search/)


