# 04 Greedy Algorithms

## Description

A greedy algorithm is an algorithmic paradigm that follows the problem-solving heuristic of making the locally optimal choice at each stage with the hope of finding a global optimum. In many problems, a greedy strategy does not produce an optimal solution, but in some cases, it does. The key characteristic of a greedy algorithm is that it makes decisions that seem best at the moment, without considering future consequences. It never reconsiders its choices.

For a greedy algorithm to work correctly and produce an optimal solution, the problem must exhibit two properties:

1.  **Greedy Choice Property**: A globally optimal solution can be arrived at by making a locally optimal (greedy) choice. This means that once a greedy choice is made, it does not need to be reconsidered later.
2.  **Optimal Substructure**: An optimal solution to the problem contains optimal solutions to its subproblems. This property is also present in Dynamic Programming problems.

If a problem has these two properties, a greedy approach can be much simpler and more efficient than dynamic programming.

## Mentality Before Solving

When considering a greedy algorithm, ask yourself:

1.  **Local vs. Global Optimum**: Does making the best choice right now (locally) lead to the best overall solution (globally)? This is the most critical question. If not, a greedy approach will fail, and you might need Dynamic Programming or other techniques.
2.  **Simplicity and Efficiency**: Greedy algorithms are often simpler to design and implement, and they typically have better time complexity than dynamic programming solutions (often O(n) or O(n log n) compared to O(n^2) or O(n^3)).
3.  **Proof of Correctness**: For problems where a greedy approach yields an optimal solution, proving its correctness is essential. This often involves an exchange argument or showing that any optimal solution can be transformed into the greedy solution without loss of optimality.
4.  **Common Applications**: Greedy algorithms are often used in problems like:
    *   **Activity Selection Problem**: Select the maximum number of non-overlapping activities.
    *   **Fractional Knapsack Problem**: Maximize the value of items that can be taken into a knapsack, where items can be broken into fractions.
    *   **Huffman Coding**: Constructing optimal prefix codes for data compression.
    *   **Dijkstra's Algorithm**: Finding the shortest paths from a single source to all other vertices in a graph with non-negative edge weights.
    *   **Prim's and Kruskal's Algorithms**: Finding Minimum Spanning Trees.

## Code in JavaScript/Node.js

### Example 1: Activity Selection Problem

**Problem**: You are given `n` activities with their start and finish times. Select the maximum number of activities that can be performed by a single person, assuming a person can only work on one activity at a time.

**Greedy Choice**: Always select the next activity that finishes earliest among the remaining activities that do not conflict with the previously selected activity.

**Time Complexity**: O(n log n) due to sorting, or O(n) if activities are already sorted by finish time.

```javascript
function activitySelection(activities) {
  // Sort activities by their finish times in ascending order
  activities.sort((a, b) => a.finish - b.finish);

  let selectedActivities = [];
  // The first activity to be selected is always the one with the earliest finish time
  selectedActivities.push(activities[0]);
  let lastFinishTime = activities[0].finish;

  // Iterate through the remaining activities
  for (let i = 1; i < activities.length; i++) {
    // If this activity's start time is greater than or equal to the last selected activity's finish time
    if (activities[i].start >= lastFinishTime) {
      selectedActivities.push(activities[i]);
      lastFinishTime = activities[i].finish;
    }
  }
  return selectedActivities;
}

// Example:
// activities = [
//   { start: 1, finish: 2 },
//   { start: 3, finish: 4 },
//   { start: 0, finish: 6 },
//   { start: 5, finish: 7 },
//   { start: 8, finish: 9 },
//   { start: 5, finish: 9 },
// ];
// console.log("Selected Activities:", activitySelection(activities));
// Output: [ { start: 1, finish: 2 }, { start: 3, finish: 4 }, { start: 5, finish: 7 }, { start: 8, finish: 9 } ]
```

### Example 2: Fractional Knapsack Problem

**Problem**: Given weights and values of `n` items, put these items in a knapsack of capacity `W` to get the maximum total value in the knapsack. Items can be broken into fractions.

**Greedy Choice**: Always pick the item with the highest value-to-weight ratio.

**Time Complexity**: O(n log n) due to sorting.

```javascript
function fractionalKnapsack(items, W) {
  // Calculate value-to-weight ratio for each item
  items.forEach(item => {
    item.ratio = item.value / item.weight;
  });

  // Sort items by ratio in descending order
  items.sort((a, b) => b.ratio - a.ratio);

  let totalValue = 0;
  let currentWeight = 0;
  let takenItems = [];

  for (let i = 0; i < items.length; i++) {
    const item = items[i];

    if (currentWeight + item.weight <= W) {
      // Take the whole item
      currentWeight += item.weight;
      totalValue += item.value;
      takenItems.push({ ...item, amount: 1 });
    } else {
      // Take a fraction of the item
      const remainingCapacity = W - currentWeight;
      const fraction = remainingCapacity / item.weight;
      totalValue += item.value * fraction;
      currentWeight += remainingCapacity;
      takenItems.push({ ...item, amount: fraction });
      break; // Knapsack is full
    }
  }
  return { totalValue, takenItems };
}

// Example:
// const items = [
//   { weight: 10, value: 60 },
//   { weight: 20, value: 100 },
//   { weight: 30, value: 120 },
// ];
// const W = 50;
// console.log("Fractional Knapsack:", fractionalKnapsack(items, W));
// Output: { totalValue: 240, takenItems: [ { weight: 20, value: 100, ratio: 5, amount: 1 }, { weight: 10, value: 60, ratio: 6, amount: 1 }, { weight: 30, value: 120, ratio: 4, amount: 0.666... } ] }
// (The order of items in takenItems might vary depending on initial sort stability for equal ratios)
```

## Edge Cases and How to Solve Them

1.  **Empty Input**: If the input array of activities or items is empty, the algorithms should handle this gracefully by returning an empty list or zero value.

    *   **Solution**: Add checks at the beginning of the functions.

    ```javascript
    function activitySelection(activities) {
      if (activities.length === 0) {
        return [];
      }
      // ...
    }
    ```

2.  **Single Item/Activity**: The algorithms should work correctly with a single item or activity.

3.  **All Items Fit / No Items Fit**: For the knapsack problem, if all items fit, the algorithm should take all of them. If no items fit, it should return 0 value.

4.  **Negative Weights/Values**: Greedy algorithms typically assume non-negative weights and values. If negative values are allowed, the problem might change significantly, and a greedy approach might not be optimal.

    *   **Solution**: Clarify problem constraints. If negative values are possible, a different algorithm (e.g., dynamic programming) might be required.

5.  **Proof of Optimality**: The biggest 


challenge with greedy algorithms is proving their correctness. Without a proof, a greedy solution might seem intuitive but could be incorrect for certain inputs.

    *   **Solution**: For competitive programming or academic settings, always try to formally prove the greedy choice property and optimal substructure. For practical applications, test thoroughly with diverse inputs.

6.  **0/1 Knapsack vs. Fractional Knapsack**: It's crucial to distinguish between these two. The 0/1 Knapsack problem (items cannot be broken) *cannot* be solved with a greedy approach and requires dynamic programming. The Fractional Knapsack problem (items can be broken) *can* be solved greedily.

## References

1.  [GeeksforGeeks: Greedy Algorithms](https://www.geeksforgeeks.org/greedy-algorithms/)
2.  [Wikipedia: Greedy algorithm](https://en.wikipedia.org/wiki/Greedy_algorithm)
3.  [GeeksforGeeks: Activity Selection Problem](https://www.geeksforgeeks.org/activity-selection-problem-greedy-algo-1/)
4.  [GeeksforGeeks: Fractional Knapsack Problem](https://www.geeksforgeeks.org/fractional-knapsack-problem/)


