# Question 17: Split String into Max Number of Unique Substrings

**Question:** https://leetcode.com/problems/split-a-string-into-the-max-number-of-unique-substrings/description/

## Process Explanation (for Interview):

**Objective:** The goal of this problem is to split a string s into the maximum number of substrings such that each substring is unique.

**Key Steps:**

1. **Backtracking:**
   - We are using a backtracking approach to explore all possible ways to split the string into unique substrings.
   - At every position `index` in the string, we can create multiple substrings by moving the pointer from index to the end of the string.
   - If the substring created is not already present in the HashSet (which stores all the unique substrings), we add it and recursively check the remaining part of the string.

2. **Recursive Backtracking:**
   - If we reach the end of the string (index == s.length()), we check the size of the HashSet (number of unique substrings found) and update the global variable max if necessary.
   - During backtracking, after exploring a split option, we remove the substring from the set to explore other possibilities (this ensures we try all valid combinations).

3. **Key Points:**
   - Base Case: When the entire string is processed (index == s.length()), we update the max number of unique splits.
   - Pruning: If a substring is already in the HashSet, we skip it because it would not lead to unique splits.
   - The backtracking process ensures that we try every possible split of the string and maximize the number of unique substrings.

### Time and Space Complexity:

**Time Complexity:**
- The time complexity is O(2^n), where n is the length of the string. This is because, for every character, we can either include it in the current substring or start a new substring, leading to an exponential number of recursive calls.

**Space Complexity:**
- The space complexity is O(n), where n is the length of the string. This comes from the HashSet that stores unique substrings, and the recursive call stack.

### Dry Run of the Code:

Input: s = "ababccc"

**Step-by-Step Execution:**

Initial Setup: HashSet<String> hs = {} (empty set), max = Integer.MIN_VALUE

**Recursive Backtracking:**
- At index 0, possible substrings:
  - "a" → Add "a" to hs, hs = {"a"}, continue from index 1.
    - At index 1, possible substrings:
      - "b" → Add "b", hs = {"a", "b"}, continue from index 2.
        - At index 2, possible substrings:
          - "a" → Already in hs, skip.
          - "ab" → Add "ab", hs = {"a", "b", "ab"}, continue from index 4.
            - At index 4, possible substrings:
              - "c" → Add "c", hs = {"a", "b", "ab", "c"}, continue from index 5.
                - At index 5, possible substrings:
                  - "c" → Already in hs, skip.
                  - "cc" → Add "cc", hs = {"a", "b", "ab", "c", "cc"}, continue from index 7.
                    - End of string, check max = 5.

After exploring all possible splits, we get the maximum of 5 unique substrings.

**Final Output:** 5

### Code:

```java
import java.util.HashSet;

class Solution {
    int max = Integer.MIN_VALUE; // To keep track of the maximum number of unique substrings

    // Main function to find the maximum number of unique substrings
    public int maxUniqueSplit(String s) {
        HashSet<String> hs = new HashSet<>();  // A set to track the unique substrings
        backtrack(hs, s, 0); // Call the backtracking function
        return max; // Return the maximum number of unique substrings found
    }

    // Backtracking function to explore different splits of the string
    public void backtrack(HashSet<String> hs, String s, int index) {
        // If the index exceeds the length of the string, return
        if (index > s.length()) {
            return;
        }

        // If we have traversed the entire string, update the maximum if necessary
        if (index == s.length()) {
            max = Math.max(max, hs.size()); // Compare current set size with max
            return;
        }

        // Iterate over possible substrings starting from the current index
        for (int i = index; i < s.length(); i++) {
            String str = s.substring(index, i + 1); // Get the substring from index to i

            // If the substring is not already used
            if (!hs.contains(str)) {
                hs.add(str); // Add the substring to the set
                backtrack(hs, s, i + 1); // Recursively try to split the remaining string
                hs.remove(str); // Backtrack and remove the substring from the set
            }
        }
    }

    // Main method to run a sample test case
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: "ababccc"
        String s = "ababccc";
        int result = solution.maxUniqueSplit(s);
        System.out.println("Max unique splits: " + result); // Expected output: 5 (for example: "a", "b", "ab", "c", "cc")
    }
}
```
