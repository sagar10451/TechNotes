# Question 06: Generate Parentheses

**Question:** https://leetcode.com/problems/generate-parentheses/

## Process Explanation (for Interview):

The problem is to generate all valid combinations of n pairs of parentheses. The recursive approach used here is based on the backtracking technique, which explores all possible combinations of parentheses.

### Key Points to Explain:

**Recursive Backtracking:**
- We start with an empty string and progressively add open `(` and close `)` parentheses as long as they form valid combinations.
- We maintain two counters, `open` and `close`, to track how many open and close parentheses have been added.
- The recursion stops when both open and close counters equal n, indicating a valid combination of parentheses has been formed.

**Base Case:**
- When `open == n && close == n`, it means we have formed a valid combination of parentheses, and it is added to the result list.

**Recursive Steps:**
- Add Open Parenthesis: We can add an open parenthesis as long as `open < n`.
- Add Close Parenthesis: We can add a close parenthesis only if `close < open`, ensuring that we never add more close parentheses than open ones.

**Backtracking:**
- After each recursive call, we backtrack by removing the last added parenthesis (`sb.setLength(sb.length() - 1)`) to explore other valid combinations.

**Key Intuition:**
- Open parentheses can always be added until they reach the limit n.
- Close parentheses can only be added when the number of open parentheses is greater than the number of close parentheses.

### Time and Space Complexity:

**Time Complexity:**
- The time complexity is O(4^n / √n). This is the number of valid parentheses combinations for n pairs, which is related to the nth Catalan number.
- For each valid combination, the recursive function generates and backtracks through multiple options, but the pruning in the recursion (by checking valid conditions) prevents generating invalid combinations, making the algorithm more efficient than brute force.

**Space Complexity:**
- The space complexity is O(n) for the recursion call stack and the StringBuilder used to construct each valid combination.
- The result list will also take space proportional to the number of valid combinations, which can be significant for large n.

### Dry Run of the Code:

Input: n = 3

We want to generate all valid combinations of 3 pairs of parentheses.

**Initial Call:**
- Start with an empty string: sb = "".
- open = 0, close = 0, n = 3.

**First Recursive Calls:**
- Add "(" → sb = "(", open = 1, close = 0.
- Add another "(" → sb = "((", open = 2, close = 0.
- Add another "(" → sb = "(((", open = 3, close = 0.

**Start Adding Close Parentheses:**
- Add ")" → sb = "((()", open = 3, close = 1.
- Add ")" → sb = "((())", open = 3, close = 2.
- Add ")" → sb = "((()))", open = 3, close = 3.
- This forms a valid combination: "((()))", which is added to the result.

**Backtrack and Explore Other Combinations:**
- Backtrack and remove the last ")", so sb = "((())".
- Backtrack again and remove another ")", so sb = "((()".
- Add ")" → sb = "(()()", and proceed similarly to generate more combinations: "(()())", "(())()", "()(())", "()()()".

**Result:**
All valid combinations of parentheses for n = 3 are:
`["((()))", "(()())", "(())()", "()(())", "()()()"]`

### Code:

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    // Main method to generate all valid combinations of n pairs of parentheses
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>(); // List to store the result
        helper(res, new StringBuilder(), 0, 0, n); // Call helper function to generate parentheses
        return res;
    }

    // Helper function to build valid parentheses combinations recursively
    private void helper(List<String> res, StringBuilder sb, int open, int close, int n) {
        // Base case: when the number of open and close parentheses equals n, we've formed a valid combination
        if (open == n && close == n) {
            res.add(sb.toString()); // Add the valid combination to the result list
            return;
        }

        // If we can still add more open parentheses (open < n), add an open parenthesis
        if (open < n) {
            sb.append("("); // Add "("
            helper(res, sb, open + 1, close, n); // Recur with one more open parenthesis
            sb.setLength(sb.length() - 1); // Backtrack (remove the last added parenthesis)
        }

        // If the number of close parentheses is less than the number of open ones, add a close parenthesis
        if (close < open) {
            sb.append(")"); // Add ")"
            helper(res, sb, open, close + 1, n); // Recur with one more close parenthesis
            sb.setLength(sb.length() - 1); // Backtrack (remove the last added parenthesis)
        }
    }

    // Main method to run a sample test case
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: n = 3
        int n = 3;
        List<String> result = solution.generateParenthesis(n);
        System.out.println("All valid parentheses combinations for n = " + n + ": " + result);
        // Expected output: ["((()))", "(()())", "(())()", "()(())", "()()()"]
    }
}
```
