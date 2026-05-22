# Question 07: Generalized Abbreviation

**Question:** https://algo.monster/liteproblems/320

## Explanation of the Code:

**Function generateAbbreviations(String word):**
This function initializes the process by creating a result list to store all generated abbreviations. It then calls the backtracking function, starting from the first character (position 0) and with a count of 0 (no characters abbreviated yet).

**Function backtrack(List<String> result, StringBuilder current, String word, int position, int count):**
This function performs the core logic for generating abbreviations:

- **Base Case:** If the current position reaches the end of the word:
  - If there are characters abbreviated (count > 0), it appends the count to the current abbreviation and adds it to the result.
- **Recursive Cases:**
  - First, it considers the option of abbreviating the current character. It calls itself with the next position and increments the count.
  - Next, it considers the option of including the current character in the abbreviation. If the count is greater than 0, it appends it to the current abbreviation, adds the current character, and calls itself for the next position with the count reset to 0.
  - Finally, it backtracks by removing the last appended character or count from the current abbreviation.

### Dry Run:

Input: word = "word"

Initial call: generateAbbreviations("word") initializes result and calls backtrack(result, new StringBuilder(), "word", 0, 0).

**Backtracking Steps:**

Initial Call: position = 0, count = 0

- Abbreviate 'w': position = 1, count = 1
  - Abbreviate 'o': position = 2, count = 2
    - Abbreviate 'r': position = 3, count = 3
      - Abbreviate 'd': position = 4, count = 4
        - Append "4" to current and add it to result: ["4"]
      - Include 'd': position = 4, count = 0
        - Append "3d" to current and add it to result: ["4", "3d"]
    - Include 'r': position = 3, count = 0
      - Append "2r" to current and add it to result: ["4", "3d", "2r..."]
      - (continues exploring with 'r' included)
  - Include 'o': position = 2, count = 0
    - Append "1o" to current and add it to result
    - (continues exploring)
- Include 'w': position = 1, count = 0
  - Append "w" to current
  - (continues exploring all sub-abbreviations)

**Final Result:** All 2⁴ = 16 possible abbreviations of "word".

### Time and Space Complexity:

**Time Complexity:**
The time complexity is O(2^n), where n is the length of the input string. This is because for each character, we have two choices: either to abbreviate or to include the character.

**Space Complexity:**
The space complexity is O(n), where n is the length of the input string. This is mainly due to the space required for the recursion stack and the storage of the resulting abbreviations.

### Code:

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> generateAbbreviations(String word) {
        List<String> result = new ArrayList<>(); // To store all generated abbreviations
        backtrack(result, new StringBuilder(), word, 0, 0); // Start backtracking
        return result; // Return the result
    }

    private void backtrack(List<String> result, StringBuilder current, String word, int position, int count) {
        // If we have reached the end of the word
        if (position == word.length()) {
            // If count is greater than 0, append it to current
            if (count > 0) {
                current.append(count);
            }
            result.add(current.toString()); // Add the current abbreviation to the result
            return;
        }

        // Abbreviate the current character
        backtrack(result, current, word, position + 1, count + 1);

        // Include the current character and reset count
        if (count > 0) {
            current.append(count); // Append the count if it's greater than 0
        }
        current.append(word.charAt(position)); // Append the current character
        backtrack(result, current, word, position + 1, 0); // Move to the next character
        current.setLength(current.length() - (count > 0 ? (String.valueOf(count).length() + 1) : 1)); // Backtrack
    }

    // Main method to test the function
    public static void main(String[] args) {
        Solution sol = new Solution();
        String word = "word"; // Test input
        List<String> abbreviations = sol.generateAbbreviations(word); // Get abbreviations
        System.out.println("Generalized abbreviations for \"" + word + "\": " + abbreviations);
    }
}
```
