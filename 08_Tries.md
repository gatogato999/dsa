# 08 Tries

## Description

A Trie (pronounced "try"), also known as a prefix tree, is a tree-like data structure used to store a dynamic set or associative array where the keys are usually strings. Unlike a binary search tree, where nodes store the entire key, in a trie, each node represents a common prefix of the keys. The path from the root to a node represents a prefix, and the node itself might mark the end of a complete word.

Key characteristics of Tries:

*   **Nodes represent prefixes**: Each node in a trie stores a character (or a part of a character) and has references to its children nodes, which represent the next characters in a sequence.
*   **Efficient prefix matching**: Searching for a key or a prefix is very fast, proportional to the length of the key, not the number of keys in the trie.
*   **Space efficiency for common prefixes**: Tries can be space-efficient when storing many keys that share common prefixes, as these prefixes are stored only once.
*   **No collisions**: Unlike hash tables, tries do not have hash collisions, which simplifies operations and guarantees worst-case performance.

Tries are commonly used for applications like:

*   **Autocomplete and spell checkers**: Suggesting words based on a typed prefix.
*   **Dictionary search**: Quickly checking if a word exists.
*   **IP routing**: Storing and searching IP addresses.
*   **T9 predictive text**: Mapping number sequences to words.

## Mentality Before Solving

Consider using a trie when:

1.  **String-based Keys with Prefix Operations**: The primary use case for tries is when you have a collection of strings (or sequences) and need to perform operations based on prefixes, such as searching for all words starting with a given prefix, or checking if a word exists.
2.  **Fast Search and Insertion (by key length)**: Tries offer O(L) time complexity for search, insertion, and deletion, where L is the length of the key. This is often faster than O(log N) for balanced BSTs or average O(1) for hash tables if L is small compared to log N or if hash collisions are frequent.
3.  **Space Optimization for Common Prefixes**: If your dataset contains many strings with shared prefixes (e.g., a dictionary of English words), a trie can be more space-efficient than storing each string individually, as common prefixes are represented only once.
4.  **Lexicographical Ordering**: Tries naturally store keys in lexicographical (alphabetical) order, which can be useful for certain applications.
5.  **No Hashing Required**: Unlike hash tables, tries do not require a hash function, avoiding potential issues with hash collisions or poorly designed hash functions.

## Code in JavaScript/Node.js

Here's a basic implementation of a Trie:

```javascript
class TrieNode {
  constructor() {
    this.children = {}; // Map of characters to TrieNodes
    this.isEndOfWord = false; // Marks if this node completes a word
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  /**
   * Inserts a word into the trie.
   * @param {string} word
   */
  insert(word) {
    let current = this.root;
    for (let i = 0; i < word.length; i++) {
      const char = word[i];
      if (!current.children[char]) {
        current.children[char] = new TrieNode();
      }
      current = current.children[char];
    }
    current.isEndOfWord = true;
  }

  /**
   * Returns true if the word is in the trie.
   * @param {string} word
   * @return {boolean}
   */
  search(word) {
    let current = this.root;
    for (let i = 0; i < word.length; i++) {
      const char = word[i];
      if (!current.children[char]) {
        return false;
      }
      current = current.children[char];
    }
    return current.isEndOfWord; // Must be end of word to be a valid search
  }

  /**
   * Returns true if there is any word in the trie that starts with the given prefix.
   * @param {string} prefix
   * @return {boolean}
   */
  startsWith(prefix) {
    let current = this.root;
    for (let i = 0; i < prefix.length; i++) {
      const char = prefix[i];
      if (!current.children[char]) {
        return false;
      }
      current = current.children[char];
    }
    return true; // The prefix path exists
  }

  /**
   * Deletes a word from the trie.
   * @param {string} word
   * @return {boolean} True if the word was successfully deleted, false otherwise.
   */
  delete(word) {
    return this._delete(this.root, word, 0);
  }

  _delete(node, word, index) {
    if (!node) {
      return false;
    }

    if (index === word.length) {
      // Base case: reached the end of the word
      if (!node.isEndOfWord) {
        return false; // Word not found
      }
      node.isEndOfWord = false; // Mark as not end of word
      // Return true if this node has no other children (can be safely removed)
      return Object.keys(node.children).length === 0;
    }

    const char = word[index];
    const childNode = node.children[char];

    if (!childNode) {
      return false; // Word not found
    }

    const shouldDeleteChild = this._delete(childNode, word, index + 1);

    if (shouldDeleteChild) {
      delete node.children[char];
      // Return true if this node is no longer part of any other word
      // and has no other children after deleting the current branch
      return !node.isEndOfWord && Object.keys(node.children).length === 0;
    }

    return false;
  }

  /**
   * Finds all words in the trie that start with the given prefix.
   * @param {string} prefix
   * @return {string[]}
   */
  findAllWordsWithPrefix(prefix) {
    let current = this.root;
    for (let i = 0; i < prefix.length; i++) {
      const char = prefix[i];
      if (!current.children[char]) {
        return []; // No words start with this prefix
      }
      current = current.children[char];
    }

    const words = [];
    this._collectWords(current, prefix, words);
    return words;
  }

  _collectWords(node, currentPrefix, words) {
    if (node.isEndOfWord) {
      words.push(currentPrefix);
    }

    for (const char in node.children) {
      this._collectWords(node.children[char], currentPrefix + char, words);
    }
  }
}

// Example Usage:
const trie = new Trie();
trie.insert("apple");
trie.insert("apricot");
trie.insert("apply");
trie.insert("banana");

console.log("Search for apple:", trie.search("apple")); // Output: true
console.log("Search for app:", trie.search("app"));   // Output: false (not a full word)
console.log("Search for ban:", trie.search("ban"));   // Output: false

console.log("Starts with app:", trie.startsWith("app")); // Output: true
console.log("Starts with bat:", trie.startsWith("bat")); // Output: false

console.log("Words with prefix ap:", trie.findAllWordsWithPrefix("ap")); // Output: [ 'apple', 'apricot', 'apply' ]

trie.delete("apple");
console.log("Search for apple after deletion:", trie.search("apple")); // Output: false
console.log("Starts with ap after apple deletion:", trie.startsWith("ap")); // Output: true (apricot, apply still exist)

trie.delete("apricot");
trie.delete("apply");
console.log("Starts with ap after all deletions:", trie.startsWith("ap")); // Output: false
```

## Edge Cases and How to Solve Them

1.  **Empty Trie**: Operations like `search`, `startsWith`, or `delete` must handle the case where the trie is empty (i.e., `this.root` has no children). The current implementation correctly handles this by returning `false` or an empty array.

2.  **Empty String Input**: If an empty string `""` is inserted, searched, or used as a prefix, how should it be handled? The current `insert` method would mark the root as `isEndOfWord = true`. `search` would return `true` for `""` if the root is marked as a word. `startsWith` would always return `true` for `""`.

    *   **Solution**: Decide if empty strings are valid keys for your application. If so, the current implementation is mostly fine. If not, add checks at the beginning of methods to reject empty strings.

3.  **Non-existent Word/Prefix**: When searching for a word or prefix that doesn't exist, the traversal will eventually hit a node where the next character is not a child. The `search` and `startsWith` methods correctly return `false` in this scenario.

4.  **Deletion of Words**: Deleting a word from a trie is more complex than insertion. Simply setting `isEndOfWord` to `false` is not enough if the node is part of another word or a prefix for other words. The `_delete` recursive helper function handles this by:
    *   Marking `isEndOfWord` as `false`.
    *   Recursively deleting child nodes if they are no longer part of any other word or prefix.
    *   Only deleting a node if it is not an end of word for another word and has no other children.

5.  **Case Sensitivity**: The current implementation is case-sensitive (e.g., "Apple" is different from "apple").

    *   **Solution**: If case-insensitivity is required, convert all input strings to a consistent case (e.g., `word.toLowerCase()`) before inserting or searching.

6.  **Non-alphabetic Characters**: The current implementation assumes alphanumeric characters. If you need to handle spaces, numbers, or special characters, the `children` map will naturally accommodate them, but ensure your input processing is consistent.

7.  **Space Complexity**: While tries can be space-efficient for common prefixes, they can consume a lot of memory if the keys are very long and share few common prefixes, or if the alphabet size is large (leading to many empty `children` maps). For very large alphabets or sparse character sets, consider using a `Map` for `children` (as done in the example) instead of a fixed-size array, or a more compact trie variant like a Radix Tree (Patricia Trie).

## References

1.  [GeeksforGeeks: Trie | (Insert and Search)](https://www.geeksforgeeks.org/trie-insert-and-search/)
2.  [Wikipedia: Trie](https://en.wikipedia.org/wiki/Trie)
3.  [freeCodeCamp: Tries in JavaScript](https://www.freecodecamp.org/news/tries-in-javascript/)


