# 05 Trees (General Trees, Binary Trees, Binary Search Trees)

## Description

A tree is a non-linear data structure that simulates a hierarchical tree structure, with a root value and subtrees of children with a parent node, represented as a set of linked nodes. Unlike linear data structures (Arrays, Linked Lists, Stacks, Queues), trees are used to represent data with a hierarchical relationship between elements.

Key terminology:
*   **Root**: The topmost node in a tree. It has no parent.
*   **Child**: A node directly connected to another node when moving away from the Root.
*   **Parent**: A node directly connected to another node when moving towards the Root.
*   **Siblings**: Nodes that share the same parent.
*   **Leaf**: A node with no children.
*   **Edge**: The connection between two nodes.
*   **Path**: A sequence of nodes and edges connecting one node to another.
*   **Depth**: The length of the path from the root to the node.
*   **Height**: The length of the path from the node to the deepest leaf.

### General Trees

A general tree is a tree data structure in which each node can have an arbitrary number of children. There are no restrictions on the number of children a node can have.

### Binary Trees

A binary tree is a tree data structure in which each node has at most two children, referred to as the left child and the right child. Binary trees are widely used in computer science for various purposes, including expression parsing, data compression, and implementing other data structures.

Common types of binary trees:
*   **Full Binary Tree**: Every node has either 0 or 2 children.
*   **Complete Binary Tree**: All levels are completely filled except possibly the last level, which is filled from left to right.
*   **Perfect Binary Tree**: All internal nodes have two children and all leaf nodes are at the same level.

### Binary Search Trees (BST)

A Binary Search Tree (BST) is a special type of binary tree that maintains a specific ordering property: for any given node, all values in its left subtree are less than the node's value, and all values in its right subtree are greater than the node's value. This property makes BSTs highly efficient for searching, insertion, and deletion operations, with an average time complexity of O(log n).

## Mentality Before Solving

When considering a tree data structure, ask yourself:

1.  **Hierarchical Relationships**: Does the data inherently have a parent-child or hierarchical relationship? Examples include file systems, organizational charts, or XML/HTML document structures. If so, a tree is a natural fit.
2.  **Efficient Searching/Sorting (for BSTs)**: If you need to store ordered data and perform frequent searches, insertions, and deletions efficiently, a Binary Search Tree (or its self-balancing variants) is an excellent choice. The logarithmic time complexity makes it very performant for large datasets.
3.  **Range Queries**: For problems involving range queries (e.g., finding all numbers within a certain range), BSTs can be adapted or specialized trees like Segment Trees can be used.
4.  **Traversal Order**: Different tree traversals (In-order, Pre-order, Post-order, Level-order) yield elements in different sequences. Understand which traversal is appropriate for your problem (e.g., in-order traversal of a BST yields sorted elements).
5.  **Dynamic Data**: Trees are dynamic data structures, meaning they can grow and shrink as elements are added or removed, unlike fixed-size arrays.
6.  **Memory Overhead**: Each node in a tree typically requires memory for its data and pointers to its children (and sometimes parent). This can be more memory-intensive than linear structures for the same number of elements.

## Code in JavaScript/Node.js

### Binary Search Tree Implementation

```javascript
// Node class for a Binary Search Tree
class Node {
  constructor(data) {
    this.data = data;
    this.left = null;  // Pointer to the left child
    this.right = null; // Pointer to the right child
  }
}

// Binary Search Tree class
class BinarySearchTree {
  constructor() {
    this.root = null; // The root node of the BST
  }

  // Insert a new node into the BST
  insert(data) {
    const newNode = new Node(data);
    if (this.root === null) {
      this.root = newNode;
    } else {
      this.insertNode(this.root, newNode);
    }
  }

  // Helper function to insert a node recursively
  insertNode(node, newNode) {
    if (newNode.data < node.data) {
      if (node.left === null) {
        node.left = newNode;
      } else {
        this.insertNode(node.left, newNode);
      }
    } else {
      if (node.right === null) {
        node.right = newNode;
      } else {
        this.insertNode(node.right, newNode);
      }
    }
  }

  // Search for a node with a given data value
  search(node, data) {
    if (node === null) {
      return null;
    } else if (data < node.data) {
      return this.search(node.left, data);
    } else if (data > node.data) {
      return this.search(node.right, data);
    }
    return node; // Found the node
  }

  // Remove a node with a given data value
  remove(data) {
    this.root = this.removeNode(this.root, data);
  }

  // Helper function to remove a node recursively
  removeNode(node, key) {
    if (node === null) {
      return null;
    } else if (key < node.data) {
      node.left = this.removeNode(node.left, key);
      return node;
    } else if (key > node.data) {
      node.right = this.removeNode(node.right, key);
      return node;
    } else {
      // Node with only one child or no child
      if (node.left === null && node.right === null) {
        node = null;
        return node;
      }
      if (node.left === null) {
        node = node.right;
        return node;
      }
      if (node.right === null) {
        node = node.left;
        return node;
      }

      // Node with two children: Get the in-order successor (smallest in the right subtree)
      const aux = this.findMinNode(node.right);
      node.data = aux.data;
      node.right = this.removeNode(node.right, aux.data);
      return node;
    }
  }

  // Helper function to find the node with the minimum value in a subtree
  findMinNode(node) {
    while (node.left !== null) {
      node = node.left;
    }
    return node;
  }

  // In-order traversal (Left -> Root -> Right) - yields sorted elements
  inorder(node) {
    if (node !== null) {
      this.inorder(node.left);
      console.log(node.data);
      this.inorder(node.right);
    }
  }

  // Pre-order traversal (Root -> Left -> Right)
  preorder(node) {
    if (node !== null) {
      console.log(node.data);
      this.preorder(node.left);
      this.preorder(node.right);
    }
  }

  // Post-order traversal (Left -> Right -> Root)
  postorder(node) {
    if (node !== null) {
      this.postorder(node.left);
      this.postorder(node.right);
      console.log(node.data);
    }
  }

  // Level-order traversal (Breadth-First Search)
  levelorder() {
    if (this.root === null) return;

    const queue = [];
    queue.push(this.root);

    while (queue.length > 0) {
      const node = queue.shift(); // Dequeue the front node
      console.log(node.data);

      if (node.left !== null) {
        queue.push(node.left);
      }
      if (node.right !== null) {
        queue.push(node.right);
      }
    }
  }

  // Get the root of the tree
  getRoot() {
    return this.root;
  }
}

// Example Usage:
const bst = new BinarySearchTree();

bst.insert(15);
bst.insert(25);
bst.insert(10);
bst.insert(7);
bst.insert(22);
bst.insert(17);
bst.insert(13);
bst.insert(5);
bst.insert(9);
bst.insert(27);

console.log("In-order traversal:");
bst.inorder(bst.getRoot()); // Output: 5 7 9 10 13 15 17 22 25 27 (sorted)

console.log("\nSearch for 22:", bst.search(bst.getRoot(), 22) ? "Found" : "Not Found"); // Output: Found
console.log("Search for 100:", bst.search(bst.getRoot(), 100) ? "Found" : "Not Found"); // Output: Not Found

console.log("\nRemoving 5 (leaf node):");
bst.remove(5);
bst.inorder(bst.getRoot());

console.log("\nRemoving 15 (node with two children):");
bst.remove(15);
bst.inorder(bst.getRoot());

console.log("\nLevel-order traversal:");
bst.levelorder();
```

## Edge Cases and How to Solve Them

1.  **Empty Tree**: Operations like `search`, `remove`, or traversals must handle the case where the tree is empty (`root` is `null`). This typically involves checking `if (this.root === null)` at the beginning of the method.

    ```javascript
    search(node, data) {
      if (node === null) { // Base case for empty subtree or not found
        return null;
      }
      // ...
    }
    ```

2.  **Inserting Duplicate Values (for BSTs)**: The standard BST definition states that all values in the left subtree are *less than* the node's value, and all values in the right subtree are *greater than* the node's value. This implies that duplicate values are typically not allowed. If duplicates are allowed, you must decide where to place them (e.g., always in the left subtree, always in the right subtree, or maintain a count at the node). The provided BST implementation places duplicates in the right subtree.

3.  **Removing Nodes**: Removing nodes from a BST is the most complex operation, especially for nodes with two children. The general strategy is:
    *   **Leaf Node**: Simply remove the node.
    *   **Node with one child**: Replace the node with its child.
    *   **Node with two children**: Find the in-order successor (the smallest node in the right subtree) or in-order predecessor (the largest node in the left subtree), replace the node's data with the successor/predecessor's data, and then recursively delete the successor/predecessor node.

4.  **Skewed Trees (Worst-Case Performance)**: If elements are inserted into a BST in strictly ascending or descending order, the tree degenerates into a linked list. In this scenario, the height of the tree becomes O(n), and operations like search, insert, and delete degrade to O(n) time complexity, losing the logarithmic advantage. This is where **Self-balancing Trees** (like AVL Trees or Red-Black Trees) become crucial, as they automatically adjust their structure to maintain a balanced height, ensuring O(log n) performance in all cases.

5.  **Memory Management (Recursion Depth)**: Recursive tree traversal and manipulation functions can lead to deep recursion stacks for very tall trees. While JavaScript engines handle recursion well, extremely deep trees might hit recursion limits in some environments. Iterative approaches can be used to avoid this, especially for traversals.

6.  **Non-Comparable Data**: BSTs rely on the ability to compare data to determine placement (less than or greater than). If the data stored in the nodes is not naturally comparable (e.g., complex objects without a defined comparison logic), you'll need to provide a custom comparison function.

## References

1.  [GeeksforGeeks: Tree Data Structure](https://www.geeksforgeeks.org/tree-data-structure/)
2.  [Wikipedia: Tree (data structure)](https://en.wikipedia.org/wiki/Tree_(data_structure))
3.  [GeeksforGeeks: Binary Tree](https://www.geeksforgeeks.org/binary-tree-data-structure/)
4.  [GeeksforGeeks: Binary Search Tree](https://www.geeksforgeeks.org/binary-search-tree-data-structure/)


