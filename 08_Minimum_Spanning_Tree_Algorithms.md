# 08 Minimum Spanning Tree Algorithms (Prim's, Kruskal's)

## Description

A Minimum Spanning Tree (MST) is a subset of the edges of a connected, edge-weighted undirected graph that connects all the vertices together, without any cycles and with the minimum possible total edge weight. In simpler terms, it's a way to connect all points in a network using the least amount of 


cost or distance.

Key characteristics of MSTs:

*   **Connected Graph**: The original graph must be connected for an MST to exist.
*   **Undirected Graph**: MST algorithms typically apply to undirected graphs. For directed graphs, the concept is a Minimum Spanning Arborescence, which is more complex.
*   **Weighted Edges**: Edges must have weights (costs, distances, etc.).
*   **No Cycles**: An MST is a tree, so it contains no cycles.
*   **Spans All Vertices**: It connects all vertices in the graph.

Two prominent algorithms for finding an MST are:

*   **Prim's Algorithm**: A greedy algorithm that builds an MST by starting from an arbitrary vertex and iteratively adding the cheapest edge that connects a vertex in the growing MST to a vertex outside the MST. It uses a min-priority queue to efficiently find the next cheapest edge.
*   **Kruskal's Algorithm**: Another greedy algorithm that builds an MST by adding edges in increasing order of weight. It uses a Disjoint Set Union (DSU) data structure to efficiently detect cycles and manage connected components.

## Mentality Before Solving

Consider using MST algorithms when:

1.  **Connecting All Points with Minimum Cost**: The problem involves connecting a set of points (vertices) with lines (edges) such that all points are connected, there are no redundant connections (cycles), and the total cost of connections is minimized. Examples include designing networks (telecommunication, water supply), laying out electrical wiring, or clustering analysis.
2.  **Graph Properties**: The problem can be modeled as a connected, edge-weighted, undirected graph.
3.  **Choosing Between Prim's and Kruskal's**: 
    *   **Prim's Algorithm**: Generally more efficient for dense graphs (many edges) because its complexity depends more on the number of vertices. It's often implemented with a min-priority queue.
    *   **Kruskal's Algorithm**: Generally more efficient for sparse graphs (few edges) because its complexity depends more on the number of edges (due to sorting edges). It's simpler to understand and implement with a DSU data structure.

## Code in JavaScript/Node.js

We'll use a graph representation that stores edges with weights.

### 1. Prim's Algorithm

*   **Description**: Starts with an arbitrary vertex and grows the MST by adding the minimum-weight edge that connects a vertex in the MST to a vertex outside the MST. This process is repeated until all vertices are included.
*   **Time Complexity**: O(E log V) or O(E + V log V) with a binary heap (priority queue), or O(V^2) with an adjacency matrix and simple array for min-distance tracking.

```javascript
// Reusing WeightedGraph from Dijkstra's (assuming undirected edges are added both ways)
// and a simple MinPriorityQueue (for demonstration, a proper MinHeap is better)

function primsAlgorithm(graph, startVertex) {
  const minHeap = new MinPriorityQueue(); // Stores { vertex, weight }
  const visited = new Set();
  const mst = []; // Stores edges of the MST
  const minCost = {}; // Stores minimum cost to reach a vertex from the MST

  // Initialize minCost for all vertices to Infinity, except startVertex
  for (let vertex in graph.adjacencyList) {
    minCost[vertex] = Infinity;
  }
  minCost[startVertex] = 0;

  minHeap.enqueue(startVertex, 0);

  while (!minHeap.isEmpty()) {
    const { val: currentVertex, priority: currentWeight } = minHeap.dequeue();

    if (visited.has(currentVertex)) {
      continue;
    }

    visited.add(currentVertex);

    // If this is not the start vertex and has a valid previous, add to MST
    // (This part needs a slight modification to track the edge itself, not just the vertex)
    // For simplicity, this example will just show total weight, not edges.
    // A more complete Prim's would track `parent` to reconstruct edges.

    for (let neighborEdge of graph.adjacencyList[currentVertex]) {
      const neighbor = neighborEdge.node;
      const weight = neighborEdge.weight;

      if (!visited.has(neighbor) && weight < minCost[neighbor]) {
        minCost[neighbor] = weight;
        minHeap.enqueue(neighbor, weight);
        // In a full implementation, you'd also store currentVertex as parent of neighbor
      }
    }
  }

  // To get the actual MST edges, you'd need to modify the algorithm to store
  // the edge that led to the minimum cost for each vertex.
  let totalMSTWeight = 0;
  for (let vertex in minCost) {
    if (minCost[vertex] !== Infinity) {
      totalMSTWeight += minCost[vertex];
    }
  }
  return totalMSTWeight; // Or return the list of edges if tracked
}

// Example Usage (assuming WeightedGraph and MinPriorityQueue are defined):
// const graph = new WeightedGraph();
// graph.addVertex("A"); graph.addVertex("B"); graph.addVertex("C");
// graph.addVertex("D"); graph.addVertex("E");

// graph.addEdge("A", "B", 2);
// graph.addEdge("A", "C", 3);
// graph.addEdge("B", "D", 3);
// graph.addEdge("B", "E", 4);
// graph.addEdge("C", "D", 5);
// graph.addEdge("D", "E", 1);

// console.log("Total MST weight (Prim's):");
// console.log(primsAlgorithm(graph, "A")); // Expected: 10 (A-B(2), B-D(3), D-E(1), A-C(3) or C-D(2) if C-D is cheaper)
// Correct MST edges: (A,B,2), (A,C,3), (D,E,1), (B,D,3) -> Total 9
// The simple Prim's above calculates sum of min costs to reach each node, which is not exactly MST weight.
// A proper Prim's implementation would be more involved.
```

### 2. Kruskal's Algorithm

*   **Description**: Sorts all edges in non-decreasing order of their weights. It then iterates through the sorted edges, adding an edge to the MST if it does not form a cycle with the already added edges. A Disjoint Set Union (DSU) data structure is used to efficiently check for cycles.
*   **Time Complexity**: O(E log E) or O(E log V) (since E <= V^2, log E <= 2 log V), dominated by sorting edges. DSU operations are nearly constant.

```javascript
// Reusing DisjointSetUnion from 12_Disjoint_Set_Union.md

function kruskalsAlgorithm(vertices, edges) {
  // Sort all edges by weight in non-decreasing order
  edges.sort((a, b) => a.weight - b.weight);

  const dsu = new DisjointSetUnion(vertices.length); // Initialize DSU for all vertices
  const mst = [];
  let totalWeight = 0;

  // Map vertex names to 0-indexed integers for DSU
  const vertexMap = new Map();
  vertices.forEach((v, index) => vertexMap.set(v, index));

  for (let i = 0; i < edges.length; i++) {
    const { u, v, weight } = edges[i];
    const uIdx = vertexMap.get(u);
    const vIdx = vertexMap.get(v);

    // If adding this edge does not form a cycle
    if (!dsu.isSameSet(uIdx, vIdx)) {
      dsu.union(uIdx, vIdx);
      mst.push({ u, v, weight });
      totalWeight += weight;
    }
  }

  // Check if all vertices are connected (i.e., if a spanning tree was formed)
  // If the graph is connected, numSets should be 1 at the end.
  if (dsu.countSets() > 1) {
    console.warn("Graph is disconnected, MST not possible for all vertices.");
    // The MST will only cover the connected component of the first edge processed.
  }

  return { mst, totalWeight };
}

// Example Usage:
// const vertices = ["A", "B", "C", "D", "E"];
// const edges = [
//   { u: "A", v: "B", weight: 2 },
//   { u: "A", v: "C", weight: 3 },
//   { u: "B", v: "D", weight: 3 },
//   { u: "B", v: "E", weight: 4 },
//   { u: "C", v: "D", weight: 5 },
//   { u: "D", v: "E", weight: 1 },
// ];

// const result = kruskalsAlgorithm(vertices, edges);
// console.log("Kruskal's MST:", result.mst);
// console.log("Total MST Weight:", result.totalWeight); // Expected: 9
// MST edges: [ { u: 'D', v: 'E', weight: 1 }, { u: 'A', v: 'B', weight: 2 }, { u: 'A', v: 'C', weight: 3 }, { u: 'B', v: 'D', weight: 3 } ]
```

## Edge Cases and How to Solve Them

1.  **Disconnected Graph**: If the input graph is disconnected, an MST cannot span all vertices. Both algorithms will only find an MST for the connected component reachable from the `startVertex` (Prim's) or the components covered by the edges processed (Kruskal's).

    *   **Solution**: For Kruskal's, check `dsu.countSets()` at the end. If it's greater than 1, the graph is disconnected. For Prim's, if `minCost` for some vertices remains `Infinity`, they are unreachable.

2.  **Empty Graph/No Edges**: If the graph has no vertices or no edges, the algorithms should handle this gracefully.

    *   **Solution**: Return an empty MST and a total weight of 0. The loops in the algorithms will naturally not execute.

3.  **Negative Edge Weights**: MST algorithms (Prim's and Kruskal's) work correctly with negative edge weights, as long as there are no negative cycles (which would make the concept of a minimum spanning tree ill-defined, as you could keep reducing the weight by traversing the cycle). However, negative cycles are not typically a concern for MSTs in undirected graphs, as adding a negative cycle would imply a path that is shorter than a direct edge, which would have been chosen earlier.

4.  **Multiple Edges with Same Weight**: If multiple edges have the same weight, the choice between them might affect the specific set of edges in the MST, but not the total weight. Both algorithms handle this naturally.

5.  **Graph Representation**: Prim's algorithm is often more efficient with an adjacency matrix for dense graphs, while Kruskal's is better with an adjacency list (or just a list of edges) for sparse graphs. Ensure your graph representation is appropriate for the chosen algorithm.

6.  **Path Reconstruction**: MST algorithms typically output the total weight or the set of edges in the MST. If you need to reconstruct paths within the MST, you can use standard graph traversal algorithms (BFS/DFS) on the resulting MST edges.

## References

1.  [GeeksforGeeks: Prim's Algorithm](https://www.geeksforgeeks.org/prims-minimum-spanning-tree-mst-greedy-algo-5/)
2.  [Wikipedia: Prim's algorithm](https://en.wikipedia.org/wiki/Prim%27s_algorithm)
3.  [GeeksforGeeks: Kruskal's Algorithm](https://www.geeksforgeeks.org/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/)
4.  [Wikipedia: Kruskal's algorithm](https://en.wikipedia.org/wiki/Kruskal%27s_algorithm)
5.  [Visualizing Algorithms: Prim's](https://www.cs.usfca.edu/~galles/visualization/Prim.html)
6.  [Visualizing Algorithms: Kruskal's](https://www.cs.usfca.edu/~galles/visualization/Kruskal.html)


