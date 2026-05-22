# Question 08: Letter Case Permutation

**Question:** https://leetcode.com/problems/letter-case-permutation/

## Detailed Explanation for the Interview:

**Objective:** Given a string s containing both letters and digits, generate all possible strings by changing the case of the letters (keeping digits unchanged).

**Approach:**
- This problem is solved using backtracking.
- We traverse each character in the string:
  - If it's a digit, we move on to the next character without making changes.
  - If it's a letter, we generate two possibilities: one with the letter in lowercase and one in uppercase.
- The recursion explores all possible letter case combinations by branching for each letter.
- Each valid combination is added to the result when we reach the end of the string.

**How it Works:**
- We start by converting the input string into a `char[]` to easily modify characters in-place.
- We use a helper function `generate` that takes the current index `idx` and checks if the character is a letter or digit.
- For each letter, we try both lowercase and uppercase, recurse further, and backtrack to reset the state for the next option.

### Time and Space Complexity:

**Time Complexity:**
- The string has n characters, and for each letter, there are 2 choices (lowercase or uppercase).
- Therefore, the total number of permutations is 2^m, where m is the number of letters.
- Generating each permutation requires O(n) time to construct the string.
- So, the overall time complexity is O(n * 2^m), where m is the number of letters and n is the length of the string.

**Space Complexity:**
- The recursion stack depth is proportional to the length of the string n, so the space complexity due to recursion is O(n).
- Additionally, we're storing 2^m permutations, where each permutation is of length n, so the additional space is O(n * 2^m).

### Dry Run:

Input: "a1b2"

**Process:**
- Start with idx = 0, character is 'a' (a letter).
  - Convert to lowercase: 'a1b2'
  - Recurse with idx = 1.
    - idx = 1, character is '1' (a digit).
    - Skip to next character and recurse with idx = 2.
      - idx = 2, character is 'b' (a letter).
        - Convert to lowercase: 'a1b2'
        - Recurse with idx = 3.
        - Convert to uppercase: 'a1B2'
        - Recurse with idx = 3.
      - idx = 3, character is '2' (a digit).
      - Skip and recurse with idx = 4 (end of string).
      - Add the permutations to the result: 'a1b2' and 'a1B2'.
  - Backtrack to idx = 0, convert 'a' to uppercase: 'A1b2', repeat the same process as above.
  - This gives permutations: 'A1b2' and 'A1B2'.

**Result:** The possible permutations are: a1b2, a1B2, A1b2, A1B2

### Code:

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    List<String> ans = new ArrayList<>();  // To store the result of all possible permutations

    // Main method that initiates the process of generating letter case permutations
    public List<String> letterCasePermutation(String s) {
        generate(s.toCharArray(), 0);  // Start recursion from index 0
        return ans;  // Return the final result list
    }

    // Helper method that generates permutations recursively
    public void generate(char[] arr, int idx) {
        // Base case: If we've processed all characters, add the result to the answer list
        if (idx == arr.length) {
            ans.add(new String(arr));  // Convert the char array back to a string
            return;
        }

        // If the current character is a digit, skip and move to the next character
        if (Character.isDigit(arr[idx])) {
            generate(arr, idx + 1);
            return;
        }

        // Try the current character as lowercase and recurse to the next character
        arr[idx] = Character.toLowerCase(arr[idx]);
        generate(arr, idx + 1);

        // Backtrack and try the current character as uppercase, then recurse to the next character
        arr[idx] = Character.toUpperCase(arr[idx]);
        generate(arr, idx + 1);
    }

    // Main method to run the sample test case
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case
        String input = "a1b2";  // Input string
        List<String> result = solution.letterCasePermutation(input);  // Get permutations

        // Print all possible letter case permutations
        for (String str : result) {
            System.out.println(str);
        }
    }
}
```
