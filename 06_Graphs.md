# 06 Graphs

## Description

A graph is a non-linear data structure consisting of a finite set of vertices (or nodes) and a set of edges that connect pairs of vertices. Graphs are used to model many real-world systems, such as social networks, road networks, computer networks, and dependencies between tasks.

Key terminology:
*   **Vertex (Node)**: A fundamental entity in a graph, representing an item or a point.
*   **Edge (Arc)**: A connection between two vertices. Edges can be directed or undirected.
*   **Directed Graph (Digraph)**: Edges have a direction, meaning the connection goes from one vertex to another in a specific way (e.g., one-way street).
*   **Undirected Graph**: Edges have no direction; the connection is bidirectional (e.g., a two-way street).
*   **Weighted Graph**: Each edge has a numerical value (weight or cost) associated with it, representing distance, time, cost, etc.
*   **Path**: A sequence of vertices connected by edges.
*   **Cycle**: A path that starts and ends at the same vertex.
*   **Connected Graph**: A graph where there is a path between every pair of vertices.
*   **Degree**: The number of edges incident to a vertex (in undirected graphs). For directed graphs, it's in-degree (incoming edges) and out-degree (outgoing edges).

Graphs can be represented in several ways:

*   **Adjacency Matrix**: A 2D array where `matrix[i][j]` is 1 (or weight) if there's an edge from vertex `i` to vertex `j`, and 0 otherwise. Good for dense graphs (many edges) and quick checks for edge existence.
*   **Adjacency List**: An array or hash map where each index/key represents a vertex, and its value is a list of its adjacent vertices. Good for sparse graphs (few edges) and efficient traversal of neighbors.

## Mentality Before Solving

When a problem involves relationships or connections between entities, a graph is often the most appropriate data structure. Consider the following:

1.  **Relationships and Connections**: Is the core of the problem about how different entities are connected or interact? Examples include finding the shortest path, determining reachability, or identifying dependencies.
2.  **Network Modeling**: If you're modeling a network of any kind (social, transportation, communication), graphs are the natural choice.
3.  **Traversal and Reachability**: Problems that require visiting all connected components, finding paths, or determining if one point can reach another are prime candidates for graph algorithms (e.g., BFS, DFS).
4.  **Optimization Problems**: Many optimization problems, such as finding the shortest path (Dijkstra, Bellman-Ford), minimum spanning trees (Prim, Kruskal), or maximum flow, are solved using graph algorithms.
5.  **Directed vs. Undirected**: Carefully determine if the relationships are one-way or two-way. This impacts how you build the graph (directed or undirected edges).
6.  **Weighted vs. Unweighted**: If connections have associated costs or values, use a weighted graph. This is crucial for algorithms like shortest path.
7.  **Density**: Consider whether the graph is dense (many edges) or sparse (few edges). This influences the choice of representation (adjacency matrix vs. adjacency list) for optimal performance.

## Code in JavaScript/Node.js

Here's an example of a simple graph implementation using an Adjacency List, which is generally preferred for most real-world graphs as they tend to be sparse.

```javascript
class Graph {
  constructor() {
    this.adjacencyList = {}; // Using an object (hash map) to store vertices and their neighbors
  }

  // Add a vertex to the graph
  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      this.adjacencyList[vertex] = [];
    }
  }

  // Add an edge between two vertices (undirected graph)
  addEdge(vertex1, vertex2) {
    if (!this.adjacencyList[vertex1] || !this.adjacencyList[vertex2]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[vertex1].push(vertex2);
    this.adjacencyList[vertex2].push(vertex1); // For undirected graph
  }

  // Add a directed edge
  addDirectedEdge(fromVertex, toVertex) {
    if (!this.adjacencyList[fromVertex] || !this.adjacencyList[toVertex]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[fromVertex].push(toVertex);
  }

  // Remove an edge between two vertices (undirected graph)
  removeEdge(vertex1, vertex2) {
    if (!this.adjacencyList[vertex1] || !this.adjacencyList[vertex2]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[vertex1] = this.adjacencyList[vertex1].filter(
      (v) => v !== vertex2
    );
    this.adjacencyList[vertex2] = this.adjacencyList[vertex2].filter(
      (v) => v !== vertex1
    );
  }

  // Remove a vertex and all its incident edges
  removeVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      console.error("Vertex not found.");
      return;
    }
    // Remove edges connected to this vertex
    for (let adjacentVertex of this.adjacencyList[vertex]) {
      this.removeEdge(vertex, adjacentVertex); // This will remove both directions for undirected
    }
    delete this.adjacencyList[vertex];
  }

  // Depth-First Search (DFS) - Recursive
  dfsRecursive(startVertex, visited = new Set()) {
    console.log(startVertex);
    visited.add(startVertex);

    for (let neighbor of this.adjacencyList[startVertex]) {
      if (!visited.has(neighbor)) {
        this.dfsRecursive(neighbor, visited);
      }
    }
  }

  // Breadth-First Search (BFS)
  bfs(startVertex) {
    const queue = [startVertex];
    const visited = new Set();
    visited.add(startVertex);
    let result = [];

    while (queue.length > 0) {
      const currentVertex = queue.shift();
      result.push(currentVertex);

      for (let neighbor of this.adjacencyList[currentVertex]) {
        if (!visited.has(neighbor)) {
          visited.add(neighbor);
          queue.push(neighbor);
        }
      }
    }
    console.log(result.join(" "));
  }

  // Print the graph
  printGraph() {
    for (let vertex in this.adjacencyList) {
      console.log(`${vertex} -> ${this.adjacencyList[vertex].join(", ")}`);
    }
  }
}

// Example Usage:
const graph = new Graph();

graph.addVertex("A");
graph.addVertex("B");
graph.addVertex("C");
graph.addVertex("D");
graph.addVertex("E");
graph.addVertex("F");

graph.addEdge("A", "B");
graph.addEdge("A", "C");
graph.addEdge("B", "D");
graph.addEdge("C", "E");
graph.addEdge("D", "E");
graph.addEdge("D", "F");
graph.addEdge("E", "F");

console.log("Graph representation:");
graph.printGraph();
// Output:
// A -> B, C
// B -> A, D
// C -> A, E
// D -> B, E, F
// E -> C, D, F
// F -> D, E

console.log("\nDFS Traversal (starting from A):");
graph.dfsRecursive("A");
// Output: A B D E C F (order may vary slightly based on adjacency list order)

console.log("\nBFS Traversal (starting from A):");
graph.bfs("A");
// Output: A B C D E F

console.log("\nRemoving edge D-F:");
graph.removeEdge("D", "F");
graph.printGraph();

console.log("\nRemoving vertex E:");
graph.removeVertex("E");
graph.printGraph();
```

## Edge Cases and How to Solve Them

1.  **Disconnected Graph**: A graph might consist of multiple disconnected components. Standard traversal algorithms (BFS, DFS) starting from a single vertex will only visit nodes within that connected component. To visit all nodes in a disconnected graph, you need to iterate through all vertices and start a traversal from any unvisited vertex.

    ```javascript
    // To traverse all components of a disconnected graph
    traverseAll(graph) {
      const visited = new Set();
      for (let vertex in graph.adjacencyList) {
        if (!visited.has(vertex)) {
          graph.dfsRecursive(vertex, visited); // Or graph.bfs(vertex)
        }
      }
    }
    ```

2.  **Self-Loops and Parallel Edges**: Some graph problems might allow self-loops (an edge from a vertex to itself) or parallel edges (multiple edges between the same two vertices). Your graph representation and algorithms should account for these if they are relevant to the problem. Adjacency lists naturally handle parallel edges by simply adding the destination vertex multiple times. Self-loops are also straightforward to add.

3.  **Non-existent Vertices/Edges**: Operations like `addEdge`, `removeEdge`, `removeVertex`, or traversals should gracefully handle cases where the specified vertex or edge does not exist in the graph. This typically involves checking `if (!this.adjacencyList[vertex])` before proceeding.

4.  **Weighted Graphs**: If edges have weights, the `addEdge` method needs to store this weight (e.g., `this.adjacencyList[vertex1].push({ node: vertex2, weight: weight });`). Graph algorithms like Dijkstra's or Prim's specifically use these weights.

5.  **Cycles**: Detecting cycles is a common graph problem. DFS can be used to detect cycles in both directed and undirected graphs. For directed graphs, a cycle is detected if DFS encounters a visited node that is still in the current recursion stack. For undirected graphs, a cycle is detected if DFS encounters a visited node that is not the immediate parent of the current node.

6.  **Graph Mutability During Traversal**: Modifying a graph (adding/removing vertices or edges) while performing a traversal can lead to unexpected behavior or errors. It's generally best to complete the traversal before making structural changes or to use a snapshot of the graph if modifications are necessary during traversal.

7.  **Large Graphs and Memory**: For very large graphs, the choice of representation (adjacency list vs. matrix) becomes critical for memory efficiency. Adjacency lists are more memory-efficient for sparse graphs, while adjacency matrices can consume a lot of memory for sparse graphs (O(V^2)).

## References

1.  [GeeksforGeeks: Graph Data Structure](https://www.geeksforgeeks.org/graph-data-structure-and-algorithms/)
2.  [Wikipedia: Graph (discrete mathematics)](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics))
3.  [freeCodeCamp: Graph Data Structure in JavaScript](https://www.freecodecamp.org/news/graph-data-structure-in-javascript/)
4.  [Visualizing Algorithms: Graph Traversal](https://www.cs.usfca.edu/~galles/visualization/BFS.html)


