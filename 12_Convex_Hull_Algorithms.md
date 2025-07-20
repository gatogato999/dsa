# 12 Convex Hull Algorithms

## Description

A convex hull of a set of points in a plane is the smallest convex polygon that contains all the points. A convex polygon is a polygon such that for every edge of the polygon, all other points of the polygon lie on the same side of the line defined by that edge. Intuitively, if you imagine the points as nails sticking out of a board, the convex hull would be the shape formed by a rubber band stretched around the outermost nails.

Convex hull algorithms are a fundamental problem in computational geometry and have applications in various fields, including:

*   **Pattern Recognition**: Identifying the shape or boundary of a cluster of points.
*   **Image Processing**: Object recognition and shape analysis.
*   **Geographic Information Systems (GIS)**: Finding the smallest region enclosing a set of locations.
*   **Collision Detection**: In robotics and computer graphics.
*   **Statistics**: Outlier detection.

Several algorithms exist to compute the convex hull, each with different time complexities and suitability for various scenarios:

*   **Jarvis March (Gift Wrapping)**: A simple algorithm that works by iteratively finding the next vertex on the hull. It has a time complexity of O(nh), where n is the number of points and h is the number of vertices on the convex hull. It is efficient when h is small.
*   **Graham Scan**: A more efficient algorithm that sorts points by polar angle around a pivot point and then uses a stack to maintain the convex hull. It has a time complexity of O(n log n), dominated by the sorting step.
*   **Monotone Chain (Andrew's Algorithm)**: Similar to Graham Scan, it sorts points by x-coordinate and then builds the upper and lower hulls separately. It also has a time complexity of O(n log n).
*   **Quickhull**: A divide-and-conquer algorithm inspired by Quick Sort. Its average time complexity is O(n log n), but its worst-case is O(n^2).

## Mentality Before Solving

Consider using a Convex Hull algorithm when:

1.  **Boundary Identification**: The problem requires finding the outermost boundary or shape enclosing a set of discrete points.
2.  **Geometric Optimization**: You need to optimize a geometric property related to a set of points, such as finding the smallest enclosing polygon.
3.  **Data Visualization**: To highlight clusters or extreme points in a scatter plot.
4.  **Choosing an Algorithm**: 
    *   **Jarvis March**: Good for small `h` (number of hull vertices) or when `h` is unknown but expected to be small. Simple to implement.
    *   **Graham Scan / Monotone Chain**: Generally preferred for their robust O(n log n) performance and relative ease of implementation compared to more complex algorithms for larger datasets.
    *   **Quickhull**: Can be fast on average but beware of worst-case scenarios.

## Code in JavaScript/Node.js

We will implement the **Graham Scan** algorithm, as it is widely used and provides good performance.

```javascript
// Helper function to find the lowest point (and leftmost if ties)
function findLowestPoint(points) {
  let lowest = points[0];
  for (let i = 1; i < points.length; i++) {
    if (points[i].y < lowest.y || (points[i].y === lowest.y && points[i].x < lowest.x)) {
      lowest = points[i];
    }
  }
  return lowest;
}

// Helper function to calculate the orientation of three points (p, q, r)
// 0 -> Collinear
// 1 -> Clockwise
// 2 -> Counterclockwise
function orientation(p, q, r) {
  const val = (q.y - p.y) * (r.x - q.x) - (q.x - p.x) * (r.y - q.y);
  if (val === 0) return 0; // Collinear
  return (val > 0) ? 1 : 2; // Clockwise or Counterclockwise
}

function grahamScan(points) {
  const n = points.length;
  if (n < 3) return points; // Convex hull is not well-defined for less than 3 points

  // 1. Find the point with the lowest y-coordinate (and leftmost if ties)
  const p0 = findLowestPoint(points);

  // 2. Sort the remaining points by polar angle with respect to p0
  // If angles are same, sort by distance from p0
  const sortedPoints = points.filter(p => p !== p0).sort((p1, p2) => {
    const o = orientation(p0, p1, p2);
    if (o === 0) {
      // If collinear, closer point comes first
      return (distSq(p0, p1) <= distSq(p0, p2)) ? -1 : 1;
    }
    return (o === 2) ? -1 : 1; // Counterclockwise comes first
  });

  // Add p0 back to the beginning of the sorted list
  sortedPoints.unshift(p0);

  // 3. Initialize a stack with the first three points
  const stack = [];
  stack.push(sortedPoints[0]);
  stack.push(sortedPoints[1]);
  stack.push(sortedPoints[2]);

  // 4. Process remaining points
  for (let i = 3; i < n; i++) {
    // While the orientation of the top two points on stack and current point is not counterclockwise,
    // pop from stack
    while (stack.length > 1 && orientation(stack[stack.length - 2], stack[stack.length - 1], sortedPoints[i]) !== 2) {
      stack.pop();
    }
    stack.push(sortedPoints[i]);
  }

  return stack;
}

// Helper function for squared distance (to avoid Math.sqrt for sorting)
function distSq(p1, p2) {
  return (p1.x - p2.x) * (p1.x - p2.x) + (p1.y - p2.y) * (p1.y - p2.y);
}

// Example Usage:
// const points = [
//   { x: 0, y: 3 }, { x: 1, y: 1 }, { x: 2, y: 2 }, { x: 4, y: 4 },
//   { x: 0, y: 0 }, { x: 1, y: 2 }, { x: 3, y: 1 }, { x: 3, y: 3 }
// ];

// console.log("Points:", points);
// const hull = grahamScan(points);
// console.log("Convex Hull (Graham Scan):");
// hull.forEach(p => console.log(`(${p.x}, ${p.y})`));

// Expected Output (order might vary slightly depending on tie-breaking for collinear points):
// (0, 0)
// (3, 1)
// (4, 4)
// (2, 2) (if (2,2) is on the edge between (0,3) and (4,4))
// (0, 3)

// More robust example for visualization:
// const points2 = [
//   { x: 0, y: 0 }, { x: 1, y: 5 }, { x: 2, y: 1 }, { x: 3, y: 6 },
//   { x: 4, y: 0 }, { x: 5, y: 4 }, { x: 6, y: 2 }, { x: 7, y: 7 },
//   { x: 8, y: 3 }, { x: 9, y: 8 }, { x: 10, y: 0 }
// ];
// const hull2 = grahamScan(points2);
// console.log("\nConvex Hull (Graham Scan) for points2:");
// hull2.forEach(p => console.log(`(${p.x}, ${p.y})`));
// Expected: (0,0), (10,0), (9,8), (7,7), (1,5)
```

## Edge Cases and How to Solve Them

1.  **Fewer Than 3 Points**: A convex hull is typically defined for 3 or more points. For 0, 1, or 2 points, the convex hull is simply the points themselves (or a line segment for 2 points).

    *   **Solution**: Handle `n < 3` as a base case, returning the original points.

2.  **Collinear Points**: If multiple points are collinear (lie on the same line segment), especially on the boundary of the hull, the algorithm needs to handle them correctly. For Graham Scan, when sorting by polar angle, if two points have the same angle with `p0`, the one closer to `p0` should come first. This ensures that only the outermost collinear points are included in the hull.

    *   **Solution**: The `orientation` function returning 0 for collinear points, combined with sorting by distance for collinear points, addresses this. The `while` loop condition `orientation(...) !== 2` (not counterclockwise) ensures that collinear points on the edge are popped if they are not the outermost.

3.  **All Points Collinear**: If all input points lie on a single line, the convex hull is the line segment defined by the two extreme points (the minimum and maximum x-coordinates, or y-coordinates if x-coordinates are identical).

    *   **Solution**: The Graham Scan implementation will correctly return the two extreme points if all points are collinear, as all intermediate points will be popped from the stack due to collinearity.

4.  **Duplicate Points**: If the input contains duplicate points, they should not affect the shape of the convex hull. The algorithm will naturally handle them as distinct points, but they won't change the hull's vertices unless they are unique extreme points.

5.  **Floating Point Precision**: Geometric algorithms often involve calculations with floating-point numbers, which can lead to precision issues. The `orientation` function relies on cross-product, which can be sensitive to precision. Using integer coordinates or robust geometric libraries can mitigate this.

    *   **Solution**: For competitive programming, integer coordinates are often used. For real-world applications, consider using a small epsilon for comparisons or a library designed for robust computational geometry.

6.  **Performance for Large Datasets**: While O(n log n) is efficient, for extremely large datasets, the sorting step can still be a bottleneck. For specific applications, more advanced algorithms like Chan's algorithm (O(n log h)) might be considered if `h` is expected to be much smaller than `n`.

## References

1.  [GeeksforGeeks: Convex Hull | Set 1 (Introduction and Graham Scan)](https://www.geeksforgeeks.org/convex-hull-set-1-introduction-and-graham-scan/)
2.  [Wikipedia: Convex hull algorithms](https://en.wikipedia.org/wiki/Convex_hull_algorithms)
3.  [CP-Algorithms: Convex Hull (Graham Scan)](https://cp-algorithms.com/geometry/convex-hull-graham.html)
4.  [Visualizing Algorithms: Convex Hull](https://www.cs.usfca.edu/~galles/visualization/ConvexHull.html)


