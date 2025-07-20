# 02 Graph Traversal Algorithms (Breadth-First Search, Depth-First Search)

## Description

Graph traversal algorithms are techniques used to visit (check and/or update) each node in a graph exactly once. These algorithms are fundamental for solving many graph-related problems, such as finding paths, detecting cycles, checking connectivity, and more.

The two most common graph traversal algorithms are:

*   **Breadth-First Search (BFS)**: Explores a graph level by level. It starts at a source node, then visits all its immediate neighbors, then all their unvisited neighbors, and so on. BFS uses a queue data structure to manage the order of nodes to visit.
*   **Depth-First Search (DFS)**: Explores a graph as far as possible along each branch before backtracking. It starts at a source node, then explores one of its neighbors, then that neighbor's neighbor, and so on, until it reaches a dead end. DFS typically uses a stack (explicitly or implicitly via recursion) to manage the order of nodes to visit.

## Mentality Before Solving

When deciding between BFS and DFS for a graph problem, consider:

1.  **Shortest Path (Unweighted Graph)**: If you need to find the shortest path (in terms of number of edges) between two nodes in an unweighted graph, BFS is the ideal choice because it explores nodes layer by layer, guaranteeing that the first time you reach a target node, it's via the shortest path.
2.  **Connectivity and Components**: Both BFS and DFS can be used to find connected components in a graph, determine if a graph is connected, or check reachability between two nodes.
3.  **Cycle Detection**: DFS is often preferred for cycle detection in graphs. In a directed graph, a cycle is detected if DFS encounters a visited node that is still in the current recursion stack. In an undirected graph, a cycle is detected if DFS encounters a visited node that is not the immediate parent of the current node.
4.  **Topological Sorting**: DFS is used for topological sorting of Directed Acyclic Graphs (DAGs).
5.  **Memory Usage**: BFS can consume more memory than DFS for wide graphs (many nodes at the same level) because it needs to store all nodes at the current level in the queue. DFS can consume more memory for deep graphs (long paths) due to the recursion stack.
6.  **All Paths/Backtracking**: DFS is generally more suitable for problems that involve exploring all possible paths or require backtracking, such as finding all paths between two nodes, solving mazes, or solving puzzles like Sudoku.

## Code in JavaScript/Node.js

We'll use the `Graph` class from `06_Graphs.md` as a base, which uses an adjacency list representation.

```javascript
// Assuming the Graph class from 06_Graphs.md is available:
// class Graph {
//   constructor() {
//     this.adjacencyList = {};
//   }
//   addVertex(vertex) { /* ... */ }
//   addEdge(vertex1, vertex2) { /* ... */ }
//   // ... other graph methods
// }

// Breadth-First Search (BFS)
function bfs(graph, startVertex) {
  const queue = [startVertex];
  const visited = new Set();
  visited.add(startVertex);
  const result = [];

  while (queue.length > 0) {
    const currentVertex = queue.shift(); // Dequeue
    result.push(currentVertex);

    // Get neighbors of the current vertex
    const neighbors = graph.adjacencyList[currentVertex];
    if (neighbors) {
      for (let neighbor of neighbors) {
        if (!visited.has(neighbor)) {
          visited.add(neighbor);
          queue.push(neighbor); // Enqueue unvisited neighbor
        }
      }
    }
  }
  return result;
}

// Depth-First Search (DFS) - Recursive
function dfsRecursive(graph, startVertex, visited = new Set(), result = []) {
  visited.add(startVertex);
  result.push(startVertex);

  const neighbors = graph.adjacencyList[startVertex];
  if (neighbors) {
    for (let neighbor of neighbors) {
      if (!visited.has(neighbor)) {
        dfsRecursive(graph, neighbor, visited, result);
      }
    }
  }
  return result;
}

// Depth-First Search (DFS) - Iterative
function dfsIterative(graph, startVertex) {
  const stack = [startVertex];
  const visited = new Set();
  visited.add(startVertex);
  const result = [];

  while (stack.length > 0) {
    const currentVertex = stack.pop(); // Pop from stack
    result.push(currentVertex);

    // Get neighbors of the current vertex
    // Note: For consistent output with recursive DFS, process neighbors in reverse order
    // if adjacency list is ordered, or just iterate as is.
    const neighbors = graph.adjacencyList[currentVertex];
    if (neighbors) {
      // Iterating in reverse ensures that the 'first' neighbor (in natural order) is processed last (popped first)
      // matching the recursive call order more closely.
      for (let i = neighbors.length - 1; i >= 0; i--) {
        const neighbor = neighbors[i];
        if (!visited.has(neighbor)) {
          visited.add(neighbor);
          stack.push(neighbor);
        }
      }
    }
  }
  return result;
}

// Example Usage (using the Graph class from 06_Graphs.md):
// const graph = new Graph();
// graph.addVertex("A");
// graph.addVertex("B");
// graph.addVertex("C");
// graph.addVertex("D");
// graph.addVertex("E");
// graph.addVertex("F");

// graph.addEdge("A", "B");
// graph.addEdge("A", "C");
// graph.addEdge("B", "D");
// graph.addEdge("C", "E");
// graph.addEdge("D", "E");
// graph.addEdge("D", "F");
// graph.addEdge("E", "F");

// console.log("BFS Traversal (starting from A):");
// console.log(bfs(graph, "A")); // Output: [ 'A', 'B', 'C', 'D', 'E', 'F' ]

// console.log("\nDFS Recursive Traversal (starting from A):");
// console.log(dfsRecursive(graph, "A")); // Output: [ 'A', 'B', 'D', 'E', 'C', 'F' ] (order depends on adjacency list order)

// console.log("\nDFS Iterative Traversal (starting from A):");
// console.log(dfsIterative(graph, "A")); // Output: [ 'A', 'C', 'E', 'F', 'D', 'B' ] (order depends on stack pop order)
```

## Edge Cases and How to Solve Them

1.  **Disconnected Graph**: If the graph is disconnected, a traversal starting from a single vertex will only visit nodes within that connected component. To traverse all nodes in a disconnected graph, you need to iterate through all vertices and start a traversal from any unvisited vertex.

    ```javascript
    function traverseAllComponents(graph) {
      const visited = new Set();
      const allTraversalResults = [];
      for (let vertex in graph.adjacencyList) {
        if (!visited.has(vertex)) {
          // Use either bfs or dfs here
          allTraversalResults.push(dfsRecursive(graph, vertex, visited));
        }
      }
      return allTraversalResults;
    }

    // Example:
    // const disconnectedGraph = new Graph();
    // disconnectedGraph.addVertex("A"); disconnectedGraph.addVertex("B");
    // disconnectedGraph.addVertex("X"); disconnectedGraph.addVertex("Y");
    // disconnectedGraph.addEdge("A", "B");
    // disconnectedGraph.addEdge("X", "Y");
    // console.log(traverseAllComponents(disconnectedGraph));
    // Output: [ [ 'A', 'B' ], [ 'X', 'Y' ] ]
    ```

2.  **Non-existent Start Vertex**: If the `startVertex` provided to BFS or DFS does not exist in the graph, the algorithms should handle this gracefully (e.g., by returning an empty array or throwing an error).

    ```javascript
    function bfs(graph, startVertex) {
      if (!graph.adjacencyList[startVertex]) {
        console.error("Start vertex not found in graph.");
        return [];
      }
      // ... rest of BFS logic
    }
    ```

3.  **Graphs with Cycles**: Both BFS and DFS handle cycles correctly by using a `visited` set (or array) to keep track of already visited nodes. This prevents infinite loops in cyclic graphs.

4.  **Directed vs. Undirected Graphs**: The traversal algorithms work for both directed and undirected graphs. The difference lies in how the `Graph` class's `addEdge` method is implemented (whether it adds edges in one direction or both).

5.  **Self-Loops and Parallel Edges**: These are handled naturally by the adjacency list representation and the traversal logic, as they are just treated as additional connections from a node.

6.  **Performance for Large Graphs**: For very large graphs, the choice of traversal method and graph representation can impact performance. Adjacency lists are generally preferred for sparse graphs, while adjacency matrices might be better for dense graphs. Recursive DFS can lead to stack overflow for very deep graphs; in such cases, an iterative DFS (using an explicit stack) is safer.

7.  **Weighted Graphs**: BFS and DFS themselves do not directly use edge weights. If weights are important (e.g., for shortest path problems in weighted graphs), you would need algorithms like Dijkstra's or Bellman-Ford, which build upon traversal concepts but incorporate weight considerations.

## References

1.  [GeeksforGeeks: BFS Traversal](https://www.geeksforgeeks.org/breadth-first-search-bfs-for-a-graph/)
2.  [GeeksforGeeks: DFS Traversal](https://www.geeksforgeeks.org/depth-first-search-dfs-for-a-graph/)
3.  [Wikipedia: Breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search)
4.  [Wikipedia: Depth-first search](https://en.wikipedia.org/wiki/Depth-first_search)
5.  [Visualizing Algorithms: BFS](https://www.cs.usfca.edu/~galles/visualization/BFS.html)
6.  [Visualizing Algorithms: DFS](https://www.cs.usfca.edu/~galles/visualization/DFS.html)


