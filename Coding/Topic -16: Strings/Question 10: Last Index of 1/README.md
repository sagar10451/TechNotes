# Question 10: Last Index of 1

**Source:** https://www.geeksforgeeks.org/problems/last-index-of-15847/1

## Approach-1: Traverse from End

### Steps:
1. Iterate through the string from the end to the beginning.
2. For each character, check if it is '1'.
3. If found, immediately return its index.
4. If loop completes without finding '1', return -1.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

### Dry Run:

Input: s = "011010"
- i=5: '0' → continue
- i=4: '1' → **return 4**

### Code:

```java
class Solution {
    // Method to find the last index of '1' in the given string
    public int lastIndex(String s) {
        // Iterate from the end of the string to the beginning
        for (int i = s.length() - 1; i >= 0; i--) {
            // Check if the current character is '1'
            if (s.charAt(i) == '1') {
                // Return the index of the last occurrence of '1'
                return i;
            }
        }
        // If '1' is not found, return -1
        return -1;
    }

    // Main method to test the lastIndex method with a sample test case
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Sample test case
        String testString = "011010";
        int result = solution.lastIndex(testString);

        // Print the result
        System.out.println("The last index of '1' in the string \"" + testString + "\" is: " + result);
    }
}
```
