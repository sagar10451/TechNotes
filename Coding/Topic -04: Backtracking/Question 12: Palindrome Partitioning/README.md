# Question 12: Palindrome Partitioning

**Question:** https://leetcode.com/problems/palindrome-partitioning/description/

## 1. Brute Force Solution (Backtracking)

### Explanation for Interview:

This code solves the Palindrome Partitioning problem using backtracking. The goal is to partition a given string into all possible lists of substrings, where each substring is a palindrome.

**Key Points:**

**Backtracking:**
- The function explores all possible partitions of the string. Each partition is made by checking every substring from a given start index to an end index.
- For every valid palindrome substring found, the function recursively explores the remaining part of the string.
- If the entire string has been partitioned, the current partition (path) is added to the result.

**Palindrome Checking:**
- The function uses a helper `isPalindrome()` to check if a substring s[left:right] is a palindrome by comparing characters from the left and right ends.

**Recursive Exploration:**
- The recursive `backtrack()` function keeps exploring partitions by moving the start pointer through the string and adding palindromes to the current path. Once a valid partition is found, it's added to the result list.

**Backtracking Mechanism:**
- After exploring a branch of partitions, the last palindrome is removed from the path (backtracking) to explore other possibilities.

### Dry Run:

Input: s = "aab"

**Initial Setup:** path = [], start = 0

- Substring "a" is a palindrome: Add "a" to path = ["a"].
  - Recursive call with start = 1.
  - Substring "a" is a palindrome: Add "a" to path = ["a", "a"].
    - Recursive call with start = 2.
    - Substring "b" is a palindrome: Add "b" to path = ["a", "a", "b"].
    - Reached the end of the string (start = 3), so add ["a", "a", "b"] to result.
  - Backtrack: Remove "b", explore other partitions.
  - Substring "ab" is not a palindrome. Continue backtracking.
- Exploring Alternative Partition:
  - Substring "aa" is a palindrome: Add "aa" to path = ["aa"].
    - Recursive call with start = 2.
    - Substring "b" is a palindrome: Add "b" to path = ["aa", "b"], add it to the result.

**Final Result:** [["a", "a", "b"], ["aa", "b"]]

### Time and Space Complexity:

**Time Complexity:**
- Worst-case time complexity: O(2^n * n), where n is the length of the string.
- We generate all possible partitions, and each substring is checked for being a palindrome, which takes O(n) time. In the worst case, we could explore O(2^n) different partition combinations.

**Space Complexity:**
- O(n) (recursion stack) + O(2^n * n) (result storage)

### Code:

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    // Main function to partition the string into all possible palindromic substrings
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();  // List to store the final partitioned palindromes
        backtrack(s, 0, new ArrayList<>(), result);     // Start backtracking from index 0
        return result;  // Return the result after all valid partitions are added
    }

    // Helper function to perform backtracking
    private void backtrack(String s, int start, List<String> path, List<List<String>> result) {
        // Base case: if we have reached the end of the string, add the current partition (path) to result
        if (start == s.length()) {
            result.add(new ArrayList<>(path));  // Add a copy of the current path to the result list
            return;  // End this path exploration
        }

        // Try to partition the string starting from 'start' to all possible 'end' indices
        for (int end = start + 1; end <= s.length(); end++) {
            // Check if the substring s[start:end] is a palindrome
            if (isPalindrome(s, start, end - 1)) {
                path.add(s.substring(start, end));  // Add the palindrome substring to the current path
                backtrack(s, end, path, result);    // Recursively explore further partitions
                path.remove(path.size() - 1);       // Backtrack and remove the last added palindrome
            }
        }
    }

    // Helper function to check if a substring s[left:right] is a palindrome
    private boolean isPalindrome(String s, int left, int right) {
        // Use two-pointer technique to check if the substring is a palindrome
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) {
                return false;  // If characters don't match, it's not a palindrome
            }
        }
        return true;  // If we completed the loop, it's a palindrome
    }

    // Main method to test the solution with one test case
    public static void main(String[] args) {
        Solution solution = new Solution();  // Create an instance of Solution
        String testString = "aab";  // Test case: input string "aab"

        // Call the partition function and store the result
        List<List<String>> result = solution.partition(testString);

        // Print the result
        System.out.println("Palindrome partitions of '" + testString + "': ");
        for (List<String> partition : result) {
            System.out.println(partition);
        }
    }
}
```


## 2. Optimal Solution (Dynamic Programming + Backtracking)

### Explanation for Interview:

This code effectively solves the Palindrome Partitioning problem using a Dynamic Programming (DP) approach combined with backtracking.

**Key Points:**

**Dynamic Programming Table Initialization:**
- A 2D boolean array `dp` is created to keep track of whether the substring s[i:j] is a palindrome.
- The table is initialized such that all single characters are marked as palindromes (dp[i][i] = true).

**Filling the DP Table:**
- The DP table is filled for all possible substring lengths starting from 2 up to n.
- A substring s[i:j] is considered a palindrome if:
  - The first and last characters are the same (s.charAt(i) == s.charAt(j)) and
  - The substring between them is also a palindrome (checked using the previously filled DP table).

**Backtracking to Find Partitions:**
- The `backtrack()` function explores all valid partitions of the string by checking the DP table.
- If a substring is a palindrome, it is added to the current partition path, and the function recursively explores the rest of the string.

**Base Case for Backtracking:**
- When the end of the string is reached (start == s.length()), the current partition is added to the result.

**Efficient Partitioning:**
- The use of the DP table allows for efficient palindrome checking, avoiding redundant calculations.

### Dry Run:

Input: s = "aab"

**Initialize DP Table:**
- dp after initialization:
  - `[[true, false, false], [false, true, true], [false, false, true]]`
- For substrings of length 2: "aa" is a palindrome.
- For substrings of length 3: "aab" is not a palindrome.

**Backtracking Process:**
- Start with start = 0, path = [].
- Check partitions:
  - Substring "a" (indices 0-0) is a palindrome:
    - Add "a" to path: path = ["a"].
    - Recur with start = 1.
      - Check "a" (indices 1-1), add it to path: path = ["a", "a"].
      - Recur with start = 2.
        - Check "b" (indices 2-2), add it to path: path = ["a", "a", "b"].
        - Reached the end: Add ["a", "a", "b"] to result.
      - Backtrack: Remove "b".
    - Backtrack: Remove second "a".
  - Check "aa" (indices 0-1), add it to path: path = ["aa"].
    - Recur with start = 2.
      - Check "b" (indices 2-2), add it to path: path = ["aa", "b"].
      - Reached the end: Add ["aa", "b"] to result.

**Final Result:** [["a", "a", "b"], ["aa", "b"]]

### Time and Space Complexity:

**Time Complexity:**
- O(n^2) for filling the DP table.
- O(n * 2^n) for backtracking to generate partitions.
- Overall Complexity: O(n^2 + n * 2^n).

**Space Complexity:**
- O(n^2) for the DP table.
- O(n) for the recursion stack.
- Overall Complexity: O(n^2).

### Code:

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    // Main function to partition the string into all possible palindromic substrings
    public List<List<String>> partition(String s) {
        int n = s.length();  // Get the length of the input string
        boolean[][] dp = new boolean[n][n];  // DP table to store palindrome checks

        // Initialize the DP table for single characters (always palindromes)
        for (int i = 0; i < n; i++) {
            dp[i][i] = true;
        }

        // Fill the DP table for substrings of length 2 and more
        for (int length = 2; length <= n; length++) {
            for (int i = 0; i <= n - length; i++) {
                int j = i + length - 1;  // Calculate the end index

                // Check if the substring is a palindrome
                if (s.charAt(i) == s.charAt(j) && (length == 2 || dp[i + 1][j - 1])) {
                    dp[i][j] = true;  // Mark the substring as a palindrome
                }
            }
        }

        List<List<String>> result = new ArrayList<>();  // List to store the final partitions
        backtrack(s, 0, new ArrayList<>(), result, dp);  // Start backtracking from index 0
        return result;  // Return the list of palindrome partitions
    }

    // Helper function to perform backtracking
    private void backtrack(String s, int start, List<String> path, List<List<String>> result, boolean[][] dp) {
        // Base case: If we've reached the end of the string, add the current partition to the result list
        if (start == s.length()) {
            result.add(new ArrayList<>(path));  // Add a copy of the current path to the result
            return;  // End this path exploration
        }

        // Explore all possible partitions
        for (int end = start; end < s.length(); end++) {
            // Use the DP table to check if the substring s[start:end+1] is a palindrome
            if (dp[start][end]) {
                path.add(s.substring(start, end + 1));  // Add the palindrome substring to the current path

                // Recur to find other partitions starting from the next index
                backtrack(s, end + 1, path, result, dp);

                // Backtrack: remove the last added palindrome
                path.remove(path.size() - 1);
            }
        }
    }

    // Main method to test the solution with one test case
    public static void main(String[] args) {
        Solution solution = new Solution();  // Create an instance of Solution
        String testString = "aab";  // Test case: input string "aab"

        // Call the partition function and store the result
        List<List<String>> result = solution.partition(testString);

        // Print the result
        System.out.println("Palindrome partitions of '" + testString + "': ");
        for (List<String> partition : result) {
            System.out.println(partition);
        }
    }
}
```
