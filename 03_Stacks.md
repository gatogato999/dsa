# 03 Stacks

## Description

A stack is a linear data structure that follows a particular order in which operations are performed. The order may be LIFO (Last In, First Out) or FILO (First In, Last Out). This means the last element added to the stack is the first one to be removed. Think of a stack of plates: you can only add a new plate to the top, and you can only remove the topmost plate.

The two primary operations associated with a stack are:

*   **Push**: Adds an element to the top of the stack.
*   **Pop**: Removes the top element from the stack.

Additionally, stacks often support:

*   **Peek/Top**: Returns the top element of the stack without removing it.
*   **isEmpty**: Checks if the stack is empty.
*   **size**: Returns the number of elements in the stack.

Stacks can be implemented using arrays or linked lists. The choice of implementation depends on the specific requirements and performance considerations.

## Mentality Before Solving

When considering using a stack, keep these points in mind:

1.  **LIFO/FILO Requirement**: The most crucial aspect is whether the problem naturally fits the Last In, First Out principle. If the order of processing requires that the most recently added item is handled first, a stack is the ideal choice.
2.  **Tracking State/Backtracking**: Stacks are excellent for managing state in algorithms that involve backtracking, such as depth-first search (DFS) in graphs or trees, parsing expressions, or undo/redo functionalities in applications. Each step or state can be pushed onto the stack, and when backtracking is needed, states are popped off.
3.  **Function Call Stack**: Understand that the function call stack in programming languages operates on a LIFO principle. This can help in understanding recursion and how function calls are managed.
4.  **Balanced Parentheses/Brackets**: A classic application of stacks is checking for balanced parentheses, brackets, or braces in an expression. When an opening bracket is encountered, it's pushed onto the stack. When a closing bracket is found, the top of the stack is popped and checked for a match.
5.  **Expression Evaluation**: Stacks are used in evaluating arithmetic expressions, particularly in converting infix expressions to postfix (Reverse Polish Notation) or prefix, and then evaluating them.

## Code in JavaScript/Node.js

JavaScript arrays inherently support stack-like operations using `push()` and `pop()`, making them a convenient way to implement a stack. However, for a more explicit data structure, you can wrap an array or use a linked list.

### Array-based Stack Implementation

```javascript
class Stack {
  constructor() {
    this.items = []; // Using a JavaScript array to store stack elements
  }

  // Add an element to the top of the stack
  push(element) {
    this.items.push(element);
  }

  // Remove and return the top element from the stack
  pop() {
    if (this.isEmpty()) {
      return "Underflow"; // Stack is empty, cannot pop
    }
    return this.items.pop();
  }

  // Return the top element of the stack without removing it
  peek() {
    if (this.isEmpty()) {
      return "No elements in Stack";
    }
    return this.items[this.items.length - 1];
  }

  // Check if the stack is empty
  isEmpty() {
    return this.items.length === 0;
  }

  // Get the number of elements in the stack
  size() {
    return this.items.length;
  }

  // Clear the stack
  clear() {
    this.items = [];
  }

  // Print the stack elements (from top to bottom)
  printStack() {
    let str = "";
    for (let i = this.items.length - 1; i >= 0; i--) {
      str += this.items[i] + "\n";
    }
    console.log(str);
  }
}

// Example Usage:
const stack = new Stack();
console.log(stack.isEmpty()); // Output: true

stack.push(10);
stack.push(20);
stack.push(30);
stack.printStack();
// Output:
// 30
// 20
// 10

console.log(stack.peek()); // Output: 30
console.log(stack.pop());  // Output: 30
stack.printStack();
// Output:
// 20
// 10

console.log(stack.size()); // Output: 2
stack.clear();
console.log(stack.isEmpty()); // Output: true
console.log(stack.pop()); // Output: Underflow
```

### Linked List-based Stack Implementation

This implementation uses the `Node` class from the `Linked_Lists.md` file.

```javascript
// Assuming Node class is defined as:
// class Node {
//   constructor(data) {
//     this.data = data;
//     this.next = null;
//   }
// }

class StackWithLinkedList {
  constructor() {
    this.top = null; // Points to the top node of the stack
    this.size = 0;
  }

  // Add an element to the top of the stack
  push(element) {
    const newNode = new Node(element);
    newNode.next = this.top; // New node points to the current top
    this.top = newNode;      // New node becomes the new top
    this.size++;
  }

  // Remove and return the top element from the stack
  pop() {
    if (this.isEmpty()) {
      return "Underflow";
    }
    const poppedElement = this.top.data;
    this.top = this.top.next; // Move top to the next node
    this.size--;
    return poppedElement;
  }

  // Return the top element of the stack without removing it
  peek() {
    if (this.isEmpty()) {
      return "No elements in Stack";
    }
    return this.top.data;
  }

  // Check if the stack is empty
  isEmpty() {
    return this.top === null;
  }

  // Get the number of elements in the stack
  size() {
    return this.size;
  }

  // Print the stack elements
  printStack() {
    let current = this.top;
    let str = "";
    while (current) {
      str += current.data + "\n";
      current = current.next;
    }
    console.log(str);
  }
}

// Example Usage:
// const stackLL = new StackWithLinkedList();
// stackLL.push(100);
// stackLL.push(200);
// stackLL.printStack();
// console.log(stackLL.pop());
```

## Edge Cases and How to Solve Them

1.  **Stack Underflow (Popping from an Empty Stack)**: Attempting to `pop()` or `peek()` from an empty stack is a common edge case. This should be prevented to avoid errors or undefined behavior. The solution is to check if the stack is empty before performing these operations.

    ```javascript
    pop() {
      if (this.isEmpty()) {
        return "Underflow"; // Or throw an error
      }
      // ... (rest of pop logic)
    }

    peek() {
      if (this.isEmpty()) {
        return "No elements in Stack"; // Or throw an error
      }
      // ... (rest of peek logic)
    }
    ```

2.  **Stack Overflow (Pushing to a Full Stack - for fixed-size implementations)**: While JavaScript arrays are dynamic and grow automatically, if you were implementing a stack with a fixed-size array (e.g., in C++ or Java), you would need to handle the case where `push()` is called on a full stack. This is known as stack overflow. The solution would involve checking if `this.size === MAX_SIZE` before pushing.

    ```javascript
    // (Conceptual for fixed-size array implementation)
    // push(element) {
    //   if (this.size === MAX_SIZE) {
    //     return "Overflow"; // Or throw an error
    //   }
    //   this.items[this.size++] = element;
    // }
    ```

3.  **Empty Stack State after Operations**: After a series of `pop` operations, the stack might become empty. Ensure that `isEmpty()` correctly reflects this state and that subsequent `push` operations correctly re-initialize the stack (e.g., setting `this.top` to the new node in a linked list implementation).

4.  **Performance of Array-based Stacks**: While `push()` and `pop()` on JavaScript arrays are generally efficient (amortized O(1)), `unshift()` and `shift()` (adding/removing from the beginning) are O(n) operations. Therefore, it's crucial to use `push()` and `pop()` for stack operations when using arrays to maintain optimal performance.

5.  **Memory Usage (Linked List vs. Array)**: Linked list implementations of stacks have a higher memory overhead per element due to storing pointers. Array-based implementations are more memory-efficient for storing many small elements. However, linked lists avoid the need for resizing, which can be a performance consideration for very large, frequently growing/shrinking stacks if using a fixed-size array that needs resizing.

## References

1.  [GeeksforGeeks: Stack Data Structure](https://www.geeksforgeeks.org/stack-data-structure/)
2.  [Wikipedia: Stack (abstract data type)](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))
3.  [MDN Web Docs: Array.prototype.push()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)
4.  [MDN Web Docs: Array.prototype.pop()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)


