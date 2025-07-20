# 06 Bellman-Ford Algorithm

## Description

The Bellman-Ford algorithm is a single-source shortest path algorithm that finds the shortest paths from a single source vertex to all other vertices in a weighted graph. Unlike Dijkstra's algorithm, Bellman-Ford can handle graphs with negative edge weights. It can also detect negative cycles, which are cycles where the sum of edge weights is negative. If a negative cycle is reachable from the source, the shortest path is undefined because the path can be made arbitrarily short by traversing the cycle repeatedly.

The algorithm works by repeatedly relaxing all edges in the graph. Relaxation is the process of updating the distance to a vertex if a shorter path is found through an adjacent vertex. The algorithm performs `V-1` iterations (where `V` is the number of vertices), and in each iteration, it relaxes all `E` edges (where `E` is the number of edges). After `V-1` iterations, if no more distances can be relaxed, the shortest paths are found. A `V`-th iteration is then performed to detect negative cycles.

## Mentality Before Solving

Consider using the Bellman-Ford algorithm when:

1.  **Shortest Path with Negative Edge Weights**: This is the primary reason to choose Bellman-Ford over Dijkstra's. If your graph might contain negative edge weights, Bellman-Ford is necessary to find correct shortest paths.
2.  **Negative Cycle Detection**: If the problem requires detecting whether a negative cycle exists in the graph (and is reachable from the source), Bellman-Ford is suitable. This is crucial in applications like arbitrage detection in financial markets.
3.  **Single Source**: Like Dijkstra's, it finds shortest paths from a single source to all other vertices.
4.  **Time Complexity Trade-off**: Bellman-Ford's time complexity is O(V*E), which is generally slower than Dijkstra's O(E log V) or O(E + V log V) for sparse graphs. For dense graphs (where E approaches V^2), the difference might be less pronounced. Choose Bellman-Ford only if negative weights or negative cycle detection are requirements.

## Code in JavaScript/Node.js

We'll use a graph representation suitable for weighted, directed edges.

```javascript
// Graph class with weighted, directed edges
class WeightedDirectedGraph {
  constructor() {
    this.adjacencyList = {};
    this.edges = []; // Store all edges for easy iteration
  }

  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) {
      this.adjacencyList[vertex] = [];
    }
  }

  addEdge(fromVertex, toVertex, weight) {
    if (!this.adjacencyList[fromVertex] || !this.adjacencyList[toVertex]) {
      console.error("One or both vertices not found.");
      return;
    }
    this.adjacencyList[fromVertex].push({ node: toVertex, weight });
    this.edges.push({ from: fromVertex, to: toVertex, weight: weight });
  }

  getAllVertices() {
    return Object.keys(this.adjacencyList);
  }
}

function bellmanFord(graph, startVertex) {
  const vertices = graph.getAllVertices();
  const distances = {};
  const previous = {};

  // Step 1: Initialize distances from source to all vertices as infinity
  // and previous vertex as null
  for (let vertex of vertices) {
    distances[vertex] = Infinity;
    previous[vertex] = null;
  }
  distances[startVertex] = 0;

  // Step 2: Relax all edges V-1 times
  // A path can have at most V-1 edges
  for (let i = 0; i < vertices.length - 1; i++) {
    for (let edge of graph.edges) {
      const u = edge.from;
      const v = edge.to;
      const weight = edge.weight;

      // Relaxation step
      if (distances[u] !== Infinity && distances[u] + weight < distances[v]) {
        distances[v] = distances[u] + weight;
        previous[v] = u;
      }
    }
  }

  // Step 3: Check for negative cycles
  // If we can still relax an edge, then a negative cycle exists
  for (let edge of graph.edges) {
    const u = edge.from;
    const v = edge.to;
    const weight = edge.weight;

    if (distances[u] !== Infinity && distances[u] + weight < distances[v]) {
      console.error("Graph contains a negative cycle reachable from the source!");
      return { distances: null, previous: null, hasNegativeCycle: true };
    }
  }

  return { distances, previous, hasNegativeCycle: false };
}

// Example Usage:
const graph = new WeightedDirectedGraph();
graph.addVertex("A");
graph.addVertex("B");
graph.addVertex("C");
graph.addVertex("D");
graph.addVertex("E");

graph.addEdge("A", "B", 6);
graph.addEdge("A", "C", 7);
graph.addEdge("B", "C", 8);
graph.addEdge("B", "D", 5);
graph.addEdge("B", "E", -4);
graph.addEdge("C", "D", -3);
graph.addEdge("D", "E", 9);
graph.addEdge("E", "A", 2);

console.log("Graph with negative edge, no negative cycle:");
const result1 = bellmanFord(graph, "A");
console.log("Distances:", result1.distances);
// Expected Output: { A: 0, B: 6, C: 3, D: 0, E: 2 }

// Example with a negative cycle:
const graphWithNegativeCycle = new WeightedDirectedGraph();
graphWithNegativeCycle.addVertex("A");
graphWithNegativeCycle.addVertex("B");
graphWithNegativeCycle.addVertex("C");

graphWithNegativeCycle.addEdge("A", "B", 1);
graphWithNegativeCycle.addEdge("B", "C", -1);
graphWithNegativeCycle.addEdge("C", "A", -1);

console.log("\nGraph with negative cycle:");
const result2 = bellmanFord(graphWithNegativeCycle, "A");
// Expected Output: Graph contains a negative cycle reachable from the source!
// { distances: null, previous: null, hasNegativeCycle: true }
```

## Edge Cases and How to Solve Them

1.  **Disconnected Graph**: If some vertices are unreachable from the `startVertex`, their distances will remain `Infinity`. This is correct, as there is no path from the source to them. The algorithm correctly handles this.

2.  **Non-existent Start Vertex**: If the `startVertex` provided does not exist in the graph, the initialization will not set its distance to 0, leading to incorrect results.

    *   **Solution**: Add a check at the beginning of the `bellmanFord` function to ensure `startVertex` exists in `graph.adjacencyList`.

    ```javascript
    function bellmanFord(graph, startVertex) {
      if (!graph.adjacencyList[startVertex]) {
        console.error("Start vertex not found in graph.");
        return { distances: null, previous: null, hasNegativeCycle: false };
      }
      // ... rest of the function
    }
    ```

3.  **Graph with No Edges**: If the graph has vertices but no edges, only the `startVertex` will have a distance of 0, and all other vertices will remain at `Infinity`. This is correct.

4.  **Graph with No Vertices**: If the graph is empty (no vertices), the algorithm should handle this gracefully.

    *   **Solution**: The `getAllVertices()` method will return an empty array, and the loops will not execute, resulting in empty `distances` and `previous` objects, which is acceptable.

5.  **Positive Cycles**: Bellman-Ford correctly handles positive cycles. They do not affect the shortest path calculation, as traversing a positive cycle would only increase the path length.

6.  **Multiple Edges Between Same Vertices**: If there are multiple edges between the same two vertices, the algorithm will relax all of them. The one with the minimum weight will contribute to finding the shortest path.

7.  **Path Reconstruction**: The `previous` object stores the predecessor of each vertex in the shortest path. To reconstruct the actual path from the source to a target vertex, you can backtrack from the target using the `previous` pointers until you reach the source. This is similar to path reconstruction in Dijkstra's algorithm.

## References

1.  [GeeksforGeeks: Bellman-Ford Algorithm](https://www.geeksforgeeks.org/bellman-ford-algorithm-dp-23/)
2.  [Wikipedia: Bellman–Ford algorithm](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm)
3.  [CP-Algorithms: Bellman-Ford algorithm](https://cp-algorithms.com/graph/bellman-ford.html)
4.  [Visualizing Algorithms: Bellman-Ford](https://www.cs.usfca.edu/~galles/visualization/BellmanFord.html)


