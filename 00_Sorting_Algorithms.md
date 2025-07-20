# 00 Sorting Algorithms

## Description

Sorting algorithms are algorithms that put elements of a list in a certain order. The most frequently used orders are numerical order and lexicographical order. Efficient sorting is important for optimizing the efficiency of other algorithms (such as search and merge algorithms) that require input data to be in sorted lists. Sorting is also often useful for canonicalizing data and for producing human-readable output.

Sorting algorithms can be categorized based on several factors:

*   **Time Complexity**: How the execution time grows with the input size (e.g., O(n^2), O(n log n)).
*   **Space Complexity**: How much auxiliary space the algorithm requires (e.g., O(1) for in-place sorts, O(n) for sorts requiring extra arrays).
*   **Stability**: A sorting algorithm is stable if it preserves the relative order of equal elements. For example, if two elements `A` and `B` are equal and `A` appears before `B` in the original list, then in a stable sort, `A` will still appear before `B` in the sorted list.
*   **In-place**: An algorithm is in-place if it transforms input using only a small, constant amount of extra space.
*   **Comparison-based**: Algorithms that sort by comparing elements (e.g., Bubble Sort, Merge Sort, Quick Sort). These have a theoretical lower bound of O(n log n).
*   **Non-comparison-based**: Algorithms that do not rely on comparisons (e.g., Counting Sort, Radix Sort). These can achieve better than O(n log n) time complexity for specific data distributions.

Here, we will cover some of the most popular sorting algorithms:

*   **Bubble Sort**
*   **Insertion Sort**
*   **Selection Sort**
*   **Merge Sort**
*   **Quick Sort**
*   **Counting Sort**
*   **Radix Sort**

## Mentality Before Solving

When faced with a problem that requires sorting, consider the following:

1.  **Data Size**: For small datasets, simple O(n^2) algorithms might be sufficient and easier to implement. For large datasets, O(n log n) algorithms are essential.
2.  **Data Characteristics**: Are the numbers mostly sorted? Are there many duplicates? Is the range of values small? These factors can influence the choice of algorithm (e.g., Insertion Sort is good for nearly sorted data; Counting Sort/Radix Sort for data with a small range or specific digit properties).
3.  **Stability Requirement**: Does the relative order of equal elements matter? If so, choose a stable sort (e.g., Merge Sort, Insertion Sort, Bubble Sort, Counting Sort).
4.  **Space Constraints**: Is auxiliary space a concern? If memory is limited, prefer in-place algorithms (e.g., Quick Sort, Heap Sort, Insertion Sort, Selection Sort).
5.  **Worst-Case Performance**: For critical applications, consider the worst-case time complexity. Quick Sort, while generally fast, has a worst-case O(n^2) (though rare with good pivot selection). Merge Sort consistently offers O(n log n).
6.  **Implementation Complexity**: Some algorithms are simpler to implement than others. For a quick solution, a simpler algorithm might be preferred if performance is not strictly critical.

## Code in JavaScript/Node.js

### 1. Bubble Sort

*   **Description**: Repeatedly steps through the list, compares adjacent elements and swaps them if they are in the wrong order. The pass through the list is repeated until no swaps are needed, which indicates that the list is sorted.
*   **Time Complexity**: O(n^2) in worst and average case, O(n) in best case (already sorted).
*   **Space Complexity**: O(1) (in-place).
*   **Stability**: Stable.

```javascript
function bubbleSort(arr) {
  const n = arr.length;
  let swapped;
  for (let i = 0; i < n - 1; i++) {
    swapped = false;
    for (let j = 0; j < n - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        // Swap arr[j] and arr[j+1]
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }
    // If no two elements were swapped by inner loop, then break
    if (!swapped) {
      break;
    }
  }
  return arr;
}

// Example:
// console.log("Bubble Sort:", bubbleSort([64, 34, 25, 12, 22, 11, 90])); // [11, 12, 22, 25, 34, 64, 90]
```

### 2. Insertion Sort

*   **Description**: Builds the final sorted array (or list) one item at a time. It iterates through the input elements and removes one element per iteration, finds the place within the sorted list/array, and inserts it there. It is efficient for small data sets and mostly sorted data.
*   **Time Complexity**: O(n^2) in worst and average case, O(n) in best case (already sorted).
*   **Space Complexity**: O(1) (in-place).
*   **Stability**: Stable.

```javascript
function insertionSort(arr) {
  const n = arr.length;
  for (let i = 1; i < n; i++) {
    let current = arr[i];
    let j = i - 1;
    while (j >= 0 && arr[j] > current) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = current;
  }
  return arr;
}

// Example:
// console.log("Insertion Sort:", insertionSort([12, 11, 13, 5, 6])); // [5, 6, 11, 12, 13]
```

### 3. Selection Sort

*   **Description**: Divides the input list into two parts: a sorted sublist of items built up from left to right at the front (left) of the list and a sublist of the remaining unsorted items. The algorithm proceeds by finding the smallest (or largest, depending on sorting order) element in the unsorted sublist, exchanging it with the leftmost unsorted element, and moving the sublist boundary one element to the right.
*   **Time Complexity**: O(n^2) in all cases (worst, average, best).
*   **Space Complexity**: O(1) (in-place).
*   **Stability**: Unstable.

```javascript
function selectionSort(arr) {
  const n = arr.length;
  for (let i = 0; i < n - 1; i++) {
    let minIdx = i;
    for (let j = i + 1; j < n; j++) {
      if (arr[j] < arr[minIdx]) {
        minIdx = j;
      }
    }
    // Swap the found minimum element with the first element
    if (minIdx !== i) {
      [arr[i], arr[minIdx]] = [arr[minIdx], arr[i]];
    }
  }
  return arr;
}

// Example:
// console.log("Selection Sort:", selectionSort([64, 25, 12, 22, 11])); // [11, 12, 22, 25, 64]
```

### 4. Merge Sort

*   **Description**: A divide-and-conquer algorithm. It divides the unsorted list into n sublists, each containing one element (a list of one element is considered sorted). Then, it repeatedly merges sublists to produce new sorted sublists until there is only one sorted sublist remaining.
*   **Time Complexity**: O(n log n) in all cases (worst, average, best).
*   **Space Complexity**: O(n) (not in-place, requires auxiliary space for merging).
*   **Stability**: Stable.

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) {
    return arr;
  }

  const mid = Math.floor(arr.length / 2);
  const left = arr.slice(0, mid);
  const right = arr.slice(mid);

  return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
  let result = [];
  let leftIndex = 0;
  let rightIndex = 0;

  while (leftIndex < left.length && rightIndex < right.length) {
    if (left[leftIndex] < right[rightIndex]) {
      result.push(left[leftIndex]);
      leftIndex++;
    } else {
      result.push(right[rightIndex]);
      rightIndex++;
    }
  }

  return result.concat(left.slice(leftIndex)).concat(right.slice(rightIndex));
}

// Example:
// console.log("Merge Sort:", mergeSort([38, 27, 43, 3, 9, 82, 10])); // [3, 9, 10, 27, 38, 43, 82]
```

### 5. Quick Sort

*   **Description**: A divide-and-conquer algorithm. It picks an element as a pivot and partitions the given array around the picked pivot. All elements smaller than the pivot are moved to its left, and all greater elements are moved to its right. This process is applied recursively to the sub-arrays on both sides of the pivot.
*   **Time Complexity**: O(n log n) on average, O(n^2) in worst case (poor pivot selection).
*   **Space Complexity**: O(log n) on average (due to recursion stack), O(n) in worst case.
*   **Stability**: Unstable.

```javascript
function quickSort(arr, low = 0, high = arr.length - 1) {
  if (low < high) {
    const pi = partition(arr, low, high);
    quickSort(arr, low, pi - 1);
    quickSort(arr, pi + 1, high);
  }
  return arr;
}

function partition(arr, low, high) {
  const pivot = arr[high]; // Choosing the last element as pivot
  let i = low - 1;

  for (let j = low; j < high; j++) {
    if (arr[j] < pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]]; // Swap
    }
  }
  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]]; // Swap pivot to its correct position
  return i + 1;
}

// Example:
// console.log("Quick Sort:", quickSort([10, 7, 8, 9, 1, 5])); // [1, 5, 7, 8, 9, 10]
```

### 6. Counting Sort

*   **Description**: A non-comparison-based sorting algorithm. It works by counting the number of occurrences of each distinct element in the input array. Then, it uses those counts to determine the positions of each element in the output array. It is efficient when the range of input data is not significantly larger than the number of elements.
*   **Time Complexity**: O(n + k), where n is the number of elements and k is the range of input values.
*   **Space Complexity**: O(n + k).
*   **Stability**: Can be implemented as stable.

```javascript
function countingSort(arr, min, max) {
  const count = Array(max - min + 1).fill(0);
  const output = Array(arr.length);

  // Store count of each element
  for (let i = 0; i < arr.length; i++) {
    count[arr[i] - min]++;
  }

  // Store cumulative count
  for (let i = 1; i < count.length; i++) {
    count[i] += count[i - 1];
  }

  // Place elements in output array from right to left to ensure stability
  for (let i = arr.length - 1; i >= 0; i--) {
    output[count[arr[i] - min] - 1] = arr[i];
    count[arr[i] - min]--;
  }

  // Copy the output array to arr
  for (let i = 0; i < arr.length; i++) {
    arr[i] = output[i];
  }
  return arr;
}

// Example:
// console.log("Counting Sort:", countingSort([4, 2, 2, 8, 3, 3, 1], 1, 8)); // [1, 2, 2, 3, 3, 4, 8]
```

### 7. Radix Sort

*   **Description**: A non-comparison-based integer sorting algorithm that sorts data with integer keys by grouping keys by individual digits which share the same significant position and value. It processes digits from least significant to most significant (LSD Radix Sort) or vice versa (MSD Radix Sort). It typically uses Counting Sort as a subroutine to sort by each digit.
*   **Time Complexity**: O(nk), where n is the number of elements and k is the number of digits (or maximum number of digits).
*   **Space Complexity**: O(n + k).
*   **Stability**: Stable (if the underlying sorting algorithm, like Counting Sort, is stable).

```javascript
function radixSort(arr) {
  const max = Math.max(...arr); // Find the maximum number to determine the number of digits
  let exp = 1; // Current digit place value (1s, 10s, 100s, ...)

  while (Math.floor(max / exp) > 0) {
    countingSortForRadix(arr, exp);
    exp *= 10;
  }
  return arr;
}

// Counting Sort modified for Radix Sort (sorts by a specific digit place)
function countingSortForRadix(arr, exp) {
  const n = arr.length;
  const output = Array(n);
  const count = Array(10).fill(0); // For digits 0-9

  // Store count of occurrences in count[]
  for (let i = 0; i < n; i++) {
    const digit = Math.floor(arr[i] / exp) % 10;
    count[digit]++;
  }

  // Change count[i] so that count[i] now contains actual position of this digit in output[]
  for (let i = 1; i < 10; i++) {
    count[i] += count[i - 1];
  }

  // Build the output array
  for (let i = n - 1; i >= 0; i--) {
    const digit = Math.floor(arr[i] / exp) % 10;
    output[count[digit] - 1] = arr[i];
    count[digit]--;
  }

  // Copy the output array to arr, so that arr now contains sorted numbers according to current digit
  for (let i = 0; i < n; i++) {
    arr[i] = output[i];
  }
}

// Example:
// console.log("Radix Sort:", radixSort([170, 45, 75, 90, 802, 24, 2, 66])); // [2, 24, 45, 66, 75, 90, 170, 802]
```

## Edge Cases and How to Solve Them

1.  **Empty Array**: Most sorting algorithms should handle an empty array gracefully, typically by returning the empty array itself. The provided implementations generally do this (e.g., `if (arr.length <= 1)` for Merge Sort, or loops simply won't run for others).

2.  **Array with One Element**: Similar to empty arrays, an array with a single element is already sorted. Algorithms should handle this without issues.

3.  **Array with Duplicate Elements**: Sorting algorithms must correctly handle duplicate elements. Stable sorts preserve their relative order, which can be important in some applications. Unstable sorts might change the relative order of equal elements.

4.  **Already Sorted/Reverse Sorted Array**: The performance of some algorithms varies significantly with the initial order of the array:
    *   **Bubble Sort, Insertion Sort**: O(n) best case for already sorted data.
    *   **Quick Sort**: O(n^2) worst case for already sorted or reverse-sorted data if a naive pivot selection (e.g., first or last element) is used. This can be mitigated by choosing a random pivot or median-of-three pivot.
    *   **Merge Sort, Selection Sort**: O(n log n) or O(n^2) regardless of initial order.

5.  **Non-Numeric Data**: The provided examples are for numeric data. For sorting strings or objects, you need to provide a custom comparison function to `sort()` (for JavaScript's built-in sort) or adapt the comparison logic within the algorithms.

    ```javascript
    // Custom sort for strings (lexicographical)
    const words = ["banana", "apple", "cherry"];
    words.sort((a, b) => a.localeCompare(b));
    console.log(words); // [ 'apple', 'banana', 'cherry' ]

    // Custom sort for objects by a property
    const users = [{ name: "Alice", age: 30 }, { name: "Bob", age: 25 }];
    users.sort((a, b) => a.age - b.age);
    console.log(users); // [ { name: 'Bob', age: 25 }, { name: 'Alice', age: 30 } ]
    ```

6.  **Large Numbers/Floating Points**: For very large integers that exceed JavaScript's safe integer limit (`Number.MAX_SAFE_INTEGER`), or for floating-point numbers, standard comparison might have precision issues. For very large integers, you might need a BigInt implementation or specialized libraries. For floating points, direct comparison `a < b` is usually fine, but equality checks (`a === b`) can be problematic due to precision.

7.  **Memory Constraints (for Merge Sort)**: Merge Sort requires O(n) auxiliary space. For extremely large datasets, this might be a concern. In such cases, an in-place sorting algorithm like Heap Sort (not covered here, but also O(n log n) and O(1) space) or an external sorting algorithm might be necessary.

8.  **Counting Sort/Radix Sort Range (k)**: Counting Sort and Radix Sort are highly efficient when the range of input values (k) is not excessively large. If `k` is very large, the space and time complexity can become prohibitive (e.g., O(n + k) becomes dominated by `k`). These algorithms are best suited for integers within a limited range.

## References

1.  [GeeksforGeeks: Sorting Algorithms](https://www.geeksforgeeks.org/sorting-algorithms/)
2.  [Wikipedia: Sorting algorithm](https://en.wikipedia.org/wiki/Sorting_algorithm)
3.  [Visualizing Algorithms: Sorting](https://www.cs.usfca.edu/~galles/visualization/Sorting.html)
4.  [MDN Web Docs: Array.prototype.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)


