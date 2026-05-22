# Question 09: Letter Combinations of a Phone Number

**Question:** https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/

## Explanation of Process (Interview Explanation):

**Problem:** Given a string containing digits from 2-9, each digit corresponds to letters on a phone keypad (like '2' -> "abc", '3' -> "def"). The task is to return all possible letter combinations that the number could represent.

**Approach:**
- Use a backtracking algorithm to generate all possible letter combinations.
- First, map each digit to the corresponding letters using a HashMap.
- Start from the first digit and append each possible letter to the current combination.
- Recursively move to the next digit, adding letters to the combination until all digits are processed.
- Once a complete combination is formed, add it to the result list.
- The backtracking function helps explore all possible combinations by removing the last added letter and trying others.

### Time and Space Complexity:

**Time Complexity:**
- Each digit can map to up to 4 possible letters (like '7' -> "pqrs").
- If there are n digits, the total number of combinations is 4^n in the worst case. Each combination takes O(n) to construct.
- Thus, the time complexity is O(4^n * n), where n is the length of the input string.

**Space Complexity:**
- The space complexity is mainly due to the recursive call stack and the result list. The depth of the recursion can be at most n (length of the digits), and the result list contains O(4^n) combinations.
- Thus, the space complexity is O(4^n).

### Dry Run (Example: "23"):

**First call:**
backtrack("23", 0, "", []) -> Start with the first digit '2' mapped to "abc".

**For the first digit '2':**
- Append 'a' -> backtrack("23", 1, "a", []).
  - For the second digit '3' (mapped to "def"):
    - Append 'd' -> Add "ad" to the result.
    - Backtrack, append 'e' -> Add "ae" to the result.
    - Backtrack, append 'f' -> Add "af" to the result.
- Backtrack to first digit, try 'b' -> backtrack("23", 1, "b", ["ad", "ae", "af"]).
  - Repeat for 'b' and '3', generating "bd", "be", "bf".
- Backtrack to first digit, try 'c' -> backtrack("23", 1, "c", ["ad", "ae", "af", "bd", "be", "bf"]).
  - Repeat for 'c' and '3', generating "cd", "ce", "cf".

### Code:

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class Solution {
    public List<String> letterCombinations(String digits) {
        // List to store all possible letter combinations
        List<String> res = new ArrayList<>();

        // Return empty result if input digits is null or empty
        if (digits == null || digits.length() == 0) {
            return res;
        }

        // Mapping of digits to corresponding letters (similar to a phone keypad)
        Map<Character, String> digitToLetters = new HashMap<>();
        digitToLetters.put('2', "abc");
        digitToLetters.put('3', "def");
        digitToLetters.put('4', "ghi");
        digitToLetters.put('5', "jkl");
        digitToLetters.put('6', "mno");
        digitToLetters.put('7', "pqrs");
        digitToLetters.put('8', "tuv");
        digitToLetters.put('9', "wxyz");

        // Call the recursive backtracking function to generate combinations
        //digits = string given to us, 0 = index 
        backtrack(digits, 0, new StringBuilder(), res, digitToLetters);

        return res;       
    }

    // Backtracking function to generate all possible letter combinations
    private void backtrack(String digits, int idx, StringBuilder comb, List<String> res, Map<Character, String> digitToLetters) {
        // Base case: if index reaches the end of the digits, add the current combination to result
        if (idx == digits.length()) {
            res.add(comb.toString());
            return;
        }

        // Get the letters corresponding to the current digit
        String letters = digitToLetters.get(digits.charAt(idx));

        // Iterate over each letter and append it to the current combination
        for (int i = 0; i < letters.length(); i++) {
            char letter = letters.charAt(i);  // Extract the current letter
            comb.append(letter);  // Add the letter to the current combination

            // Recursively call backtrack to process the next digit
            backtrack(digits, idx + 1, comb, res, digitToLetters);

            // Remove the last letter to backtrack and explore other possibilities
            comb.deleteCharAt(comb.length() - 1);
        }
    }   
}

// Main class for testing the solution
public class Main {
    public static void main(String[] args) {
        Solution solution = new Solution();
        String digits = "23";  // Test case

        // Get the letter combinations
        List<String> result = solution.letterCombinations(digits);

        // Print the result
        System.out.println(result);  // Output: [ad, ae, af, bd, be, bf, cd, ce, cf]
    }
}
```
