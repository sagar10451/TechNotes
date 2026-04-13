# LeetCode 289 — Game of Life

## Problem

Given an `m x n` board where each cell is either alive (`1`) or dead (`0`), apply the Game of Life rules **simultaneously** to every cell and update the board.

**Rules (simplified):**
- Live cell (`1`): survives if **2 or 3** alive neighbors, else dies.
- Dead cell (`0`): becomes alive if **exactly 3** alive neighbors, else stays dead.

**Example:**
```
Input:          Output:
[0, 1, 0]      [0, 0, 0]
[0, 0, 1]  →   [1, 0, 1]
[1, 1, 1]      [0, 1, 1]
[0, 0, 0]      [0, 1, 0]
```

---

## 1. Brute Force (Extra Space)

**Approach:** Use an auxiliary `nextState[][]` array. For each cell, count live neighbors from the original board, apply rules, store result in `nextState`. Copy back at the end.

| Time | Space |
|------|-------|
| O(m × n) | O(m × n) |

```java
import java.util.Arrays;

class Solution {
    public void gameOfLife(int[][] board) {
        int m = board.length;
        int n = board[0].length;
        int[][] nextState = new int[m][n];

        int[][] directions = {
            {-1, -1}, {-1, 0}, {-1, 1},
            {0, -1},           {0, 1},
            {1, -1},  {1, 0},  {1, 1}
        };

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int liveNeighbors = 0;

                for (int k = 0; k < directions.length; k++) {
                    int newRow = i + directions[k][0];
                    int newCol = j + directions[k][1];

                    if (newRow >= 0 && newRow < m && newCol >= 0 && newCol < n) {
                        liveNeighbors += board[newRow][newCol];
                    }
                }

                if (board[i][j] == 1) {
                    // Live cell: survives with 2 or 3 neighbors
                    nextState[i][j] = (liveNeighbors == 2 || liveNeighbors == 3) ? 1 : 0;
                } else {
                    // Dead cell: becomes alive with exactly 3 neighbors
                    nextState[i][j] = (liveNeighbors == 3) ? 1 : 0;
                }
            }
        }

        // Copy back
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                board[i][j] = nextState[i][j];
            }
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] board = {{0,1,0},{0,0,1},{1,1,1},{0,0,0}};
        s.gameOfLife(board);
        for (int[] row : board) System.out.println(Arrays.toString(row));
        // [0, 0, 0]  [1, 0, 1]  [0, 1, 1]  [0, 1, 0]
    }
}
```

---

## 2. ✅ Optimal — In-Place with Transition States

**Key Idea:** Encode transitions using temporary states so we can read the **original** state while writing the **new** state in the same array.

**Transition states:**
| Code | Meaning | Original → New |
|------|---------|----------------|
| 0 | was dead, stays dead | 0 → 0 |
| 1 | was alive, stays alive | 1 → 1 |
| 2 | was alive, now dead | 1 → 0 |
| 3 | was dead, now alive | 0 → 1 |

> To check if a cell was **originally alive**, check `board[r][c] == 1 || board[r][c] == 2` (both mean "was alive").

**Approach:**
1. **First pass:** Count live neighbors (checking for `1` or `2`), mark transitions as `2` or `3`.
2. **Second pass:** Convert `2 → 0` and `3 → 1`.

| Time | Space |
|------|-------|
| **O(m × n)** | **O(1)** — in-place |

```java
import java.util.Arrays;

class Solution {
    public void gameOfLife(int[][] board) {
        int m = board.length;
        int n = board[0].length;

        int[][] directions = {
            {-1, -1}, {-1, 0}, {-1, 1},
            {0, -1},           {0, 1},
            {1, -1},  {1, 0},  {1, 1}
        };

        // Pass 1: Mark transitions
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int liveNeighbors = 0;

                for (int k = 0; k < directions.length; k++) {
                    int newRow = i + directions[k][0];
                    int newCol = j + directions[k][1];

                    // Check 1 or 2 — both mean "originally alive"
                    if (newRow >= 0 && newRow < m && newCol >= 0 && newCol < n
                        && (board[newRow][newCol] == 1 || board[newRow][newCol] == 2)) {
                        liveNeighbors++;
                    }
                }

                // Live cell dies
                if (board[i][j] == 1 && (liveNeighbors < 2 || liveNeighbors > 3)) {
                    board[i][j] = 2; // 1 → 0 (alive → dead)
                }
                // Dead cell becomes alive
                if (board[i][j] == 0 && liveNeighbors == 3) {
                    board[i][j] = 3; // 0 → 1 (dead → alive)
                }
            }
        }

        // Pass 2: Finalize states
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 2) board[i][j] = 0;
                if (board[i][j] == 3) board[i][j] = 1;
            }
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] board = {{0,1,0},{0,0,1},{1,1,1},{0,0,0}};
        s.gameOfLife(board);
        for (int[] row : board) System.out.println(Arrays.toString(row));
        // [0, 0, 0]  [1, 0, 1]  [0, 1, 1]  [0, 1, 0]
    }
}
```

### Dry Run

**Input:**
```
[0, 1, 0]
[0, 0, 1]
[1, 1, 1]
[0, 0, 0]
```

**Pass 1 — Mark transitions:**

| Cell | Live Neighbors | Rule | State |
|------|---------------|------|-------|
| (0,0) | 1 | dead, ≠3 | 0 |
| (0,1) | 1 | alive, <2 → dies | **2** |
| (0,2) | 2 | dead, ≠3 | 0 |
| (1,0) | 3 | dead, ==3 → alive | **3** |
| (1,1) | 5 | dead, ≠3 | 0 |
| (1,2) | 3 | alive, ==3 → survives | 1 |
| (2,0) | 1 | alive, <2 → dies | **2** |
| (2,1) | 3 | alive, ==3 → survives | 1 |
| (2,2) | 2 | alive, ==2 → survives | 1 |
| (3,0) | 2 | dead, ≠3 | 0 |
| (3,1) | 3 | dead, ==3 → alive | **3** |
| (3,2) | 2 | dead, ≠3 | 0 |

**Pass 2 — Finalize:** `2 → 0`, `3 → 1`

```
[0, 0, 0]
[1, 0, 1]
[0, 1, 1]
[0, 1, 0]
```
✅

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Extra Array | O(m×n) | O(m×n) | Separate nextState array |
| 2 | **In-Place Transitions** | **O(m×n)** | **O(1)** | **2 = was alive→dead, 3 = was dead→alive** |

> 🔑 **Key takeaway:** Encode state transitions as temporary values (2, 3) so you can read the original state while writing the new state in-place. Check `== 1 || == 2` for "originally alive."
