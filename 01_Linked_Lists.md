# 01 Linked Lists

## Description

A linked list is a linear data structure where elements are not stored at contiguous memory locations. Instead, each element (called a node) points to the next element in the sequence. A node typically consists of two parts: the data (or value) and a reference (or pointer) to the next node. The first node in the list is called the head, and the last node points to null, signifying the end of the list.

Linked lists come in several variations:

*   **Singly Linked List**: Each node points only to the next node.
*   **Doubly Linked List**: Each node has two pointers, one to the next node and one to the previous node, allowing for traversal in both directions.
*   **Circular Linked List**: The last node points back to the first node, forming a circle.

Linked lists are particularly useful for implementing dynamic data structures like stacks, queues, and hash tables, and for situations where frequent insertions and deletions are required, as they can be performed efficiently without shifting elements.

## Mentality Before Solving

When considering a linked list for a problem, think about:

1.  **Dynamic Size**: Linked lists are ideal when the number of elements is not known in advance or changes frequently, as they can grow or shrink dynamically without needing to reallocate memory for the entire structure.
2.  **Efficient Insertions and Deletions**: Unlike arrays, inserting or deleting an element in a linked list (once the position is found) takes O(1) time, as it only involves updating a few pointers. This is a major advantage over arrays where such operations can take O(n) time.
3.  **Sequential Access**: Accessing elements in a linked list is sequential, meaning to reach the nth element, you must traverse from the head, taking O(n) time. This is a disadvantage compared to arrays, which offer O(1) random access.
4.  **Memory Overhead**: Each node in a linked list requires extra memory for the pointer(s) in addition to the data itself. This can be a concern for very large lists with small data elements.
5.  **Traversal Direction**: If bidirectional traversal is needed, a doubly linked list is more appropriate. If only forward traversal is sufficient, a singly linked list is simpler and uses less memory.
6.  **Use Cases**: Linked lists are often used in scenarios like implementing LRU caches, managing memory (free lists), and representing polynomials.

## Code in JavaScript/Node.js

Here's a basic implementation of a Singly Linked List in JavaScript:

```javascript
// Node class for a Singly Linked List
class Node {
  constructor(data) {
    this.data = data;
    this.next = null; // Pointer to the next node
  }
}

// Singly Linked List class
class LinkedList {
  constructor() {
    this.head = null; // First node in the list
    this.size = 0;    // Number of nodes in the list
  }

  // Add a node to the end of the list
  add(data) {
    const newNode = new Node(data);
    if (!this.head) {
      this.head = newNode;
    } else {
      let current = this.head;
      while (current.next) {
        current = current.next;
      }
      current.next = newNode;
    }
    this.size++;
  }

  // Insert a node at a specific index
  insertAt(data, index) {
    if (index < 0 || index > this.size) {
      console.error("Invalid index for insertion.");
      return false;
    }

    const newNode = new Node(data);
    let current = this.head;
    let previous;

    // Insert at the beginning
    if (index === 0) {
      newNode.next = this.head;
      this.head = newNode;
    } else {
      // Traverse to the index
      for (let i = 0; i < index; i++) {
        previous = current;
        current = current.next;
      }
      newNode.next = current;
      previous.next = newNode;
    }
    this.size++;
    return true;
  }

  // Get data at a specific index
  getAt(index) {
    if (index < 0 || index >= this.size) {
      console.error("Invalid index for retrieval.");
      return null;
    }

    let current = this.head;
    for (let i = 0; i < index; i++) {
      current = current.next;
    }
    return current.data;
  }

  // Remove a node from a specific index
  removeAt(index) {
    if (index < 0 || index >= this.size) {
      console.error("Invalid index for removal.");
      return null;
    }

    let current = this.head;
    let previous;
    let removedData;

    // Remove first element
    if (index === 0) {
      removedData = current.data;
      this.head = current.next;
    } else {
      for (let i = 0; i < index; i++) {
        previous = current;
        current = current.next;
      }
      removedData = current.data;
      previous.next = current.next;
    }
    this.size--;
    return removedData;
  }

  // Find the index of a given data value
  indexOf(data) {
    let current = this.head;
    let index = 0;
    while (current) {
      if (current.data === data) {
        return index;
      }
      current = current.next;
      index++;
    }
    return -1; // Not found
  }

  // Check if the list is empty
  isEmpty() {
    return this.size === 0;
  }

  // Get the size of the list
  getSize() {
    return this.size;
  }

  // Print the list elements
  printList() {
    let current = this.head;
    let listString = "";
    while (current) {
      listString += current.data + " -> ";
      current = current.next;
    }
    listString += "null";
    console.log(listString);
  }
}

// Example Usage:
const myList = new LinkedList();
myList.add(10);
myList.add(20);
myList.add(30);
myList.printList(); // Output: 10 -> 20 -> 30 -> null

myList.insertAt(5, 0);
myList.printList(); // Output: 5 -> 10 -> 20 -> 30 -> null

myList.insertAt(25, 3);
myList.printList(); // Output: 5 -> 10 -> 20 -> 25 -> 30 -> null

console.log("Element at index 2:", myList.getAt(2)); // Output: Element at index 2: 20

console.log("Index of 25:", myList.indexOf(25)); // Output: Index of 25: 3
console.log("Index of 100:", myList.indexOf(100)); // Output: Index of 100: -1

console.log("Removed element at index 1:", myList.removeAt(1)); // Output: Removed element at index 1: 10
myList.printList(); // Output: 5 -> 20 -> 25 -> 30 -> null

console.log("List size:", myList.getSize()); // Output: List size: 4
console.log("Is list empty:", myList.isEmpty()); // Output: Is list empty: false
```

## Edge Cases and How to Solve Them

1.  **Empty List Operations**: Many operations (like `getAt`, `removeAt`) need to handle the case where the list is empty. Attempting to access `this.head.data` or `this.head.next` on an empty list (where `this.head` is `null`) will result in a `TypeError`. Solutions involve checking `if (!this.head)` at the beginning of such methods and returning `null` or throwing an error.

    ```javascript
    // Example from add method:
    add(data) {
      const newNode = new Node(data);
      if (!this.head) { // Handle empty list: new node becomes the head
        this.head = newNode;
      } else {
        // ... (rest of the logic for non-empty list)
      }
      this.size++;
    }

    // Example from getAt method:
    getAt(index) {
      if (index < 0 || index >= this.size) {
        console.error("Invalid index for retrieval.");
        return null; // Return null for invalid index or empty list
      }
      // ... (rest of the logic)
    }
    ```

2.  **Insertion/Deletion at the Beginning (Head)**: This is a special case because the `head` pointer needs to be updated. If not handled separately, it can lead to incorrect list structure. The solution is to check if `index === 0` and update `this.head` accordingly.

    ```javascript
    // Example from insertAt method:
    if (index === 0) {
      newNode.next = this.head;
      this.head = newNode;
    } else {
      // ... (rest of the logic for insertion elsewhere)
    }

    // Example from removeAt method:
    if (index === 0) {
      removedData = current.data;
      this.head = current.next;
    } else {
      // ... (rest of the logic for removal elsewhere)
    }
    ```

3.  **Insertion/Deletion at the End**: While not as complex as the head, inserting at the end requires traversing the entire list to find the last node. Deleting the last node also requires finding the second-to-last node to update its `next` pointer to `null`. In a singly linked list, this means traversing from the beginning. In a doubly linked list, it's more efficient as you can use the `prev` pointer from the last node.

    ```javascript
    // Example from add method (adds to end):
    add(data) {
      const newNode = new Node(data);
      if (!this.head) {
        this.head = newNode;
      } else {
        let current = this.head;
        while (current.next) { // Traverse until current.next is null (i.e., current is the last node)
          current = current.next;
        }
        current.next = newNode; // Link the new node to the end
      }
      this.size++;
    }
    ```

4.  **Invalid Index**: Providing an index that is negative or greater than or equal to the list's size for operations like `insertAt`, `getAt`, or `removeAt` should be handled gracefully. The solution is to validate the `index` parameter at the start of the method and return `null`, `false`, or throw an error.

    ```javascript
    // Example from insertAt method:
    insertAt(data, index) {
      if (index < 0 || index > this.size) { // index > this.size for insertion at end
        console.error("Invalid index for insertion.");
        return false;
      }
      // ...
    }

    // Example from getAt/removeAt methods:
    getAt(index) {
      if (index < 0 || index >= this.size) { // index >= this.size for retrieval/removal
        console.error("Invalid index for retrieval.");
        return null;
      }
      // ...
    }
    ```

5.  **Memory Leaks (in languages with manual memory management)**: In languages like C++ or C, failing to properly deallocate memory for removed nodes can lead to memory leaks. JavaScript has automatic garbage collection, so this is less of a concern, but understanding the concept is important for other environments.

6.  **Circular References (for garbage collection)**: While JavaScript handles most memory management, be cautious with circular linked lists if not properly managed, as they can sometimes create situations where objects are not garbage collected if not explicitly dereferenced, though modern GCs are quite good at detecting and collecting cycles.

## References

1.  [GeeksforGeeks: Introduction to Linked List](https://www.geeksforgeeks.org/data-structures/linked-list/)
2.  [Wikipedia: Linked list](https://en.wikipedia.org/wiki/Linked_list)
3.  [MDN Web Docs: Data structures - Linked list](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Data_structures#linked_list)


