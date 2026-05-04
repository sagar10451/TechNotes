# Search a 2D Matrix

**Problem:** Given an `m x n` matrix where each row is sorted and the first element of each row is greater than the last element of the previous row, determine if a target value exists in the matrix.

**Example:**
```
Matrix:                 Target: 3 → true
 1   3   5   7         Target: 13 → false
10  11  16  20
23  30  34  60
```

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Traverse every element in the matrix and check if it equals the target.

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

## Solution 2: Row Elimination + Binary Search

**Approach:** First find which row the target could be in by checking if `target` falls between the row's first and last element. Then binary search that row.

- **Time:** O(m + log n)
- **Space:** O(1)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;

        for (int i = 0; i < m; i++) {
            if (target >= matrix[i][0] && target <= matrix[i][n - 1]) {
                return binarySearch(matrix[i], target);
            }
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

**Dry Run** with target = `3`:

| Row | Range | In range? | Action |
|-----|-------|-----------|--------|
| 0 | [1, 7] | ✅ Yes | Binary search this row |

Binary search on `[1, 3, 5, 7]`:

| left | right | mid | row[mid] | Action |
|------|-------|-----|----------|--------|
| 0 | 3 | 1 | 3 | Found! Return true |

---

## Solution 3: Optimal — Treat as Flattened 1D Array

**Approach:** Since all rows are sorted and connected, treat the entire matrix as one sorted array of size `m × n`. Use binary search with index conversion: `row = mid / n`, `col = mid % n`.

- **Time:** O(log(m × n)) = O(log m + log n)
- **Space:** O(1)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;
        int left = 0;
        int right = m * n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            int row = mid / n;
            int col = mid % n;
            int midValue = matrix[row][col];

            if (midValue == target) return true;
            else if (midValue < target) left = mid + 1;
            else right = mid - 1;
        }
        return false;
    }
}
```

**Dry Run** with target = `3`, matrix is 3×4 (indices 0–11):

| left | right | mid | row | col | midValue | Action |
|------|-------|-----|-----|-----|----------|--------|
| 0 | 11 | 5 | 1 | 1 | 11 | 11 > 3, right = 4 |
| 0 | 4 | 2 | 0 | 2 | 5 | 5 > 3, right = 1 |
| 0 | 1 | 0 | 0 | 0 | 1 | 1 < 3, left = 1 |
| 1 | 1 | 1 | 0 | 1 | 3 | Found! Return true |

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(m × n) | O(1) | Check every element |
| Row Elimination + Binary Search | O(m + log n) | O(1) | Find row, then binary search |
| Flattened Binary Search | O(log(m × n)) | O(1) | Best — single binary search |

**Interview tip:** Start with brute force (trivial). Then say "since rows are sorted, I can binary search each row — but first I need to find the right row." That gives Solution 2. Then the key insight: "since the last element of each row is less than the first of the next, the whole matrix is one sorted sequence — I can do a single binary search with index conversion `row = mid/n, col = mid%n`." That's the optimal Solution 3 and shows clear progression.
