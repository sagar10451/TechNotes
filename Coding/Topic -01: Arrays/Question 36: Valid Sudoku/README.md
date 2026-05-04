# Valid Sudoku

**Problem:** Determine if a 9×9 Sudoku board is valid. Only the filled cells need to be validated:
- Each row must contain digits 1–9 without repetition
- Each column must contain digits 1–9 without repetition
- Each of the nine 3×3 sub-boxes must contain digits 1–9 without repetition

**Note:** The board doesn't need to be solvable — just check if the current state is valid.

---

## Solution 1: Three Separate Passes

**Approach:** Check rows, columns, and 3×3 boxes in three separate loops. Use a HashSet for each to detect duplicates.

- **Time:** O(1) — board is always 9×9 (81 cells, three passes)
- **Space:** O(1) — HashSet holds at most 9 elements

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        // Check each row
        for (int i = 0; i < 9; i++) {
            HashSet<Character> set = new HashSet<>();
            for (int j = 0; j < 9; j++) {
                if (board[i][j] != '.') {
                    if (!set.add(board[i][j])) return false;
                }
            }
        }

        // Check each column
        for (int j = 0; j < 9; j++) {
            HashSet<Character> set = new HashSet<>();
            for (int i = 0; i < 9; i++) {
                if (board[i][j] != '.') {
                    if (!set.add(board[i][j])) return false;
                }
            }
        }

        // Check each 3x3 box
        for (int i = 0; i < 9; i += 3) {
            for (int j = 0; j < 9; j += 3) {
                HashSet<Character> set = new HashSet<>();
                for (int x = i; x < i + 3; x++) {
                    for (int y = j; y < j + 3; y++) {
                        if (board[x][y] != '.') {
                            if (!set.add(board[x][y])) return false;
                        }
                    }
                }
            }
        }

        return true;
    }
}
```

**Dry Run** (abbreviated — showing a duplicate detection case):

Board with duplicate `5` in row 0:
```
5  3  .  .  7  .  .  5  .    ← two 5s in row 0
...
```

| Pass | Check | Set | Result |
|------|-------|-----|--------|
| Row 0 | board[0][0] = '5' | {5} | OK |
| Row 0 | board[0][1] = '3' | {5, 3} | OK |
| Row 0 | board[0][4] = '7' | {5, 3, 7} | OK |
| Row 0 | board[0][7] = '5' | Already in set! | Return false ❌ |

---

## Solution 2: Single Pass (All Checks at Once)

**Approach:** Use three arrays of HashSets — one for rows, one for columns, one for boxes. In a single traversal of the board, check all three constraints simultaneously. Box index is calculated as `(i/3) * 3 + (j/3)`.

- **Time:** O(1)
- **Space:** O(1)

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashSet<Character>[] rows = new HashSet[9];
        HashSet<Character>[] cols = new HashSet[9];
        HashSet<Character>[] boxes = new HashSet[9];

        for (int i = 0; i < 9; i++) {
            rows[i] = new HashSet<>();
            cols[i] = new HashSet<>();
            boxes[i] = new HashSet<>();
        }

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char c = board[i][j];
                if (c == '.') continue;

                int boxIdx = (i / 3) * 3 + (j / 3);

                if (!rows[i].add(c)) return false;  // duplicate in row
                if (!cols[j].add(c)) return false;  // duplicate in column
                if (!boxes[boxIdx].add(c)) return false; // duplicate in box
            }
        }

        return true;
    }
}
```

**Box Index Mapping** (`(i/3)*3 + j/3`):

```
(0,0) (0,1) (0,2) | (0,3) (0,4) (0,5) | (0,6) (0,7) (0,8)
  box 0              box 1                box 2

(3,0) (3,1) (3,2) | (3,3) (3,4) (3,5) | (3,6) (3,7) (3,8)
  box 3              box 4                box 5

(6,0) (6,1) (6,2) | (6,3) (6,4) (6,5) | (6,6) (6,7) (6,8)
  box 6              box 7                box 8
```

**Dry Run** with valid board, cell `board[0][0] = '5'`:

| i | j | char | boxIdx | rows[0] | cols[0] | boxes[0] | Result |
|---|---|------|--------|---------|---------|----------|--------|
| 0 | 0 | '5' | 0 | {5} | {5} | {5} | OK |
| 0 | 1 | '3' | 0 | {5,3} | {3} | {5,3} | OK |
| 0 | 4 | '7' | 1 | {5,3,7} | {7} | {7} | OK |
| ... | | | | | | | |

All 81 cells pass → return `true` ✅

---

## Summary

| Solution | Passes | Time | Space | Notes |
|----------|--------|------|-------|-------|
| Three Separate Passes | 3 | O(1) | O(1) | Clear, easy to explain |
| Single Pass | 1 | O(1) | O(1) | Cleaner, checks everything at once |

**Interview tip:** Both are O(1) since the board is fixed at 9×9, so there's no real complexity difference. Start with Solution 1 — it's the most natural way to think about it: "check rows, check columns, check boxes." Then optimize to Solution 2: "I can do all three checks in one pass using arrays of sets." The key formula to explain is the box index: `(i/3)*3 + j/3` — draw the 3×3 grid of boxes and show how it maps. That's the detail interviewers focus on.
