# 09 Self-balancing Trees (AVL Trees, Red-Black Trees)

## Description

Self-balancing binary search trees are a type of binary search tree that automatically keep their height small in the face of arbitrary insertions and deletions. This ensures that operations like search, insertion, and deletion maintain a logarithmic time complexity (O(log n)) in the worst case, unlike a basic Binary Search Tree (BST) which can degenerate into a linked list (O(n) worst case) if elements are inserted in a sorted or reverse-sorted order.

The self-balancing property is achieved by performing rotations (single or double) on the tree nodes whenever an insertion or deletion causes the tree to become unbalanced. The two most common types of self-balancing BSTs are AVL Trees and Red-Black Trees.

### AVL Trees

An AVL tree is a self-balancing binary search tree where the difference between the heights of the left and right subtrees of any node is at most one. This difference is called the balance factor. If at any point the balance factor of a node becomes greater than 1 or less than -1, the tree is rebalanced by performing tree rotations.

Key characteristics of AVL Trees:
*   **Strictly balanced**: Maintains a strict balance factor of -1, 0, or 1 for all nodes.
*   **Faster lookups**: Due to stricter balancing, AVL trees generally offer faster search times compared to Red-Black trees.
*   **More rotations**: May require more rotations during insertions and deletions to maintain its strict balance, potentially making write operations slightly slower.

### Red-Black Trees

A Red-Black Tree is a self-balancing binary search tree that maintains balance by enforcing a set of properties on the nodes:

1.  Every node is either Red or Black.
2.  The root is Black.
3.  Every leaf (NIL node, representing null children) is Black.
4.  If a node is Red, then both its children are Black (no two consecutive Red nodes).
5.  For each node, all simple paths from the node to descendant leaves contain the same number of Black nodes (Black-height property).

Key characteristics of Red-Black Trees:
*   **Loosely balanced**: Less strictly balanced than AVL trees, but still guarantees O(log n) performance.
*   **Fewer rotations**: Generally performs fewer rotations than AVL trees during insertions and deletions, making write operations slightly faster.
*   **Widely used**: Used in many practical applications, including `std::map` and `std::set` in C++, and Java's `TreeMap` and `TreeSet`.

## Mentality Before Solving

Choose a self-balancing tree when:

1.  **Guaranteed Logarithmic Performance**: If your application requires guaranteed O(log n) time complexity for search, insertion, and deletion operations in the worst case, regardless of the input data order. This is critical for performance-sensitive systems.
2.  **Dynamic Ordered Data**: When you need to store ordered data that changes frequently (insertions and deletions) and requires efficient retrieval of elements in sorted order or by range.
3.  **Large Datasets**: For large datasets where a simple BST would likely degenerate and lead to poor performance.
4.  **Trade-offs**: Understand the trade-offs between AVL and Red-Black trees:
    *   **AVL**: Better for read-heavy applications where search speed is paramount, as it's more strictly balanced.
    *   **Red-Black**: Better for write-heavy applications where insertions and deletions are more frequent, as it performs fewer rotations.
5.  **Alternative to Hash Tables for Ordered Data**: If you need key-value storage with guaranteed order (e.g., for range queries or iterating in sorted order), a self-balancing BST is a better choice than a hash table, which does not maintain order.

## Code in JavaScript/Node.js

Implementing self-balancing trees from scratch is complex due to the intricate rotation and recoloring logic. JavaScript does not have a built-in self-balancing tree. For practical purposes, you would typically use a library or rely on other data structures that offer similar performance characteristics (e.g., `Map` for key-value pairs, which is usually implemented with hash tables, or `SortedMap` from a third-party library if order is strictly needed).

However, for educational purposes, here's a conceptual outline of how an AVL tree insertion might involve rotations. A full implementation is extensive.

```javascript
// Conceptual Node for AVL Tree
class AVLNode {
  constructor(data) {
    this.data = data;
    this.left = null;
    this.right = null;
    this.height = 1; // Height of the node
  }
}

class AVLTree {
  constructor() {
    this.root = null;
  }

  getHeight(node) {
    if (node === null) {
      return 0;
    }
    return node.height;
  }

  getBalanceFactor(node) {
    if (node === null) {
      return 0;
    }
    return this.getHeight(node.left) - this.getHeight(node.right);
  }

  updateHeight(node) {
    node.height = Math.max(this.getHeight(node.left), this.getHeight(node.right)) + 1;
  }

  // Right rotation
  rightRotate(y) {
    const x = y.left;
    const T2 = x.right;

    x.right = y;
    y.left = T2;

    this.updateHeight(y);
    this.updateHeight(x);

    return x;
  }

  // Left rotation
  leftRotate(x) {
    const y = x.right;
    const T2 = y.left;

    y.left = x;
    x.right = T2;

    this.updateHeight(x);
    this.updateHeight(y);

    return y;
  }

  // Insert a node (conceptual, full implementation is complex)
  insert(data) {
    this.root = this._insert(this.root, data);
  }

  _insert(node, data) {
    // 1. Perform normal BST insertion
    if (node === null) {
      return new AVLNode(data);
    }

    if (data < node.data) {
      node.left = this._insert(node.left, data);
    } else if (data > node.data) {
      node.right = this._insert(node.right, data);
    } else {
      return node; // Duplicate keys not allowed
    }

    // 2. Update height of current node
    this.updateHeight(node);

    // 3. Get the balance factor
    const balance = this.getBalanceFactor(node);

    // 4. Perform rotations if unbalanced

    // Left Left Case
    if (balance > 1 && data < node.left.data) {
      return this.rightRotate(node);
    }

    // Right Right Case
    if (balance < -1 && data > node.right.data) {
      return this.leftRotate(node);
    }

    // Left Right Case
    if (balance > 1 && data > node.left.data) {
      node.left = this.leftRotate(node.left);
      return this.rightRotate(node);
    }

    // Right Left Case
    if (balance < -1 && data < node.right.data) {
      node.right = this.rightRotate(node.right);
      return this.leftRotate(node);
    }

    return node;
  }

  // In-order traversal for verification
  inorder(node) {
    if (node !== null) {
      this.inorder(node.left);
      console.log(node.data);
      this.inorder(node.right);
    }
  }
}

// Example Usage (conceptual, as full implementation is complex):
// const avl = new AVLTree();
// avl.insert(10);
// avl.insert(20);
// avl.insert(30);
// avl.insert(40);
// avl.insert(50);
// avl.insert(25);
// console.log("In-order traversal of AVL tree:");
// avl.inorder(avl.root);
```

## Edge Cases and How to Solve Them

1.  **Empty Tree**: As with any tree, operations on an empty tree (`root` is `null`) must be handled. Insertion into an empty tree simply sets the new node as the root.

2.  **Duplicate Values**: Self-balancing BSTs typically do not allow duplicate values. If a duplicate is inserted, it's usually ignored or an error is returned. If duplicates are needed, the node could store a count, or they could be placed in a specific subtree (e.g., always right).

3.  **Maintaining Balance After Deletion**: Deletion in self-balancing trees is even more complex than insertion. After a node is deleted, the tree might become unbalanced, requiring rotations to restore the balance property. The logic for deletion and subsequent rebalancing is similar to insertion but often involves more cases.

4.  **Rotation Logic**: The core of self-balancing trees lies in their rotation operations (left, right, left-right, right-left). Incorrect implementation of these rotations can lead to an unbalanced tree or a corrupted tree structure. Each rotation must correctly update parent-child pointers and node heights/colors.

5.  **Height/Color Property Violations**: For AVL trees, incorrect height updates or balance factor calculations will lead to violations. For Red-Black trees, failure to maintain any of the five properties (especially the red-red violation or black-height violation) will break the tree's integrity and performance guarantees. Debugging these can be challenging.

6.  **Memory Overhead**: Compared to simple BSTs, self-balancing trees have slightly higher memory overhead per node (e.g., for storing height in AVL or color in Red-Black) and potentially more complex code, but this is a small price for guaranteed performance.

7.  **Choosing the Right Self-Balancing Tree**: The choice between AVL and Red-Black depends on the application's specific needs. If search operations are significantly more frequent than insertions/deletions, AVL might be slightly better. If insertions/deletions are frequent, Red-Black trees are often preferred due to fewer rotations. In many general-purpose scenarios, Red-Black trees are favored due to their slightly simpler implementation and good all-around performance.

## References

1.  [GeeksforGeeks: AVL Tree](https://www.geeksforgeeks.org/avl-tree-set-1-insertion-avl-tree/)
2.  [Wikipedia: AVL tree](https://en.wikipedia.org/wiki/AVL_tree)
3.  [GeeksforGeeks: Red-Black Tree](https://www.geeksforgeeks.org/red-black-tree-set-1-introduction-with-properties/)
4.  [Wikipedia: Red-black tree](https://en.wikipedia.org/wiki/Red-black_tree)
5.  [Visualizing Algorithms: AVL Tree](https://www.cs.usfca.edu/~galles/visualization/AVLTree.html)
6.  [Visualizing Algorithms: Red-Black Tree](https://www.cs.usfca.edu/~galles/visualization/RedBlackTree.html)


