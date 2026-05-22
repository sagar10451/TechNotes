# Question 21: Word Search

**Question:** https://leetcode.com/problems/word-search/description/

## Approach-1: Backtracking

**Key Steps:**

**exist() method:**
- It iterates through each cell of the board, looking for the first character of the word.
- When a match is found, it calls the `search()` method to explore further.

**search() method (recursive):**
- Base case: If the index reaches the length of the word, the word has been found.
- Boundary checks: Ensures that the current position is within the grid, matches the word's character, and hasn't been visited.
- The current cell is marked as visited using a placeholder (e.g., #).
- It recursively tries to find the next character in all four possible directions: up, down, left, right.
- After exploring, it restores the original value of the cell (unmarks it).
- Backtracking: If a path doesn't lead to the word, it "backtracks" and tries other directions.

### Time Complexity:

- **Worst-case time complexity:** O(M * N * 4^L), where:
  - M is the number of rows.
  - N is the number of columns.
  - L is the length of the word.
- **Explanation:** In the worst case, the algorithm tries every cell (M * N), and for each cell, it explores four possible directions, and this exploration can occur up to the length of the word, L. Therefore, the branching factor is 4, and recursion depth is L.

### Space Complexity:

- **Space complexity:** O(L), where L is the length of the word.
- **Explanation:** The space complexity is primarily due to the recursion stack, which can go up to the depth of L (the length of the word). Additionally, since the board is modified in place and restored after each recursion, there's no extra space used for storing the board.

### Code:

```java
import java.util.*;

class Solution {
    // Main method that checks if the word exists in the board
    public boolean exist(char[][] board, String word) {
        // Iterate over each cell in the board
        for (int i = 0; i < board.length; i++) {  // Loop through rows
            for (int j = 0; j < board[0].length; j++) {  // Loop through columns
                // If the first character matches, start the search
                if (board[i][j] == word.charAt(0)) {
                    // Call search method to find the rest of the word
                    if (search(board, word, i, j, 0)) {
                        return true;  // Return true if the word is found
                    }
                }
            }
        }
        return false;  // Return false if the word doesn't exist
    }

    // Recursive method to search for the word in the board
    private boolean search(char[][] board, String word, int i, int j, int index) {
        // Base case: if the full word is found (all characters matched)
        if (index == word.length()) {
            return true;
        }

        // Check boundaries and if the character matches, and if it's not visited
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != word.charAt(index)) {
            return false;  // Return false if out of bounds or character mismatch
        }

        // If the cell is already visited, return false
        if (board[i][j] == '#') {
            return false;
        }

        // Temporarily mark the current cell as visited
        char temp = board[i][j];
        board[i][j] = '#';  // Mark as visited to avoid revisiting

        // Explore in all 4 directions (up, down, left, right)
        boolean found = search(board, word, i - 1, j, index + 1) ||  // Move up
                        search(board, word, i + 1, j, index + 1) ||  // Move down
                        search(board, word, i, j - 1, index + 1) ||  // Move left
                        search(board, word, i, j + 1, index + 1);    // Move right

        // Restore the original character in the board (backtrack)
        board[i][j] = temp;

        return found;  // Return true if the word is found, otherwise false
    }

    // Main method to test the solution
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case 1: Word "SEE" should exist in the board
        char[][] board = {
            {'A', 'B', 'C', 'E'},
            {'S', 'F', 'C', 'S'},
            {'A', 'D', 'E', 'E'}
        };
        String word = "SEE";

        // Call the exist method and print the result
        boolean result = solution.exist(board, word);
        System.out.println("Output: " + result);  // Expected Output: true
    }
}
```
