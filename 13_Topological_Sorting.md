# 13 Topological Sorting

## Description

Topological sorting (also known as topological order or topological sort) of a directed acyclic graph (DAG) is a linear ordering of its vertices such that for every directed edge `u -> v`, vertex `u` comes before vertex `v` in the ordering. A topological sort is possible only for DAGs, as a cycle would imply that there is no such linear ordering.

Topological sorting has many applications, especially in scheduling problems where tasks have dependencies. For example, if you have a set of tasks to complete, and some tasks must be finished before others can start, a topological sort can give you a valid order to perform them.

There are two main algorithms for topological sorting:

*   **Kahn's Algorithm (BFS-based)**: This algorithm works by iteratively finding vertices with an in-degree of 0 (no incoming edges), adding them to the topological sort, and then removing them and their outgoing edges from the graph. This process is repeated until all vertices are added to the sort.
*   **DFS-based Algorithm**: This algorithm uses Depth-First Search. When performing a DFS, a vertex is added to the topological sort list *after* all its adjacent vertices have been visited. This means the vertices are added in reverse order of their finishing times in the DFS traversal.

## Mentality Before Solving

Consider using Topological Sorting when:

1.  **Task Scheduling with Dependencies**: The problem involves a set of tasks or events where some must precede others. Examples include course scheduling (prerequisites), build systems (compiling modules), or job scheduling.
2.  **Directed Acyclic Graph (DAG)**: The problem can be modeled as a directed graph, and it is guaranteed (or you need to check) that there are no cycles. If cycles exist, a topological sort is not possible.
3.  **Linear Ordering Requirement**: You need a valid linear sequence of elements that respects all given dependencies.

## Code in JavaScript/Node.js

We'll use a graph representation suitable for directed edges.

### 1. Kahn's Algorithm (BFS-based)

*   **Description**: Maintains a count of in-degrees for each vertex. It starts by adding all vertices with an in-degree of 0 to a queue. Then, it repeatedly dequeues a vertex, adds it to the result, and decrements the in-degree of its neighbors. If a neighbor's in-degree becomes 0, it's enqueued.
*   **Time Complexity**: O(V + E), where V is the number of vertices and E is the number of edges.

```javascript
// Graph class for directed graphs
class DirectedGraph {
  constructor() {
    this.adjacencyList = {};
  }

  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      this.adjacencyList[vertex] = [];
    }
  }

  addEdge(fromVertex, toVertex) {
    if (!this.adjacencyList[fromVertex] || !this.adjacencyList[toVertex]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[fromVertex].push(toVertex);
  }

  getAllVertices() {
    return Object.keys(this.adjacencyList);
  }
}

function kahnTopologicalSort(graph) {
  const inDegree = {};
  const vertices = graph.getAllVertices();

  // Initialize in-degrees for all vertices to 0
  for (let vertex of vertices) {
    inDegree[vertex] = 0;
  }

  // Calculate in-degrees for all vertices
  for (let vertex of vertices) {
    for (let neighbor of graph.adjacencyList[vertex] || []) {
      inDegree[neighbor]++;
    }
  }

  const queue = [];
  // Add all vertices with in-degree 0 to the queue
  for (let vertex of vertices) {
    if (inDegree[vertex] === 0) {
      queue.push(vertex);
    }
  }

  const topologicalOrder = [];
  let visitedCount = 0;

  while (queue.length > 0) {
    const currentVertex = queue.shift(); // Dequeue
    topologicalOrder.push(currentVertex);
    visitedCount++;

    // For each neighbor of the current vertex
    for (let neighbor of graph.adjacencyList[currentVertex] || []) {
      inDegree[neighbor]--;
      // If neighbor's in-degree becomes 0, enqueue it
      if (inDegree[neighbor] === 0) {
        queue.push(neighbor);
      }
    }
  }

  // Check for cycle
  if (visitedCount !== vertices.length) {
    console.error("Graph contains a cycle, topological sort not possible!");
    return null; // Or throw an error
  }

  return topologicalOrder;
}

// Example Usage:
// const graph1 = new DirectedGraph();
// graph1.addVertex("A"); graph1.addVertex("B"); graph1.addVertex("C");
// graph1.addVertex("D"); graph1.addVertex("E"); graph1.addVertex("F");

// graph1.addEdge("A", "D");
// graph1.addEdge("B", "D");
// graph1.addEdge("C", "A");
// graph1.addEdge("C", "B");
// graph1.addEdge("D", "E");
// graph1.addEdge("E", "F");

// console.log("Kahn's Topological Sort:", kahnTopologicalSort(graph1));
// Expected Output: [ 'C', 'A', 'B', 'D', 'E', 'F' ] (or similar valid order)

// Example with a cycle:
// const graphWithCycle = new DirectedGraph();
// graphWithCycle.addVertex("A"); graphWithCycle.addVertex("B"); graphWithCycle.addVertex("C");
// graphWithCycle.addEdge("A", "B");
// graphWithCycle.addEdge("B", "C");
// graphWithCycle.addEdge("C", "A"); // Cycle A -> B -> C -> A
// console.log("Kahn's Topological Sort (with cycle):");
// console.log(kahnTopologicalSort(graphWithCycle)); // Output: null and error message
```

### 2. DFS-based Algorithm

*   **Description**: Performs a DFS traversal. When a vertex is finished (i.e., all its neighbors have been visited and processed), it is added to the front of a list (or pushed to a stack and then reversed). This ensures that a vertex appears before its dependents.
*   **Time Complexity**: O(V + E).

```javascript
function dfsTopologicalSort(graph) {
  const visited = new Set();
  const recursionStack = new Set(); // To detect cycles
  const topologicalOrder = [];
  const vertices = graph.getAllVertices();

  function dfsVisit(vertex) {
    visited.add(vertex);
    recursionStack.add(vertex);

    for (let neighbor of graph.adjacencyList[vertex] || []) {
      if (!visited.has(neighbor)) {
        if (!dfsVisit(neighbor)) { // If a cycle is detected in a recursive call
          return false;
        }
      } else if (recursionStack.has(neighbor)) {
        // Back edge found, means a cycle
        console.error("Graph contains a cycle, topological sort not possible!");
        return false;
      }
    }

    recursionStack.delete(vertex); // Remove from recursion stack as all its descendants are visited
    topologicalOrder.unshift(vertex); // Add to the front of the list
    return true;
  }

  for (let vertex of vertices) {
    if (!visited.has(vertex)) {
      if (!dfsVisit(vertex)) {
        return null; // Cycle detected, return null
      }
    }
  }

  return topologicalOrder;
}

// Example Usage:
// const graph2 = new DirectedGraph();
// graph2.addVertex("A"); graph2.addVertex("B"); graph2.addVertex("C");
// graph2.addVertex("D"); graph2.addVertex("E"); graph2.addVertex("F");

// graph2.addEdge("A", "D");
// graph2.addEdge("B", "D");
// graph2.addEdge("C", "A");
// graph2.addEdge("C", "B");
// graph2.addEdge("D", "E");
// graph2.addEdge("E", "F");

// console.log("DFS-based Topological Sort:", dfsTopologicalSort(graph2));
// Expected Output: [ 'C', 'B', 'A', 'D', 'E', 'F' ] (or similar valid order)

// Example with a cycle:
// const graphWithCycle2 = new DirectedGraph();
// graphWithCycle2.addVertex("A"); graphWithCycle2.addVertex("B"); graphWithCycle2.addVertex("C");
// graphWithCycle2.addEdge("A", "B");
// graphWithCycle2.addEdge("B", "C");
// graphWithCycle2.addEdge("C", "A"); // Cycle A -> B -> C -> A
// console.log("DFS-based Topological Sort (with cycle):");
// console.log(dfsTopologicalSort(graphWithCycle2)); // Output: null and error message
```

## Edge Cases and How to Solve Them

1.  **Graph with Cycles**: Topological sort is only possible for Directed Acyclic Graphs (DAGs). Both algorithms must detect and report cycles.

    *   **Kahn's Algorithm**: A cycle is detected if `visitedCount` (number of vertices added to the topological order) is less than the total number of vertices (`vertices.length`) at the end. This means some vertices could not be processed because their in-degrees never reached 0.
    *   **DFS-based Algorithm**: A cycle is detected if, during DFS, a `neighbor` is encountered that is already in the `recursionStack`. This indicates a back edge, forming a cycle.

2.  **Disconnected Graph**: Both algorithms can handle disconnected DAGs. They will produce a valid topological order for the entire graph, but the relative order of vertices in different connected components might vary depending on the starting vertex for traversal.

3.  **Empty Graph**: If the graph has no vertices, the algorithms should return an empty list.

    *   **Solution**: The current implementations handle this gracefully; `getAllVertices()` will return an empty array, and the loops will not execute, resulting in an empty `topologicalOrder`.

4.  **Non-existent Vertices in Edges**: If `addEdge` is called with vertices that haven't been added via `addVertex`, it can lead to errors or unexpected behavior.

    *   **Solution**: The `addEdge` method in `DirectedGraph` includes checks for non-existent vertices.

5.  **Multiple Valid Topological Orders**: For a given DAG, there might be multiple valid topological sorts. Both algorithms will produce one such valid order. The specific order might depend on the order of iteration through adjacency lists or the order of vertices in the `getAllVertices()` array.

6.  **Self-Loops**: A self-loop (`u -> u`) is a cycle of length 1. This would make the graph non-acyclic and thus not amenable to topological sorting. Both algorithms would detect this as a cycle.

## References

1.  [GeeksforGeeks: Topological Sort](https://www.geeksforgeeks.org/topological-sorting-dfs/)
2.  [Wikipedia: Topological sorting](https://en.wikipedia.org/wiki/Topological_sorting)
3.  [CP-Algorithms: Topological sort](https://cp-algorithms.com/graph/topological-sort.html)
4.  [Visualizing Algorithms: Topological Sort](https://www.cs.usfca.edu/~galles/visualization/TopoSort.html)


