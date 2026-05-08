# Peak Index in a Mountain Array

**Problem:** A mountain array strictly increases then strictly decreases. Given a mountain array `arr`, return the index of the peak element. Must be O(log n).

**Example:**
- Input: `[0, 2, 1, 0]` → Output: `1`
- Input: `[0, 10, 5, 2]` → Output: `1`
- Input: `[3, 4, 5, 1]` → Output: `2`

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Find the element greater than both neighbors.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        for (int i = 1; i < arr.length - 1; i++) {
            if (arr[i] > arr[i - 1] && arr[i] > arr[i + 1]) {
                return i;
            }
        }
        return -1;
    }
}
```

---

## Solution 2: Optimal — Binary Search

**Approach:** Compare `arr[mid]` with `arr[mid + 1]`:
- If `arr[mid] < arr[mid + 1]` → on the ascending slope, peak is to the right
- Otherwise → on the descending slope (or at peak), peak is at mid or left

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int low = 0, high = arr.length - 1;

        while (low < high) {
            int mid = low + (high - low) / 2;

            if (arr[mid] < arr[mid + 1]) {
                low = mid + 1;  // ascending, peak is right
            } else {
                high = mid;     // descending or at peak
            }
        }

        return low;
    }
}
```

**Dry Run** with `[0, 2, 1, 0]`:

| Step | low | high | mid | arr[mid] | arr[mid+1] | Action |
|------|-----|------|-----|----------|------------|--------|
| 1 | 0 | 3 | 1 | 2 | 1 | 2 ≥ 1, high = 1 |
| 2 | low == high (1) | Exit | | | | |

Result: index **1** ✅

**Dry Run** with `[3, 4, 5, 1]`:

| Step | low | high | mid | arr[mid] | arr[mid+1] | Action |
|------|-----|------|-----|----------|------------|--------|
| 1 | 0 | 3 | 1 | 4 | 5 | 4 < 5, low = 2 |
| 2 | 2 | 3 | 2 | 5 | 1 | 5 ≥ 1, high = 2 |
| 3 | low == high (2) | Exit | | | | |

Result: index **2** ✅

---

## This is the Same as "Find Peak Element"

The logic is identical to LeetCode 162 (Find Peak Element). The only difference:
- **Mountain Array:** Guaranteed exactly one peak, strictly increasing then decreasing
- **Find Peak Element:** May have multiple peaks, any one is valid

Same binary search code works for both. In interviews, mention this connection.

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Check neighbors |
| Binary Search | O(log n) | O(1) | Follow the uphill slope |

**Interview tip:** This is a simpler version of "Find Peak Element." The key line: "if I'm on the ascending slope (`arr[mid] < arr[mid+1]`), the peak must be to the right. Otherwise, I'm at or past the peak, so I search left." One sentence, done. The mountain guarantee makes this cleaner than the general peak problem.
