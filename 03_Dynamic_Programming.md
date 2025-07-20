# 03 Dynamic Programming

## Description

Dynamic Programming (DP) is an algorithmic technique for solving a complex problem by breaking it down into simpler subproblems, solving each of those subproblems just once, and storing their solutions. The next time the same subproblem occurs, instead of recomputing its solution, a stored solution is simply looked up. This technique is applicable to problems exhibiting two key properties:

1.  **Optimal Substructure**: An optimal solution to the problem can be constructed from optimal solutions to its subproblems.
2.  **Overlapping Subproblems**: The problem can be broken down into subproblems that are reused multiple times. Instead of recomputing these subproblems, their results are stored (memoized) and reused.

Dynamic Programming is typically implemented in two main ways:

*   **Memoization (Top-Down)**: This is a recursive approach where solutions to subproblems are computed and stored in a cache (e.g., a hash map or array). When a subproblem is encountered again, the cached result is returned. It starts from the main problem and recursively breaks it down.
*   **Tabulation (Bottom-Up)**: This is an iterative approach where solutions to subproblems are computed starting from the smallest subproblems and building up to the solution of the main problem. It typically uses a table (array) to store results.

DP is a powerful technique used to solve a wide range of problems, including optimization problems, combinatorial problems, and sequence problems.

## Mentality Before Solving

When approaching a problem, consider Dynamic Programming if:

1.  **Optimization or Counting**: The problem asks for the maximum/minimum value, the number of ways to do something, or whether a certain configuration is possible.
2.  **Recursive Structure**: You can define the problem in terms of smaller instances of the same problem. This suggests a recursive solution.
3.  **Overlapping Subproblems**: When you draw the recursion tree, you notice that the same subproblems are being computed multiple times. This is the strongest hint for DP.
4.  **Optimal Substructure**: The optimal solution to the larger problem can be built from the optimal solutions of its subproblems.
5.  **State Definition**: Can you define the 


state of the problem using a few variables? This state should uniquely identify a subproblem.
6.  **Transition Relation**: Can you define a recurrence relation that shows how to compute the solution for a given state from the solutions of smaller states?

Common DP patterns include:
*   **0/1 Knapsack**: Given items with weights and values, maximize total value within a weight capacity.
*   **Longest Common Subsequence (LCS)**: Find the longest subsequence common to two sequences.
*   **Longest Increasing Subsequence (LIS)**: Find the longest subsequence of a given sequence such that all elements of the subsequence are sorted in increasing order.
*   **Coin Change**: Find the minimum number of coins to make a given amount, or the number of ways to make a given amount.

## Code in JavaScript/Node.js

### Example 1: Fibonacci Sequence (Illustrating Memoization and Tabulation)

**Problem**: Calculate the nth Fibonacci number.

**Recursive (Naive)**: O(2^n) - suffers from overlapping subproblems.

```javascript
function fibonacciNaive(n) {
  if (n <= 1) {
    return n;
  }
  return fibonacciNaive(n - 1) + fibonacciNaive(n - 2);
}

// console.log("Fibonacci Naive(10):");
// console.log(fibonacciNaive(10)); // Output: 55
```

**Memoization (Top-Down DP)**: O(n) time, O(n) space.

```javascript
function fibonacciMemoization(n, memo = {}) {
  if (n <= 1) {
    return n;
  }
  if (n in memo) {
    return memo[n];
  }
  memo[n] = fibonacciMemoization(n - 1, memo) + fibonacciMemoization(n - 2, memo);
  return memo[n];
}

// console.log("Fibonacci Memoization(10):");
// console.log(fibonacciMemoization(10)); // Output: 55
```

**Tabulation (Bottom-Up DP)**: O(n) time, O(n) space.

```javascript
function fibonacciTabulation(n) {
  if (n <= 1) {
    return n;
  }
  const dp = new Array(n + 1);
  dp[0] = 0;
  dp[1] = 1;
  for (let i = 2; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
  }
  return dp[n];
}

// console.log("Fibonacci Tabulation(10):");
// console.log(fibonacciTabulation(10)); // Output: 55
```

### Example 2: 0/1 Knapsack Problem

**Problem**: Given weights and values of `n` items, put these items in a knapsack of capacity `W` to get the maximum total value in the knapsack. Each item can either be included or not included (0/1 property).

**Approach**: Use a 2D DP table `dp[i][w]` representing the maximum value that can be obtained with the first `i` items and a knapsack capacity of `w`.

**Time Complexity**: O(nW), where n is the number of items and W is the knapsack capacity.
**Space Complexity**: O(nW).

```javascript
function knapsack01(weights, values, W) {
  const n = weights.length;
  // dp[i][w] will store the maximum value with i items and capacity w
  const dp = Array(n + 1).fill(0).map(() => Array(W + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    for (let w = 1; w <= W; w++) {
      // Current item's weight and value
      const currentWeight = weights[i - 1];
      const currentValue = values[i - 1];

      if (currentWeight <= w) {
        // Option 1: Include the current item
        // value of current item + max value from remaining capacity with previous items
        const includeItem = currentValue + dp[i - 1][w - currentWeight];
        // Option 2: Exclude the current item
        const excludeItem = dp[i - 1][w];
        dp[i][w] = Math.max(includeItem, excludeItem);
      } else {
        // Cannot include current item, capacity is too small
        dp[i][w] = dp[i - 1][w];
      }
    }
  }
  return dp[n][W];
}

// Example:
// const weights = [1, 2, 3];
// const values = [6, 10, 12];
// const W = 5;
// console.log("0/1 Knapsack Max Value:", knapsack01(weights, values, W)); // Output: 22 (items with weights 2 and 3)
```

### Example 3: Longest Common Subsequence (LCS)

**Problem**: Given two sequences, find the length of the longest subsequence present in both of them.

**Approach**: Use a 2D DP table `dp[i][j]` representing the length of the LCS of `text1[0...i-1]` and `text2[0...j-1]`.

**Time Complexity**: O(mn), where m and n are the lengths of the two strings.
**Space Complexity**: O(mn).

```javascript
function longestCommonSubsequence(text1, text2) {
  const m = text1.length;
  const n = text2.length;

  // dp[i][j] stores the length of LCS of text1[0...i-1] and text2[0...j-1]
  const dp = Array(m + 1).fill(0).map(() => Array(n + 1).fill(0));

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (text1[i - 1] === text2[j - 1]) {
        // If characters match, add 1 to the LCS of the preceding substrings
        dp[i][j] = 1 + dp[i - 1][j - 1];
      } else {
        // If characters don't match, take the maximum LCS from either excluding char from text1 or text2
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }
  return dp[m][n];
}

// Example:
// console.log("LCS of 'abcde' and 'ace':", longestCommonSubsequence("abcde", "ace")); // Output: 3
// console.log("LCS of 'abc' and 'abc':", longestCommonSubsequence("abc", "abc"));   // Output: 3
// console.log("LCS of 'abc' and 'def':", longestCommonSubsequence("abc", "def"));   // Output: 0
```

## Edge Cases and How to Solve Them

1.  **Base Cases**: Correctly defining the base cases for the recurrence relation is crucial. In tabulation, this means initializing the first row/column of the DP table. In memoization, it means defining the stopping conditions for recursion.

    *   **Solution**: Carefully analyze the smallest possible subproblems and their solutions. For example, in LCS, the LCS of any string with an empty string is 0.

2.  **Off-by-One Errors**: When translating recurrence relations to code, especially with 0-indexed arrays and 1-indexed DP table sizes, off-by-one errors are common. Pay close attention to array indices (`i-1`, `j-1`) and loop bounds.

    *   **Solution**: Double-check loop conditions and array accesses. Using a 1-indexed DP table (as shown in examples) can sometimes simplify index mapping.

3.  **Space Optimization**: For some DP problems (like Fibonacci or Knapsack), the current state only depends on a few previous states. This allows for space optimization, reducing the space complexity from O(n) or O(nW) to O(1) or O(W).

    *   **Solution (for Knapsack)**: Instead of a 2D array `dp[i][w]`, you can use a 1D array `dp[w]` and iterate through items. When iterating through capacities `w`, iterate backward to ensure `dp[w - currentWeight]` refers to the value from the *previous* item iteration.

    ```javascript
    function knapsack01SpaceOptimized(weights, values, W) {
      const n = weights.length;
      const dp = Array(W + 1).fill(0);

      for (let i = 0; i < n; i++) {
        const currentWeight = weights[i];
        const currentValue = values[i];
        for (let w = W; w >= currentWeight; w--) {
          dp[w] = Math.max(dp[w], currentValue + dp[w - currentWeight]);
        }
      }
      return dp[W];
    }
    // console.log("0/1 Knapsack Space Optimized:", knapsack01SpaceOptimized(weights, values, W));
    ```

4.  **Order of Computation (for Tabulation)**: Ensure that when filling the DP table, the subproblems a larger problem depends on are already computed. This dictates the order of loops.

    *   **Solution**: For problems like LCS, `dp[i][j]` depends on `dp[i-1][j-1]`, `dp[i-1][j]`, and `dp[i][j-1]`, so iterating `i` from 1 to `m` and `j` from 1 to `n` works correctly.

5.  **Large Input Sizes**: For very large `n` or `W`, the O(n) or O(nW) time/space complexity might still be too high. In such cases, DP might not be the most suitable approach, and you might need to look for more advanced algorithms or approximations.

6.  **Reconstructing the Solution**: DP typically gives the optimal value (e.g., max value in knapsack, length of LCS). If you need to reconstruct the actual items or sequence, you often need to backtrack through the DP table or store additional information during computation.

## References

1.  [GeeksforGeeks: Dynamic Programming](https://www.geeksforgeeks.org/dynamic-programming/)
2.  [Wikipedia: Dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming)
3.  [freeCodeCamp: Dynamic Programming in JavaScript](https://www.freecodecamp.org/news/dynamic-programming-in-javascript/)
4.  [HackerRank: Dynamic Programming Tutorial](https://www.hackerrank.com/challenges/dynamic-programming-tutorial/problem)


