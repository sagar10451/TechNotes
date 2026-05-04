# Search a 2D Matrix II

**Problem:** Given an `m x n` matrix where each row and each column is sorted in ascending order, determine if a target value exists. (Note: unlike Matrix I, the first element of a row is NOT guaranteed to be greater than the last element of the previous row.)

**Example:**
```
Matrix:                     Target: 5 → true
 1   4   7  11  15          Target: 20 → false
 2   5   8  12  19
 3   6   9  16  22
10  13  14  17  24
18  21  23  26  30
```

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Check every element in the matrix.

- **Time:** O(m × n)
- **Space:** O(1)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                if (matrix[i][j] == target) return true;
            }
        }
        return false;
    }
}
```

---

## Solution 2: Binary Search on Each Row

**Approach:** Since each row is sorted, binary search for the target in every row.

- **Time:** O(m × log n)
- **Space:** O(1)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return false;

        for (int i = 0; i < matrix.length; i++) {
            if (binarySearch(matrix[i], target)) return true;
        }
        return false;
    }

    private boolean binarySearch(int[] row, int target) {
        int left = 0, right = row.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (row[mid] == target) return true;
            else if (row[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return false;
    }
}
```

**Dry Run** with target = `5` on the 5×5 matrix:

| Row | Binary Search | Found? |
|-----|--------------|--------|
| [1, 4, 7, 11, 15] | mid=7 → left, mid=4 → right, mid=7 → no | ❌ |
| [2, 5, 8, 12, 19] | mid=8 → left, mid=5 → yes | ✅ Return true |

---

## Solution 3: Optimal — Start from Top-Right Corner

**Approach:** Start at the top-right corner. At any position:
- If current == target → found
- If current > target → move **left** (eliminate this column)
- If current < target → move **down** (eliminate this row)

**Why top-right?** From top-right, going left decreases and going down increases — so we can always eliminate a full row or column. From top-left, both right and down increase, so we can't decide which way to go.

(Starting from **bottom-left** also works with the same logic — left decreases, up decreases, right increases.)

- **Time:** O(m + n)
- **Space:** O(1)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) return false;

        int row = 0;
        int col = matrix[0].length - 1;

        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--;  // eliminate column
            } else {
                row++;  // eliminate row
            }
        }
        return false;
    }
}
```

**Dry Run** with target = `5`:

```
Start at top-right: matrix[0][4] = 15
```

| Step | row | col | Value | Action |
|------|-----|-----|-------|--------|
| 1 | 0 | 4 | 15 | 15 > 5, move left |
| 2 | 0 | 3 | 11 | 11 > 5, move left |
| 3 | 0 | 2 | 7 | 7 > 5, move left |
| 4 | 0 | 1 | 4 | 4 < 5, move down |
| 5 | 1 | 1 | 5 | Found! Return true |

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(m × n) | O(1) | Check every element |
| Binary Search per Row | O(m × log n) | O(1) | Use row sorting |
| Top-Right Corner | O(m + n) | O(1) | Best — eliminate row or column each step |

**Interview tip:** Start with brute force (trivial). Then say "each row is sorted, so I can binary search each row" → O(m log n). Then the key insight: "if I start from the top-right corner, I can eliminate an entire row or column at each step because left decreases and down increases." That gives O(m + n). Mention that bottom-left also works with the same logic. This shows you understand *why* the starting position matters — that's the signal interviewers look for.
