# 10 Knuth-Morris-Pratt (KMP) Algorithm

## Description

The Knuth-Morris-Pratt (KMP) algorithm is an efficient string-searching algorithm that finds occurrences of a "word" (pattern) within a longer "text" (string). Unlike naive string searching algorithms that might re-examine characters of the text multiple times, KMP avoids re-examining previously matched characters. It achieves this by pre-processing the pattern to build a "longest proper prefix suffix" (LPS) array (also known as a prefix function or failure function).

The LPS array for a pattern `P` of length `m` stores, for each index `i`, the length of the longest proper prefix of `P[0...i]` that is also a suffix of `P[0...i]`. This array tells the algorithm how many characters to shift the pattern when a mismatch occurs, without having to backtrack in the text.

## Mentality Before Solving

Consider using the KMP algorithm when:

1.  **Efficient String Searching**: You need to find all occurrences of a pattern within a large text string, and efficiency is critical. KMP offers O(N + M) time complexity (where N is text length, M is pattern length), which is optimal for string searching.
2.  **Avoiding Backtracking**: The problem requires avoiding re-scanning the text string. KMP achieves this by using the precomputed LPS array to intelligently shift the pattern.
3.  **Pattern Repetitions**: KMP is particularly effective when the pattern itself contains repeating characters or sub-patterns, as the LPS array effectively captures this information to optimize shifts.
4.  **Applications**: Used in text editors (find/replace), bioinformatics (DNA sequence matching), network intrusion detection, and data compression.

## Code in JavaScript/Node.js

### 1. Building the LPS Array (Prefix Function)

This function computes the `lps` array for a given pattern.

```javascript
function computeLPSArray(pattern) {
  const m = pattern.length;
  const lps = new Array(m).fill(0);
  let length = 0; // Length of the previous longest prefix suffix
  let i = 1;

  // lps[0] is always 0
  while (i < m) {
    if (pattern[i] === pattern[length]) {
      length++;
      lps[i] = length;
      i++;
    } else {
      // (pattern[i] !== pattern[length])
      if (length !== 0) {
        length = lps[length - 1];
        // Note that we do not increment i here
      } else {
        // if length is 0, then lps[i] is 0
        lps[i] = 0;
        i++;
      }
    }
  }
  return lps;
}

// Example:
// console.log("LPS for ABRACADABRA:", computeLPSArray("ABRACADABRA")); // [0, 0, 0, 1, 0, 1, 2, 3, 4, 5, 6]
// console.log("LPS for AAAA:", computeLPSArray("AAAA")); // [0, 1, 2, 3]
// console.log("LPS for ABCABC:", computeLPSArray("ABCABC")); // [0, 0, 0, 1, 2, 3]
```

### 2. KMP Search Algorithm

This function uses the precomputed LPS array to find all occurrences of the pattern in the text.

```javascript
function kmpSearch(text, pattern) {
  const n = text.length;
  const m = pattern.length;

  if (m === 0) return []; // Empty pattern matches at every position
  if (n === 0) return []; // Empty text, no matches
  if (m > n) return []; // Pattern longer than text, no matches

  const lps = computeLPSArray(pattern);
  const occurrences = [];

  let i = 0; // Index for text
  let j = 0; // Index for pattern

  while (i < n) {
    if (pattern[j] === text[i]) {
      i++;
      j++;
    }

    if (j === m) {
      // Pattern found at index (i - j)
      occurrences.push(i - j);
      // Shift pattern using LPS array for next possible match
      j = lps[j - 1];
    } else if (i < n && pattern[j] !== text[i]) {
      // Mismatch after j matches
      // Do not match lps[0...lps[j-1]] characters, they will match anyway
      if (j !== 0) {
        j = lps[j - 1];
      } else {
        i++; // No prefix to match, just move to next character in text
      }
    }
  }
  return occurrences;
}

// Example:
// console.log("KMP Search (ABABDABACDABABCABAB):");
// console.log(kmpSearch("ABABDABACDABABCABAB", "ABABCABAB")); // Output: [10]

// console.log("KMP Search (AAAAA, AAA):");
// console.log(kmpSearch("AAAAA", "AAA")); // Output: [0, 1, 2]

// console.log("KMP Search (ABCDEFG, XYZ):");
// console.log(kmpSearch("ABCDEFG", "XYZ")); // Output: []

// console.log("KMP Search (ABCABCABC, ABC):");
// console.log(kmpSearch("ABCABCABC", "ABC")); // Output: [0, 3, 6]
```

## Edge Cases and How to Solve Them

1.  **Empty Pattern**: If the pattern is an empty string (`m = 0`), it technically matches at every position in the text. The provided `kmpSearch` handles this by returning an empty array, which is a common convention for string searching algorithms when the pattern is empty.

    *   **Solution**: If an empty pattern should match at every position, modify the `if (m === 0)` condition to return an array of all indices from 0 to `n-1`.

2.  **Empty Text**: If the text is an empty string (`n = 0`), no matches are possible. The provided `kmpSearch` correctly returns an empty array.

3.  **Pattern Longer Than Text**: If the pattern is longer than the text (`m > n`), no matches are possible. The provided `kmpSearch` correctly returns an empty array.

4.  **Pattern Not Found**: If the pattern does not exist in the text, the `occurrences` array will remain empty, and the function will correctly return an empty array.

5.  **Pattern Occurs Multiple Times**: The algorithm correctly finds all non-overlapping occurrences of the pattern. If overlapping occurrences are desired, the logic for updating `j` after a match (`j = lps[j - 1]`) might need adjustment depending on the specific definition of overlapping matches required.

6.  **Single Character Pattern**: KMP works correctly for single-character patterns. The LPS array for a single character is `[0]`, and the algorithm will behave like a linear search.

7.  **Text/Pattern with Special Characters**: KMP compares characters directly. It works with any characters (letters, numbers, symbols, Unicode) as long as the comparison `===` works as expected. No special handling is needed for special characters themselves.

8.  **Case Sensitivity**: The current implementation is case-sensitive (e.g., "abc" will not match "ABC").

    *   **Solution**: If case-insensitivity is required, convert both the text and pattern to a consistent case (e.g., `toLowerCase()` or `toUpperCase()`) before performing the search.

## References

1.  [GeeksforGeeks: KMP Algorithm](https://www.geeksforgeeks.org/kmp-algorithm-for-pattern-searching/)
2.  [Wikipedia: Knuth–Morris–Pratt algorithm](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)
3.  [CP-Algorithms: KMP algorithm](https://cp-algorithms.com/string/kmp.html)
4.  [Visualizing Algorithms: KMP](https://www.cs.usfca.edu/~galles/visualization/KMP.html)


