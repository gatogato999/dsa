# 05 Dijkstra's Algorithm

## Description

Dijkstra's algorithm is a single-source shortest path algorithm for a graph with non-negative edge weights. It finds the shortest paths from a single source vertex to all other vertices in the graph. It is a greedy algorithm that works by iteratively expanding the set of visited nodes, always choosing the unvisited node with the smallest known distance from the source.

The algorithm maintains a set of visited vertices and a distance array (or map) that stores the shortest distance found so far from the source to each vertex. Initially, the distance to the source is 0, and to all other vertices is infinity. At each step, it selects the unvisited vertex with the minimum distance, marks it as visited, and then updates the distances of its neighbors.

## Mentality Before Solving

Consider using Dijkstra's algorithm when:

1.  **Shortest Path in Weighted Graphs**: The primary use case is finding the shortest path from a single starting point to all other points in a graph where edges have non-negative weights. If edge weights can be negative, Dijkstra's algorithm will not work correctly, and you should consider Bellman-Ford or SPFA.
2.  **Non-Negative Edge Weights**: This is a crucial constraint. If any edge has a negative weight, Dijkstra's algorithm might produce incorrect results because its greedy approach assumes that once a vertex's shortest path is finalized, it won't be improved by later paths involving negative edges.
3.  **Graph Representation**: Dijkstra's algorithm can be implemented efficiently using an adjacency list representation for the graph, especially when combined with a min-priority queue to efficiently extract the unvisited vertex with the minimum distance. Without a priority queue, the complexity increases.
4.  **Real-world Applications**: Widely used in network routing protocols (e.g., OSPF), GPS navigation systems (finding the shortest route), and other applications where optimal paths are needed.

## Code in JavaScript/Node.js

We'll use a graph representation that includes weights for edges. A simple way to do this is to store neighbors as objects with `node` and `weight` properties in the adjacency list.

```javascript
// Graph class with weighted edges
class WeightedGraph {
  constructor() {
    this.adjacencyList = {};
  }

  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      this.adjacencyList[vertex] = [];
    }
  }

  addEdge(vertex1, vertex2, weight) {
    if (!this.adjacencyList[vertex1] || !this.adjacencyList[vertex2]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[vertex1].push({ node: vertex2, weight });
    this.adjacencyList[vertex2].push({ node: vertex1, weight }); // For undirected graph
  }

  // For directed graph
  addDirectedEdge(fromVertex, toVertex, weight) {
    if (!this.adjacencyList[fromVertex] || !this.adjacencyList[toVertex]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[fromVertex].push({ node: toVertex, weight });
  }
}

// Min-Priority Queue (simplified for this example, a full implementation would be in 07_Heaps.md)
// For Dijkstra, we need to extract the node with the smallest distance.
// A simple array can work for small graphs, but a MinHeap is more efficient.
class MinPriorityQueue {
  constructor() {
    this.values = [];
  }

  enqueue(val, priority) {
    this.values.push({ val, priority });
    this.sort(); // Naive sort, for real use, implement a MinHeap
  }

  dequeue() {
    return this.values.shift();
  }

  sort() {
    this.values.sort((a, b) => a.priority - b.priority);
  }

  isEmpty() {
    return this.values.length === 0;
  }
}

function dijkstra(graph, startVertex) {
  const distances = {};
  const previous = {};
  const pq = new MinPriorityQueue();

  // Initialize distances and priority queue
  for (let vertex in graph.adjacencyList) {
    if (vertex === startVertex) {
      distances[vertex] = 0;
      pq.enqueue(vertex, 0);
    } else {
      distances[vertex] = Infinity;
      pq.enqueue(vertex, Infinity);
    }
    previous[vertex] = null;
  }

  let path = [];
  let smallest;

  while (!pq.isEmpty()) {
    smallest = pq.dequeue().val;

    if (smallest === null || distances[smallest] === Infinity) {
      // No path to remaining vertices
      break;
    }

    for (let neighbor of graph.adjacencyList[smallest]) {
      // Calculate new distance to neighbor
      let candidate = distances[smallest] + neighbor.weight;
      let nextNeighbor = neighbor.node;

      // If a shorter path to neighbor is found
      if (candidate < distances[nextNeighbor]) {
        distances[nextNeighbor] = candidate;
        previous[nextNeighbor] = smallest;
        pq.enqueue(nextNeighbor, candidate); // Re-enqueue with updated priority
      }
    }
  }

  // Reconstruct path (optional, but common for shortest path problems)
  // This part reconstructs path from startVertex to all other vertices
  const shortestPaths = {};
  for (let vertex in graph.adjacencyList) {
    let current = vertex;
    let pathStack = [];
    while (previous[current]) {
      pathStack.push(current);
      current = previous[current];
    }
    if (current === startVertex) {
      pathStack.push(startVertex);
      shortestPaths[vertex] = pathStack.reverse();
    } else {
      shortestPaths[vertex] = null; // No path from startVertex
    }
  }

  return { distances, shortestPaths };
}

// Example Usage:
const graph = new WeightedGraph();
graph.addVertex("A");
graph.addVertex("B");
graph.addVertex("C");
graph.addVertex("D");
graph.addVertex("E");
graph.addVertex("F");

graph.addEdge("A", "B", 4);
graph.addEdge("A", "C", 2);
graph.addEdge("B", "E", 3);
graph.addEdge("C", "D", 2);
graph.addEdge("C", "F", 4);
graph.addEdge("D", "E", 3);
graph.addEdge("D", "F", 1);
graph.addEdge("E", "F", 1);

const result = dijkstra(graph, "A");
console.log("Distances from A:", result.distances);
// Expected Output: { A: 0, B: 4, C: 2, D: 4, E: 7, F: 5 }

console.log("Shortest Paths from A:", result.shortestPaths);
// Expected Output (example for F): { F: [ 'A', 'C', 'D', 'F' ] }
```

## Edge Cases and How to Solve Them

1.  **Negative Edge Weights**: Dijkstra's algorithm fails with negative edge weights. If a negative edge is present, it might lead to a situation where a shorter path is found to an already 


visited and finalized vertex. This violates the greedy choice property. 

    *   **Solution**: If negative edge weights are possible, use the Bellman-Ford algorithm (for single source, handles negative cycles) or the SPFA algorithm. If only non-negative weights are guaranteed, Dijkstra's is appropriate.

2.  **Disconnected Graph**: If some vertices are unreachable from the `startVertex`, their distances will remain `Infinity`, and their `previous` entry will remain `null`. The algorithm correctly identifies this.

    *   **Solution**: The current implementation correctly handles this. The `shortestPaths` object will show `null` for unreachable vertices.

3.  **Non-existent Start Vertex**: If the `startVertex` provided does not exist in the graph, the initialization loop will not find it, and the `distances` and `previous` objects will not be populated correctly for the start vertex. This will lead to incorrect behavior.

    *   **Solution**: Add a check at the beginning of the `dijkstra` function to ensure `startVertex` exists in `graph.adjacencyList`.

    ```javascript
    function dijkstra(graph, startVertex) {
      if (!graph.adjacencyList[startVertex]) {
        console.error("Start vertex not found in graph.");
        return { distances: {}, shortestPaths: {} };
      }
      // ... rest of the function
    }
    ```

4.  **Graph with No Edges**: If the graph has vertices but no edges, only the `startVertex` will have a distance of 0, and all other vertices will remain at `Infinity`. This is correct.

5.  **Large Number of Vertices/Edges**: For very large graphs, the efficiency of the priority queue implementation becomes critical. A naive array-based priority queue (like the `MinPriorityQueue` in the example, which uses `sort()`) will lead to O(V^2) time complexity. Using a binary heap (Min-Heap) for the priority queue reduces the complexity to O(E log V) or O(E + V log V) (depending on heap implementation), which is much more efficient for sparse graphs.

    *   **Solution**: Replace the simple `MinPriorityQueue` with a proper Min-Heap implementation (as discussed in `07_Heaps.md`).

6.  **Floating Point Weights**: If edge weights are floating-point numbers, standard floating-point precision issues might arise, though typically not critical for pathfinding unless extremely precise comparisons are needed.

## References

1.  [GeeksforGeeks: Dijkstra's Algorithm](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/)
2.  [Wikipedia: Dijkstra's algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)
3.  [Visualizing Algorithms: Dijkstra's Algorithm](https://www.cs.usfca.edu/~galles/visualization/Dijkstra.html)
4.  [freeCodeCamp: Dijkstra's Algorithm in JavaScript](https://www.freecodecamp.org/news/dijkstras-algorithm-in-javascript/)


