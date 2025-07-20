# 00 Arrays

## Description

An array is a fundamental data structure that stores a collection of elements, each identified by an array index or key. Arrays are among the oldest and most important data structures, and are used by almost every program. They are typically used to implement static tables and to implement other data structures like stacks, queues, and hash tables. Arrays are characterized by their ability to store elements of the same data type (though in some languages like JavaScript, they can store mixed types) and provide direct (random) access to elements using their index. This direct access is possible because elements are stored in contiguous memory locations.

## Mentality Before Solving

When approaching a problem that might involve arrays, consider the following:

1.  **Fixed Size vs. Dynamic Size**: Are the number of elements known beforehand? If so, a fixed-size array might be efficient. If the size changes frequently, a dynamic array (like JavaScript's built-in Array) or other data structures might be more suitable.
2.  **Access Patterns**: Will elements be accessed randomly by index, or will there be frequent insertions/deletions at arbitrary positions? Arrays excel at random access (O(1) time complexity) but can be inefficient for insertions/deletions in the middle (O(n) time complexity) as it requires shifting elements.
3.  **Memory Locality**: Arrays benefit from cache locality due to contiguous memory storage, which can lead to faster processing for sequential access patterns.
4.  **Sorting and Searching**: Arrays are often used in conjunction with sorting algorithms (e.g., Quick Sort, Merge Sort) and searching algorithms (e.g., Binary Search on sorted arrays) due to their ordered nature.
5.  **Multidimensional Data**: For representing tables, matrices, or grids, multidimensional arrays are a natural fit.

## Code in JavaScript/Node.js

Here are some common array operations in JavaScript:

```javascript
// 1. Array Declaration and Initialization
let emptyArray = [];
let numbers = [1, 2, 3, 4, 5];
let mixedTypes = [1, 'hello', true, null];

// 2. Accessing Elements
console.log(numbers[0]); // Output: 1
console.log(numbers[2]); // Output: 3

// 3. Modifying Elements
numbers[1] = 10;
console.log(numbers); // Output: [1, 10, 3, 4, 5]

// 4. Array Length
console.log(numbers.length); // Output: 5

// 5. Adding Elements
// At the end (push)
numbers.push(6);
console.log(numbers); // Output: [1, 10, 3, 4, 5, 6]

// At the beginning (unshift)
numbers.unshift(0);
console.log(numbers); // Output: [0, 1, 10, 3, 4, 5, 6]

// 6. Removing Elements
// From the end (pop)
let lastElement = numbers.pop();
console.log(lastElement); // Output: 6
console.log(numbers); // Output: [0, 1, 10, 3, 4, 5]

// From the beginning (shift)
let firstElement = numbers.shift();
console.log(firstElement); // Output: 0
console.log(numbers); // Output: [1, 10, 3, 4, 5]

// 7. Iterating through an Array
// Using for loop
for (let i = 0; i < numbers.length; i++) {
  console.log(numbers[i]);
}

// Using forEach
numbers.forEach(function(number) {
  console.log(number);
});

// Using for...of
for (let number of numbers) {
  console.log(number);
}

// 8. Finding Elements
let index = numbers.indexOf(10); // Returns index of first occurrence, -1 if not found
console.log(index); // Output: 1

let includes = numbers.includes(3); // Returns true if element exists, false otherwise
console.log(includes); // Output: true

// 9. Slicing and Splicing
// slice (creates a new array)
let subArray = numbers.slice(1, 3); // Elements from index 1 up to (but not including) 3
console.log(subArray); // Output: [10, 3]
console.log(numbers); // Original array remains unchanged: [1, 10, 3, 4, 5]

// splice (modifies the original array)
// Remove elements
let removedElements = numbers.splice(1, 2); // Remove 2 elements starting from index 1
console.log(removedElements); // Output: [10, 3]
console.log(numbers); // Original array modified: [1, 4, 5]

// Add elements (at index 1, remove 0 elements, then add 100, 200)
numbers.splice(1, 0, 100, 200);
console.log(numbers); // Output: [1, 100, 200, 4, 5]

// 10. Concatenating Arrays
let moreNumbers = [7, 8, 9];
let combinedArray = numbers.concat(moreNumbers);
console.log(combinedArray); // Output: [1, 100, 200, 4, 5, 7, 8, 9]

// 11. Sorting Arrays
let unsortedNumbers = [5, 2, 8, 1, 9];
unsortedNumbers.sort(); // Sorts in place (lexicographically by default for numbers)
console.log(unsortedNumbers); // Output: [1, 2, 5, 8, 9]

let unsortedStrings = ['banana', 'apple', 'cherry'];
unsortedStrings.sort();
console.log(unsortedStrings); // Output: ['apple', 'banana', 'cherry']

// Custom sort for numbers
let customSortNumbers = [5, 2, 8, 1, 9];
customSortNumbers.sort((a, b) => a - b); // Ascending order
console.log(customSortNumbers); // Output: [1, 2, 5, 8, 9]
```

## Edge Cases and How to Solve Them

1.  **Accessing Out-of-Bounds Indices**: Attempting to access an index that is outside the array's valid range (0 to `length - 1`) in JavaScript will not throw an error but will return `undefined`. When writing code, always ensure that array access is within bounds, especially when dealing with user input or dynamic array sizes. Using `if (index >= 0 && index < array.length)` checks is crucial.

    ```javascript
    let arr = [1, 2, 3];
    console.log(arr[5]); // Output: undefined
    console.log(arr[-1]); // Output: undefined

    function safeAccess(arr, index) {
      if (index >= 0 && index < arr.length) {
        return arr[index];
      } else {
        console.warn("Index out of bounds!");
        return undefined; // Or throw an error, depending on requirements
      }
    }
    console.log(safeAccess(arr, 1)); // Output: 2
    console.log(safeAccess(arr, 5)); // Output: Index out of bounds! undefined
    ```

2.  **Modifying Array During Iteration**: Modifying an array (adding or removing elements) while iterating over it using a `for` loop can lead to unexpected behavior, such as skipping elements or infinite loops. It's generally safer to iterate backward when removing elements or create a copy of the array if modifications are extensive.

    ```javascript
    let numbersToFilter = [1, 2, 3, 4, 5];
    // Problematic: will skip 3
    for (let i = 0; i < numbersToFilter.length; i++) {
      if (numbersToFilter[i] % 2 === 0) {
        numbersToFilter.splice(i, 1); // Removes element, shifts subsequent elements
      }
    }
    console.log(numbersToFilter); // Output: [1, 3, 5] (2 and 4 were removed, but 3 was skipped)

    // Safer: iterate backward
    let numbersToFilterSafe = [1, 2, 3, 4, 5];
    for (let i = numbersToFilterSafe.length - 1; i >= 0; i--) {
      if (numbersToFilterSafe[i] % 2 === 0) {
        numbersToFilterSafe.splice(i, 1);
      }
    }
    console.log(numbersToFilterSafe); // Output: [1, 3, 5]

    // Even safer: use filter for creating a new array
    let originalNumbers = [1, 2, 3, 4, 5];
    let filteredNumbers = originalNumbers.filter(num => num % 2 !== 0);
    console.log(filteredNumbers); // Output: [1, 3, 5]
    ```

3.  **Shallow Copy vs. Deep Copy**: When copying arrays, direct assignment (`let newArr = oldArr;`) creates a shallow copy, meaning both variables point to the same underlying array in memory. Modifications to `newArr` will affect `oldArr`. For primitive elements, spread syntax (`...`) or `slice()` creates a deep copy. For arrays containing objects or other arrays, a deep copy requires recursive copying or using methods like `JSON.parse(JSON.stringify(array))` (with caveats for functions, `undefined`, `Date` objects, etc.).

    ```javascript
    let original = [1, 2, { a: 3 }];
    let shallowCopy = original; // Shallow copy
    let deepCopyPrimitives = [...original]; // Deep copy for primitives, shallow for objects

    shallowCopy[0] = 100;
    console.log(original[0]); // Output: 100 (original array modified)

    deepCopyPrimitives[0] = 200;
    console.log(original[0]); // Output: 100 (original array not modified for primitive)

    deepCopyPrimitives[2].a = 300; // Modifies the object within the array
    console.log(original[2].a); // Output: 300 (original object modified even in 'deep' copy)

    // True deep copy (for simple objects, with caveats)
    let trueDeepCopy = JSON.parse(JSON.stringify(original));
    trueDeepCopy[2].a = 400;
    console.log(original[2].a); // Output: 300 (original object not modified)
    ```

4.  **Performance for Large Arrays**: Operations like `unshift()`, `shift()`, and `splice()` (when inserting/deleting in the middle) have O(n) time complexity because they require re-indexing all subsequent elements. For very large arrays and frequent operations at the beginning or middle, consider data structures like Linked Lists or Deques that offer better performance for such operations.

5.  **Sparse Arrays**: In JavaScript, arrays can be sparse, meaning they can have empty slots (e.g., `let arr = [1, , 3];`). Iterating over sparse arrays with `forEach` or `map` will skip empty slots, while a `for` loop will access them as `undefined`. Be mindful of this behavior when processing arrays that might have missing elements.

    ```javascript
    let sparseArr = [1, , 3];
    console.log(sparseArr.length); // Output: 3
    console.log(sparseArr[1]); // Output: undefined

    sparseArr.forEach(item => console.log(item)); // Output: 1, 3 (skips undefined)

    for (let i = 0; i < sparseArr.length; i++) {
      console.log(sparseArr[i]); // Output: 1, undefined, 3
    }
    ```

## References

1.  [MDN Web Docs: Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
2.  [GeeksforGeeks: Introduction to Arrays](https://www.geeksforgeeks.org/introduction-to-arrays-data-structure-and-algorithm-tutorials/)
3.  [Wikipedia: Array data structure](https://en.wikipedia.org/wiki/Array_data_structure)


