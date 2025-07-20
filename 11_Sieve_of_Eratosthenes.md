# 11 Sieve of Eratosthenes

## Description

The Sieve of Eratosthenes is an ancient algorithm for finding all prime numbers up to a specified integer. It is one of the most efficient ways to find all primes smaller than `n` when `n` is relatively small.

The algorithm works as follows:

1.  Create a list of consecutive integers from 2 to `n`.
2.  Initially, let all numbers in the list be marked as prime.
3.  Start with the first prime number, `p = 2`.
4.  Mark all multiples of `p` (i.e., `2p, 3p, 4p`, etc.) greater than or equal to `p^2` as composite (not prime). The numbers smaller than `p^2` that are multiples of `p` would have already been marked.
5.  Find the next unmarked number greater than `p`. If there is no such number, stop. Otherwise, let this new number be `p` and repeat from step 4.

When the algorithm terminates, all the numbers in the list that are still marked as prime are indeed prime numbers.

## Mentality Before Solving

Consider using the Sieve of Eratosthenes when:

1.  **Finding All Primes Up to N**: The problem requires generating a list of all prime numbers up to a certain limit `N`. If you only need to check the primality of a single number, trial division or Miller-Rabin primality test might be more appropriate.
2.  **Efficiency for Small N**: For `N` up to around 10^7 or 10^8, the Sieve of Eratosthenes is highly efficient, with a time complexity of approximately O(N log log N).
3.  **Precomputation**: If you need to perform many primality tests or related calculations on primes within a fixed range, precomputing all primes using the Sieve can save significant time.
4.  **Number Theory Problems**: Frequently used in competitive programming and number theory problems that involve prime factorization, counting divisors, or other prime-related properties.

## Code in JavaScript/Node.js

```javascript
function sieveOfEratosthenes(n) {
  // Create a boolean array "isPrime[0..n]" and initialize
  // all entries it as true. A value in isPrime[i] will
  // finally be false if i is Not a prime, else true.
  const isPrime = new Array(n + 1).fill(true);

  // 0 and 1 are not prime numbers
  if (n >= 0) isPrime[0] = false;
  if (n >= 1) isPrime[1] = false;

  // Start from p = 2
  for (let p = 2; p * p <= n; p++) {
    // If isPrime[p] is still true, then it is a prime
    if (isPrime[p] === true) {
      // Mark all multiples of p greater than or equal to p*p as false
      // Multiples less than p*p would have already been marked by smaller primes
      for (let i = p * p; i <= n; i += p) {
        isPrime[i] = false;
      }
    }
  }

  // Collect all prime numbers
  const primes = [];
  for (let i = 2; i <= n; i++) {
    if (isPrime[i]) {
      primes.push(i);
    }
  }

  return primes;
}

// Example Usage:
// console.log("Primes up to 30:", sieveOfEratosthenes(30));
// Output: [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]

// console.log("Primes up to 10:", sieveOfEratosthenes(10));
// Output: [2, 3, 5, 7]

// console.log("Primes up to 1:", sieveOfEratosthenes(1));
// Output: []
```

## Edge Cases and How to Solve Them

1.  **`n` is 0 or 1**: By definition, 0 and 1 are not prime numbers. The algorithm should correctly exclude them.

    *   **Solution**: Explicitly set `isPrime[0]` and `isPrime[1]` to `false` at the beginning of the function. The loops will naturally handle the rest.

2.  **`n` is Negative**: The Sieve of Eratosthenes is defined for positive integers. A negative `n` is an invalid input.

    *   **Solution**: Add input validation. If `n < 0`, throw an error or return an empty array.

    ```javascript
    function sieveOfEratosthenes(n) {
      if (n < 0) {
        console.error("Input n must be a non-negative integer.");
        return [];
      }
      // ... rest of the function
    }
    ```

3.  **Small `n` (e.g., `n < 2`)**: If `n` is less than 2, there are no prime numbers. The algorithm should return an empty list.

    *   **Solution**: The current implementation handles this correctly. If `n=0` or `n=1`, `isPrime[0]` and `isPrime[1]` are set to false, and the loop `for (let p = 2; p * p <= n; p++)` will not execute. The final `primes` array will be empty.

4.  **Optimization for `p * p <= n`**: The outer loop runs up to `p * p <= n` because any composite number `x` less than or equal to `n` must have a prime factor less than or equal to `sqrt(n)`. If `x` had no prime factors less than or equal to `sqrt(n)`, then all its prime factors would be greater than `sqrt(n)`, implying `x` would be a product of two numbers greater than `sqrt(n)`, making `x > n`, which is a contradiction.

5.  **Space Complexity for Large `n`**: For very large `n` (e.g., 10^9 or more), storing a boolean array of size `n` can consume too much memory. In such cases, a segmented sieve or other more advanced primality testing methods might be necessary.

6.  **Performance for Very Large `n`**: While efficient, for extremely large `n` where `N log log N` is still too slow, or if you only need to test the primality of a few very large numbers, probabilistic primality tests (like Miller-Rabin) or deterministic primality tests (like AKS) are used.

## References

1.  [GeeksforGeeks: Sieve of Eratosthenes](https://www.geeksforgeeks.org/sieve-of-eratosthenes/)
2.  [Wikipedia: Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)
3.  [CP-Algorithms: Sieve of Eratosthenes](https://cp-algorithms.com/algebra/prime-sieve.html)


