# 02 Hash Tables / Maps

## Description

A hash table, also known as a hash map, is a data structure that implements an associative array abstract data type, a structure that can map keys to values. A hash table uses a hash function to compute an index, also called a hash code, into an array of buckets or slots, from which the desired value can be found. During lookup, the key is hashed and the resulting hash indicates where the corresponding value is stored.

Ideally, the hash function will assign each key to a unique bucket, but most hash table designs employ an imperfect hash function, which might cause hash collisions where the hash function generates the same index for more than one key. Such collisions must be accommodated in some way.

Common collision resolution techniques include:

*   **Separate Chaining**: Each bucket is independent, and a list of entries that hash to the same bucket is stored there. This list is often a linked list.
*   **Open Addressing**: All entry records are stored in the bucket array itself. When a new entry has to be inserted, the buckets are examined, starting with the hashed-to slot and proceeding in some probe sequence, until an unoccupied slot is found.

Hash tables are widely used because they are efficient. For a well-dimensioned hash table, the average time complexity for each lookup is O(1), independent of the number of elements stored in the table.

## Mentality Before Solving

When deciding whether to use a hash table, consider these points:

1.  **Key-Value Pairs**: The problem should naturally involve storing and retrieving data as key-value pairs. If you need to look up values based on a specific key, a hash table is a strong candidate.
2.  **Fast Lookups, Insertions, and Deletions**: Hash tables offer average O(1) time complexity for these operations, making them extremely efficient for dynamic datasets where these operations are frequent.
3.  **Unordered Data**: Hash tables do not maintain the order of elements. If you need to iterate through elements in a specific order (e.g., sorted by key), other data structures like a balanced binary search tree (which offers O(log n) for lookups, insertions, and deletions) might be more suitable. In JavaScript, however, the `Map` object preserves insertion order.
4.  **Hash Function Quality**: The performance of a hash table depends heavily on the quality of the hash function. A good hash function distributes keys uniformly across the buckets, minimizing collisions. In most high-level languages like JavaScript, the built-in hash functions are well-optimized.
5.  **Space Complexity**: Hash tables can have a higher memory footprint than arrays or linked lists due to the need for empty buckets to reduce collisions and the overhead of storing both keys and values.
6.  **Use Cases**: Common applications include database indexing, caching, implementing sets, and counting frequencies of items.

## Code in JavaScript/Node.js

JavaScript provides a built-in `Map` object that is a highly optimized hash table implementation. It's generally recommended to use the built-in `Map` over a custom implementation for most use cases.

```javascript
// Using the built-in Map object

// 1. Creating a Map
let myMap = new Map();

// 2. Adding Key-Value Pairs
myMap.set("name", "Alice");
myMap.set("age", 30);
myMap.set(true, "isStudent");
myMap.set({ id: 1 }, "userObject");

console.log(myMap);
// Output: Map(4) { 'name' => 'Alice', 'age' => 30, true => 'isStudent', { id: 1 } => 'userObject' }

// 3. Getting Values by Key
console.log(myMap.get("name")); // Output: Alice
console.log(myMap.get("nonExistentKey")); // Output: undefined

// 4. Checking for a Key
console.log(myMap.has("age")); // Output: true
console.log(myMap.has("city")); // Output: false

// 5. Deleting a Key-Value Pair
myMap.delete("age");
console.log(myMap.has("age")); // Output: false

// 6. Getting the Size of the Map
console.log(myMap.size); // Output: 3

// 7. Iterating through a Map
// Using for...of
for (let [key, value] of myMap) {
  console.log(`${key}: ${value}`);
}
// Output:
// name: Alice
// true: isStudent
// { id: 1 }: userObject

// Using forEach
myMap.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});

// Get only keys
for (let key of myMap.keys()) {
  console.log(key);
}

// Get only values
for (let value of myMap.values()) {
  console.log(value);
}

// 8. Clearing the Map
myMap.clear();
console.log(myMap.size); // Output: 0
```

For educational purposes, here is a simplified hash table implementation with separate chaining for collision resolution:

```javascript
class HashTable {
  constructor(size = 50) {
    this.buckets = new Array(size);
    this.size = size;
  }

  // Hash function to determine the index for a key
  _hash(key) {
    let hash = 0;
    for (let i = 0; i < key.length; i++) {
      hash = (hash + key.charCodeAt(i) * i) % this.size;
    }
    return hash;
  }

  // Set a key-value pair
  set(key, value) {
    const index = this._hash(key);
    if (!this.buckets[index]) {
      this.buckets[index] = []; // Create a bucket (linked list) if it doesn't exist
    }
    // Check if key already exists in the bucket to update it
    for (let i = 0; i < this.buckets[index].length; i++) {
      if (this.buckets[index][i][0] === key) {
        this.buckets[index][i][1] = value;
        return;
      }
    }
    this.buckets[index].push([key, value]);
  }

  // Get a value by key
  get(key) {
    const index = this._hash(key);
    const bucket = this.buckets[index];
    if (bucket) {
      for (let i = 0; i < bucket.length; i++) {
        if (bucket[i][0] === key) {
          return bucket[i][1];
        }
      }
    }
    return undefined;
  }

  // Check if a key exists
  has(key) {
    const index = this._hash(key);
    const bucket = this.buckets[index];
    if (bucket) {
      for (let i = 0; i < bucket.length; i++) {
        if (bucket[i][0] === key) {
          return true;
        }
      }
    }
    return false;
  }

  // Delete a key-value pair
  delete(key) {
    const index = this._hash(key);
    const bucket = this.buckets[index];
    if (bucket) {
      for (let i = 0; i < bucket.length; i++) {
        if (bucket[i][0] === key) {
          const deletedValue = bucket[i][1];
          bucket.splice(i, 1);
          return deletedValue;
        }
      }
    }
    return undefined;
  }
}

// Example Usage:
const myHashTable = new HashTable();
myHashTable.set("name", "Bob");
myHashTable.set("age", 42);
myHashTable.set("city", "New York");

console.log(myHashTable.get("name")); // Output: Bob
console.log(myHashTable.get("age")); // Output: 42

console.log(myHashTable.has("city")); // Output: true
console.log(myHashTable.has("country")); // Output: false

myHashTable.delete("age");
console.log(myHashTable.get("age")); // Output: undefined
```

## Edge Cases and How to Solve Them

1.  **Hash Collisions**: This is the most fundamental edge case. A poor hash function can lead to many keys hashing to the same index, degrading performance from O(1) to O(n) in the worst case (if all keys hash to the same bucket). The solution is to use a good hash function that provides a uniform distribution of keys and an effective collision resolution strategy (like separate chaining or open addressing).

2.  **Load Factor and Resizing**: The load factor of a hash table is the ratio of the number of stored elements to the number of buckets. As the load factor increases, so does the probability of collisions. To maintain O(1) performance, hash tables need to be resized (i.e., a new, larger array of buckets is created, and all existing elements are re-hashed into the new array) when the load factor exceeds a certain threshold (e.g., 0.75). This resizing operation is expensive (O(n)) but happens infrequently.

3.  **Non-String Keys**: In many languages, hash tables are optimized for string keys. When using other data types as keys (like objects in JavaScript), you need to be careful. In JavaScript's `Map`, objects are compared by reference, not by value. This means two different objects with the same properties will be treated as distinct keys.

    ```javascript
    let map = new Map();
    let obj1 = { id: 1 };
    let obj2 = { id: 1 };

    map.set(obj1, "value1");
    console.log(map.get(obj1)); // Output: value1
    console.log(map.get(obj2)); // Output: undefined (because obj1 !== obj2)
    ```

    If you need to use objects as keys based on their value, you would need to serialize the object into a consistent string representation to use as the key.

4.  **Mutable Keys**: If a key is an object and it is mutated after being inserted into the hash table, its hash code might change. This can make it impossible to find the entry again, as the lookup will start at a different bucket. It's generally a bad practice to mutate keys after they have been used in a hash table. If you must, you should remove the old key-value pair, mutate the key, and then re-insert it.

5.  **Iteration Order**: As mentioned, standard hash tables do not guarantee any specific order of iteration. If order is important, use a data structure that provides it, such as a linked hash map (which maintains insertion order) or a balanced binary search tree (which maintains sorted order). JavaScript's `Map` object is an exception as it does preserve insertion order.

## References

1.  [Wikipedia: Hash table](https://en.wikipedia.org/wiki/Hash_table)
2.  [MDN Web Docs: Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
3.  [GeeksforGeeks: Hashing | Set 1 (Introduction)](https://www.geeksforgeeks.org/hashing-set-1-introduction/)
4.  [freeCodeCamp: Hash Tables in JavaScript](https://www.freecodecamp.org/news/hash-tables-in-javascript/)


