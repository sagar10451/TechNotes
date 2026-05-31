# Question 05: Count and Say

**Question:** 38. Count and Say

## Approach-1: Using Recursion (can be done using iterative approach also)

### Steps and Explanation:

1. **Initialization:** The first term is set to "1".
2. **Iterative Generation:** A loop runs from 1 to n-1, generating each subsequent term:
   - A StringBuilder is initialized to construct the next term.
   - Another loop iterates over characters in the current term, using a counter to track consecutive identical characters.
3. **Character Counting:**
   - If current character differs from previous (or end of string): append count and character to sb, reset count.
   - If same: increment count.
4. **Updating the Term:** Newly constructed term replaces s.
5. **Return:** The nth term.

### Time and Space Complexity:
- **Time Complexity:** O(2^n) in the worst case (string length grows exponentially).
- **Space Complexity:** O(n) for the StringBuilder.

### Dry Run:

countAndSay(4):
- s = "1"
- i=1: process "1" → count=1, char='1' → sb="11", s="11"
- i=2: process "11" → count=2, char='1' → sb="21", s="21"
- i=3: process "21" → count=1,char='2' then count=1,char='1' → sb="1211", s="1211"
- **Return "1211"**

### Code:

```java
public class Solution {
    // Method to generate the nth term in the "Count and Say" sequence
    public String countAndSay(int n) {
        String s = "1"; // Base case: the 1st term of the sequence is "1"

        // Iterate from the 2nd term up to the nth term
        for (int i = 1; i < n; i++) {
            StringBuilder sb = new StringBuilder(); // StringBuilder to build the current term

            // Iterate through the current term `s`
            for (int j = 1, count = 1; j <= s.length(); j++) {
                // If we reach the end of the string or find a different character
                if (j == s.length() || s.charAt(j - 1) != s.charAt(j)) {
                    // Append the count and the character to the StringBuilder
                    sb.append(count); // Append the count of characters
                    sb.append(s.charAt(j - 1)); // Append the character
                    count = 1; // Reset count for the new character
                } else {
                    count++; // Increment count for the current character
                }
            }

            // Update `s` to be the newly generated term
            s = sb.toString();
        }

        return s; // Return the nth term of the sequence
    }

    // Main method to test the solution with sample test cases
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Sample test cases
        System.out.println(solution.countAndSay(1)); // Output: "1"
        System.out.println(solution.countAndSay(4)); // Output: "1211"
    }
}
```

