# 04 Queues

## Description

A queue is a linear data structure that follows a particular order in which operations are performed. The order is FIFO (First In, First Out). This means the first element added to the queue is the first one to be removed. Think of a line of people waiting for a service: the first person in line is the first one to be served.

The two primary operations associated with a queue are:

*   **Enqueue (or Offer)**: Adds an element to the rear (or back) of the queue.
*   **Dequeue (or Poll)**: Removes the front element from the queue.

Additionally, queues often support:

*   **Front/Peek**: Returns the front element of the queue without removing it.
*   **isEmpty**: Checks if the queue is empty.
*   **size**: Returns the number of elements in the queue.

Queues can be implemented using arrays or linked lists. Linked list implementations are generally preferred for their efficiency in enqueue and dequeue operations (O(1)), whereas array-based implementations might suffer from O(n) complexity for dequeue if elements need to be shifted.

## Mentality Before Solving

When considering using a queue, keep these points in mind:

1.  **FIFO Requirement**: The most crucial aspect is whether the problem naturally fits the First In, First Out principle. If the order of processing requires that the earliest added item is handled first, a queue is the ideal choice.
2.  **Task Scheduling/Resource Management**: Queues are excellent for managing tasks that need to be processed in the order they arrive, such as print job queues, CPU scheduling, or handling requests in a web server. They ensure fairness by processing requests in the order they were received.
3.  **Breadth-First Search (BFS)**: Queues are fundamental to implementing Breadth-First Search (BFS) algorithms for traversing graphs and trees. BFS explores all the neighbor nodes at the present depth prior to moving on to the nodes at the next depth level.
4.  **Buffering**: Queues can act as buffers in systems where data is produced at one rate and consumed at another, ensuring smooth flow and preventing data loss.
5.  **Asynchronous Operations**: In asynchronous programming, queues can be used to manage a sequence of operations that need to be executed in a specific order, even if their completion times vary.

## Code in JavaScript/Node.js

JavaScript arrays can be used to implement queues, but using `shift()` for dequeue can be inefficient (O(n)). A more efficient array-based implementation involves managing two pointers (front and rear) or using a linked list.

### Array-based Queue Implementation (using `push` and `shift` - less efficient for large queues)

```javascript
class Queue {
  constructor() {
    this.items = [];
  }

  // Add an element to the rear of the queue
  enqueue(element) {
    this.items.push(element);
  }

  // Remove and return the front element from the queue
  dequeue() {
    if (this.isEmpty()) {
      return "Underflow"; // Queue is empty, cannot dequeue
    }
    return this.items.shift(); // O(n) operation as elements are re-indexed
  }

  // Return the front element of the queue without removing it
  front() {
    if (this.isEmpty()) {
      return "No elements in Queue";
    }
    return this.items[0];
  }

  // Check if the queue is empty
  isEmpty() {
    return this.items.length === 0;
  }

  // Get the number of elements in the queue
  size() {
    return this.items.length;
  }

  // Clear the queue
  clear() {
    this.items = [];
  }

  // Print the queue elements
  printQueue() {
    let str = "";
    for (let i = 0; i < this.items.length; i++) {
      str += this.items[i] + " ";
    }
    console.log(str.trim());
  }
}

// Example Usage:
const queue = new Queue();
console.log(queue.isEmpty()); // Output: true

queue.enqueue(10);
queue.enqueue(20);
queue.enqueue(30);
queue.printQueue(); // Output: 10 20 30

console.log(queue.front()); // Output: 10
console.log(queue.dequeue()); // Output: 10
queue.printQueue(); // Output: 20 30

console.log(queue.size()); // Output: 2
queue.clear();
console.log(queue.isEmpty()); // Output: true
console.log(queue.dequeue()); // Output: Underflow
```

### Linked List-based Queue Implementation (more efficient)

This implementation uses the `Node` class from the `Linked_Lists.md` file.

```javascript
// Assuming Node class is defined as:
// class Node {
//   constructor(data) {
//     this.data = data;
//     this.next = null;
//   }
// }

class QueueWithLinkedList {
  constructor() {
    this.front = null; // Points to the front of the queue
    this.rear = null;  // Points to the rear of the queue
    this.size = 0;
  }

  // Add an element to the rear of the queue
  enqueue(element) {
    const newNode = new Node(element);
    if (this.isEmpty()) {
      this.front = newNode;
      this.rear = newNode;
    } else {
      this.rear.next = newNode;
      this.rear = newNode;
    }
    this.size++;
  }

  // Remove and return the front element from the queue
  dequeue() {
    if (this.isEmpty()) {
      return "Underflow";
    }
    const dequeuedElement = this.front.data;
    this.front = this.front.next; // Move front to the next node
    if (!this.front) { // If front becomes null, queue is now empty
      this.rear = null;
    }
    this.size--;
    return dequeuedElement;
  }

  // Return the front element of the queue without removing it
  front() {
    if (this.isEmpty()) {
      return "No elements in Queue";
    }
    return this.front.data;
  }

  // Check if the queue is empty
  isEmpty() {
    return this.front === null;
  }

  // Get the number of elements in the queue
  size() {
    return this.size;
  }

  // Print the queue elements
  printQueue() {
    let current = this.front;
    let str = "";
    while (current) {
      str += current.data + " ";
      current = current.next;
    }
    console.log(str.trim());
  }
}

// Example Usage:
// const queueLL = new QueueWithLinkedList();
// queueLL.enqueue(100);
// queueLL.enqueue(200);
// queueLL.printQueue();
// console.log(queueLL.dequeue());
```

## Edge Cases and How to Solve Them

1.  **Queue Underflow (Dequeue/Front from an Empty Queue)**: Attempting to `dequeue()` or `front()` from an empty queue is a common edge case. This should be prevented to avoid errors or undefined behavior. The solution is to check if the queue is empty before performing these operations.

    ```javascript
    dequeue() {
      if (this.isEmpty()) {
        return "Underflow"; // Or throw an error
      }
      // ... (rest of dequeue logic)
    }

    front() {
      if (this.isEmpty()) {
        return "No elements in Queue"; // Or throw an error
      }
      // ... (rest of front logic)
    }
    ```

2.  **Queue Overflow (Enqueue to a Full Queue - for fixed-size implementations)**: Similar to stacks, if you were implementing a queue with a fixed-size array, you would need to handle the case where `enqueue()` is called on a full queue. This is known as queue overflow. The solution would involve checking if `this.size === MAX_SIZE` before enqueuing.

    ```javascript
    // (Conceptual for fixed-size array implementation)
    // enqueue(element) {
    //   if (this.size === MAX_SIZE) {
    //     return "Overflow"; // Or throw an error
    //   }
    //   // ... (add element logic)
    // }
    ```

3.  **Handling `rear` pointer after `dequeue` when queue becomes empty**: In a linked list implementation, when the last element is dequeued, `this.front` becomes `null`. It's crucial to also set `this.rear` to `null` to correctly reflect that the queue is empty. Otherwise, `this.rear` might still point to a disconnected node, leading to incorrect behavior on subsequent `enqueue` operations.

    ```javascript
    dequeue() {
      // ...
      this.front = this.front.next;
      if (!this.front) { // If front becomes null, queue is now empty
        this.rear = null;
      }
      // ...
    }
    ```

4.  **Efficiency of Array-based Queues**: As noted, using `shift()` for `dequeue` in a JavaScript array-based queue is O(n) because it re-indexes all subsequent elements. For performance-critical applications with large queues, a linked list implementation or a circular array implementation (which manages front and rear pointers to avoid shifting) is more efficient.

5.  **Memory Usage (Linked List vs. Array)**: Linked list implementations of queues have a higher memory overhead per element due to storing pointers. Array-based implementations are more memory-efficient for storing many small elements. However, linked lists avoid the need for resizing, which can be a performance consideration for very large, frequently growing/shrinking queues if using a fixed-size array that needs resizing.

## References

1.  [GeeksforGeeks: Queue Data Structure](https://www.geeksforgeeks.org/queue-data-structure/)
2.  [Wikipedia: Queue (abstract data type)](https://en.wikipedia.org/wiki/Queue_(abstract_data_type))
3.  [MDN Web Docs: Array.prototype.push()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)
4.  [MDN Web Docs: Array.prototype.shift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)


