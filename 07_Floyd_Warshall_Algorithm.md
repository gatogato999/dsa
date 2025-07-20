# 07 Floyd-Warshall Algorithm

## Description

The Floyd-Warshall algorithm is an all-pairs shortest path algorithm. It finds the shortest paths between all pairs of vertices in a weighted, directed graph. It can handle graphs with negative edge weights, but it cannot handle negative cycles. If a negative cycle is present, the algorithm will detect it and indicate that shortest paths are undefined for affected pairs.

The algorithm works by iteratively improving an estimate of the shortest path between two vertices. It considers all possible intermediate vertices (`k`) between a source (`i`) and a destination (`j`). For each pair of vertices `(i, j)`, it checks if going through an intermediate vertex `k` provides a shorter path than the current known shortest path. This process is repeated for all possible `k` vertices.

The core idea is represented by the following recurrence relation:
`dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`

This algorithm is an example of dynamic programming.

## Mentality Before Solving

Consider using the Floyd-Warshall algorithm when:

1.  **All-Pairs Shortest Paths**: The problem requires finding the shortest path between *every* pair of vertices in a graph. If you only need single-source shortest paths, Dijkstra's or Bellman-Ford might be more efficient.
2.  **Negative Edge Weights (No Negative Cycles)**: It can handle negative edge weights, which Dijkstra's cannot. However, if there are negative cycles, it will detect them but cannot provide meaningful shortest paths involving those cycles.
3.  **Small to Medium-Sized Graphs**: The time complexity of Floyd-Warshall is O(V^3), where V is the number of vertices. This makes it suitable for graphs with up to a few hundred vertices. For very large graphs, other approaches (like running Dijkstra's from each vertex if no negative weights, or Johnson's algorithm) might be more efficient.
4.  **Transitive Closure**: It can be easily adapted to find the transitive closure of a graph (i.e., for every pair of vertices (i, j), determine if j is reachable from i).
5.  **Simplicity of Implementation**: Compared to running Dijkstra's V times or implementing Johnson's algorithm, Floyd-Warshall is relatively straightforward to implement.

## Code in JavaScript/Node.js

We'll represent the graph using an adjacency matrix, where `graph[i][j]` stores the weight of the edge from `i` to `j`. `Infinity` represents no direct edge.

```javascript
function floydWarshall(graph) {
  const V = graph.length; // Number of vertices
  const dist = Array(V).fill(0).map(() => Array(V).fill(0));

  // Initialize dist matrix with given graph values
  // dist[i][j] will be the shortest distance from i to j
  for (let i = 0; i < V; i++) {
    for (let j = 0; j < V; j++) {
      dist[i][j] = graph[i][j];
    }
  }

  // Main Floyd-Warshall logic
  // Pick all vertices one by one as source for shortest paths
  for (let k = 0; k < V; k++) {
    // Pick all vertices one by one as destination
    for (let i = 0; i < V; i++) {
      // Pick all vertices one by one as intermediate vertex
      for (let j = 0; j < V; j++) {
        // If vertex k is on the shortest path from i to j,
        // then update the value of dist[i][j]
        if (dist[i][k] !== Infinity && dist[k][j] !== Infinity) {
          dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
        }
      }
    }
  }

  // Check for negative cycles
  // If dist[i][i] becomes negative for any i, then there is a negative cycle
  for (let i = 0; i < V; i++) {
    if (dist[i][i] < 0) {
      console.error("Graph contains a negative cycle!");
      return null; // Or throw an error, or return a specific indicator
    }
  }

  return dist;
}

// Example Usage:
// Represent graph as an adjacency matrix
// Infinity represents no direct edge
// 0 represents distance to self
const INF = Infinity;
const graph1 = [
  [0, 3, INF, 7],
  [8, 0, 2, INF],
  [5, INF, 0, 1],
  [2, INF, INF, 0]
];

console.log("Shortest paths for graph 1:");
const result1 = floydWarshall(graph1);
if (result1) {
  result1.forEach(row => console.log(row));
}
// Expected Output:
// [ 0, 3, 5, 6 ]
// [ 8, 0, 2, 3 ]
// [ 5, 8, 0, 1 ]
// [ 2, 5, 7, 0 ]

// Example with negative edge, no negative cycle
const graph2 = [
  [0, 1, INF, INF],
  [INF, 0, -1, INF],
  [INF, INF, 0, 1],
  [INF, INF, INF, 0]
];

console.log("\nShortest paths for graph 2 (with negative edge):");
const result2 = floydWarshall(graph2);
if (result2) {
  result2.forEach(row => console.log(row));
}
// Expected Output:
// [ 0, 1, 0, 1 ]
// [ INF, 0, -1, 0 ]
// [ INF, INF, 0, 1 ]
// [ INF, INF, INF, 0 ]

// Example with negative cycle
const graph3 = [
  [0, 1, INF],
  [INF, 0, -1],
  [-1, INF, 0]
];

console.log("\nShortest paths for graph 3 (with negative cycle):");
const result3 = floydWarshall(graph3);
// Expected Output: Graph contains a negative cycle!
```

## Edge Cases and How to Solve Them

1.  **Graph Representation**: Floyd-Warshall is most naturally implemented with an adjacency matrix. If your graph is given as an adjacency list, you'll need to convert it to an adjacency matrix first. Initialize all `dist[i][j]` to `Infinity`, `dist[i][i]` to `0`, and then fill in direct edge weights.

2.  **Empty Graph**: If the input graph (matrix) is empty or has zero vertices, the algorithm should handle this gracefully.

    *   **Solution**: Check `if (V === 0)` at the beginning and return an empty matrix or handle as appropriate.

3.  **Disconnected Graph**: The algorithm correctly finds shortest paths within connected components. For unreachable pairs, the distance will remain `Infinity`, which is the correct behavior.

4.  **Negative Cycles**: The algorithm detects negative cycles by checking if `dist[i][i]` becomes negative after all `V` iterations. If it does, it means a path from `i` to `i` can be made arbitrarily short, indicating a negative cycle. In such cases, shortest paths are undefined.

    *   **Solution**: The provided code checks for `dist[i][i] < 0` and returns `null` or indicates a negative cycle. You might want to return a specific error object or throw an exception.

5.  **Path Reconstruction**: Floyd-Warshall primarily computes the shortest path *distances*. To reconstruct the actual paths, you need to maintain a separate `next` matrix (or `predecessor` matrix) during the algorithm's execution. `next[i][j]` would store the next vertex on the shortest path from `i` to `j`. When `dist[i][j]` is updated via `k`, `next[i][j]` is set to `next[i][k]`.

6.  **Large Number of Vertices**: Due to its O(V^3) time complexity, Floyd-Warshall becomes computationally expensive for graphs with a large number of vertices (e.g., V > 500-1000). For such cases, if all-pairs shortest paths are needed, and there are no negative weights, running Dijkstra's from each vertex (V times) might be faster (V * E log V). If negative weights are present but no negative cycles, Johnson's algorithm (which uses Bellman-Ford once and then Dijkstra's V times) is a better choice.

## References

1.  [GeeksforGeeks: Floyd Warshall Algorithm](https://www.geeksforgeeks.org/floyd-warshall-algorithm-dp-16/)
2.  [Wikipedia: Floyd–Warshall algorithm](https://en.wikipedia.org/wiki/Floyd%E2%80%93Warshall_algorithm)
3.  [CP-Algorithms: Floyd-Warshall algorithm](https://cp-algorithms.com/graph/floyd-warshall.html)
4.  [Visualizing Algorithms: Floyd-Warshall](https://www.cs.usfca.edu/~galles/visualization/FloydWarshall.html)


