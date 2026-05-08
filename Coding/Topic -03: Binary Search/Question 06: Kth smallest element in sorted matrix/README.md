# Kth Smallest Element in a Sorted Matrix

**Problem:** Given an `n x n` matrix where each row and column is sorted in ascending order, find the kth smallest element.

**Example:**
```
Matrix:         k = 8 → Output: 13
 1   5   9
10  11  13
12  13  15
```
Sorted order: 1, 5, 9, 10, 11, 12, 13, 13, 15 → 8th = 13

---

## Solution 1: Brute Force — Flatten and Sort

**Approach:** Put all elements into a list, sort it, return the (k-1)th element.

- **Time:** O(n² log n²) = O(n² log n)
- **Space:** O(n²)

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        List<Integer> list = new ArrayList<>();

        for (int[] row : matrix) {
            for (int num : row) {
                list.add(num);
            }
        }

        Collections.sort(list);
        return list.get(k - 1);
    }
}
```

---

## Solution 2: Optimal — Binary Search on Value

**Approach:** Binary search on the **value range** (not indices). For a given mid value, count how many elements in the matrix are ≤ mid. If count < k, search higher. Otherwise, search lower.

The counting uses the "staircase" technique: start from bottom-left, move right if ≤ mid (add entire column above), move up if > mid.

- **Time:** O(n × log(max - min))
- **Space:** O(1)

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];

        while (left < right) {
            int mid = left + (right - left) / 2;
            int count = countLessEqual(matrix, mid);

            if (count < k) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        return left;
    }

    private int countLessEqual(int[][] matrix, int mid) {
        int n = matrix.length;
        int count = 0;
        int row = n - 1, col = 0; // start from bottom-left

        while (row >= 0 && col < n) {
            if (matrix[row][col] <= mid) {
                count += row + 1; // all elements above in this column are also ≤ mid
                col++;
            } else {
                row--;
            }
        }

        return count;
    }
}
```

**Dry Run** with matrix above, `k = 8`:

Binary search on values: left = 1, right = 15

| Step | left | right | mid | countLessEqual(mid) | Action |
|------|------|-------|-----|--------------------:|--------|
| 1 | 1 | 15 | 8 | 2 (only 1,5 ≤ 8) | 2 < 8, left = 9 |
| 2 | 9 | 15 | 12 | 6 (1,5,9,10,11,12) | 6 < 8, left = 13 |
| 3 | 13 | 15 | 14 | 8 (all except 15) | 8 ≥ 8, right = 14 |
| 4 | 13 | 14 | 13 | 8 | 8 ≥ 8, right = 13 |
| 5 | left == right (13) | Exit | | | |

Result: **13** ✅

---

## How `countLessEqual` Works (Staircase)

Starting from bottom-left of the matrix:

```
For mid = 13:
 1   5   9       Start at (2,0) = 12
10  11  13       12 ≤ 13 → count += 3 (entire column 0), move right
12  13  15       (2,1) = 13 ≤ 13 → count += 3 (entire column 1), move right
                 (2,2) = 15 > 13 → move up
                 (1,2) = 13 ≤ 13 → count += 2 (rows 0,1 in column 2), move right
                 col out of bounds → done
                 Total count = 3 + 3 + 2 = 8
```

---

## Why Binary Search on Value Works

The answer must be an actual element in the matrix. Even though we binary search on arbitrary values (mid might not exist in the matrix), the convergence guarantees `left` lands on an actual matrix element:

- If count < k → the answer is strictly greater than mid
- If count ≥ k → the answer is ≤ mid (could be mid itself)
- When left == right, it's the smallest value where count ≥ k — which must be a matrix element

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Flatten + Sort | O(n² log n) | O(n²) | Simple but wasteful |
| Binary Search on Value | O(n × log(max-min)) | O(1) | Best — no extra space |

**Interview tip:** Start with flatten + sort (trivial). Then: "Instead of sorting all elements, I can binary search on the value range. For any candidate value, I count how many elements are ≤ it using the sorted row/column property — starting from bottom-left, I move right when ≤ mid and up when > mid. This counting takes O(n) per binary search step." The staircase counting technique is the same one used in "Search a 2D Matrix II" — mention that connection.
