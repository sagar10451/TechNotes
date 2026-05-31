# Question 06: Find the Index of the First Occurrence in a String

**Question:** 28. Find the Index of the First Occurrence in a String

## Approach-1: 2 For Loops

### Explanation:

1. **Initialization:** Lengths of haystack and needle stored in n and m.
2. **Check for Empty Needle:** If needle is empty, return 0.
3. **Outer Loop:** Iterates over all possible starting positions (0 to n-m).
4. **Inner Loop:** For each starting position i, compares characters. If mismatch, breaks.
5. **Match Found:** If inner loop completes (j == m), return starting index i.
6. **No Match:** If outer loop completes without match, return -1.

**Time Complexity:** O(n * m)
**Space Complexity:** O(1)

### Dry Run:

Input: haystack = "hello", needle = "ll"
- i=0: 'h' vs 'l' → mismatch
- i=1: 'e' vs 'l' → mismatch
- i=2: 'l' vs 'l' → match, 'l' vs 'l' → match, j==m → **return 2**

### Code:

```java
class Solution {
    // Method to find the index of the first occurrence of 'needle' in 'haystack'
    public int strStr(String haystack, String needle) {
        int n = haystack.length(); // Length of the haystack string
        int m = needle.length();   // Length of the needle string

        // If needle is empty, by convention, return 0
        if (m == 0) {
            return 0;
        }

        // Step 1: Iterate over each possible starting position in haystack
        for (int i = 0; i <= n - m; i++) {
            // Step 2: Check if the substring of haystack starting from i matches needle
            int j;
            for (j = 0; j < m; j++) {
                // If a mismatch is found, break out of the loop
                if (haystack.charAt(i + j) != needle.charAt(j)) {
                    break;
                }
            }
            // Step 3: If the entire string needle was matched, return the starting index
            if (j == m) {
                return i; // Match found, return the index
            }
        }

        // If no match is found, return -1
        return -1;
    }

    // Main method to test the strStr function with sample test cases
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test cases
        System.out.println(solution.strStr("hello", "ll"));   // Expected output: 2
        System.out.println(solution.strStr("aaaaa", "bba"));  // Expected output: -1
        System.out.println(solution.strStr("", ""));           // Expected output: 0
        System.out.println(solution.strStr("haystack", ""));   // Expected output: 0
        System.out.println(solution.strStr("find", "find"));   // Expected output: 0
    }
}
```

## Approach-2: KMP Algorithm (only explain, dont write code — but code provided for reference)

### Steps:
1. **Build the LPS Array:** The Longest Prefix Suffix array for the needle. Used to skip unnecessary comparisons.
2. **Searching Process:** Iterate through haystack. If characters match, increment both indices. If full match (j==m), return starting index. If mismatch: use LPS array to determine next position, avoiding redundant checks.

**Time Complexity:** O(n + m)
**Space Complexity:** O(m) for the LPS array.

### Dry Run:

Input: haystack = "hello", needle = "ll"
- LPS for "ll": [0, 1]
- i=0,j=0: 'h' vs 'l' → mismatch, i++
- i=1,j=0: 'e' vs 'l' → mismatch, i++
- i=2,j=0: 'l' vs 'l' → match, i=3,j=1
- i=3,j=1: 'l' vs 'l' → match, i=4,j=2
- j==m → **return i-j = 2**

### Code:

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int n = haystack.length();
        int m = needle.length();

        if (m == 0) {
            return 0;
        }

        // Step 1: Create the LPS array for needle
        int[] lps = computeLPSArray(needle);

        int i = 0; // Index for haystack
        int j = 0; // Index for needle

        // Step 2: Iterate over the haystack
        while (i < n) {
            if (haystack.charAt(i) == needle.charAt(j)) {
                i++;
                j++;
            }

            if (j == m) {
                return i - j; // Return the starting index of the match
            } else if (i < n && haystack.charAt(i) != needle.charAt(j)) {
                if (j != 0) {
                    j = lps[j - 1]; // Use the LPS array to skip characters
                } else {
                    i++;
                }
            }
        }

        return -1;
    }

    // Method to compute the LPS array
    private int[] computeLPSArray(String needle) {
        int m = needle.length();
        int[] lps = new int[m];
        int len = 0;
        int i = 1;

        while (i < m) {
            if (needle.charAt(i) == needle.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }

        return lps;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        System.out.println(solution.strStr("hello", "ll"));   // Expected output: 2
        System.out.println(solution.strStr("aaaaa", "bba"));  // Expected output: -1
        System.out.println(solution.strStr("", ""));           // Expected output: 0
        System.out.println(solution.strStr("haystack", ""));   // Expected output: 0
        System.out.println(solution.strStr("find", "find"));   // Expected output: 0
    }
}
```
