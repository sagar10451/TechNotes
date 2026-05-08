# Find Peak Element

**Problem:** A peak element is greater than its neighbors. Given an array `nums`, find any peak element and return its index. You may assume `nums[-1] = nums[n] = -∞`. Must be O(log n).

**Example:**
- Input: `[1, 2, 3, 1]` → Output: `2` (nums[2] = 3 is a peak)
- Input: `[1, 2, 1, 3, 5, 6, 4]` → Output: `5` (or `1`, any peak is valid)

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Check each element — if it's greater than both neighbors, it's a peak. Handle edges (first/last element only has one neighbor).

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int n = nums.length;

        for (int i = 0; i < n; i++) {
            if ((i == 0 || nums[i] > nums[i - 1]) &&
                (i == n - 1 || nums[i] > nums[i + 1])) {
                return i;
            }
        }

        return -1;
    }
}
```

---

## Solution 2: Optimal — Binary Search

**Approach:** Compare `nums[mid]` with `nums[mid + 1]`:
- If `nums[mid] < nums[mid + 1]` → we're on an ascending slope, a peak must exist to the **right**
- Otherwise → we're on a descending slope (or at a peak), the peak is at **mid or left**

**Why this works:** Since `nums[-1] = nums[n] = -∞`, if you're going uphill, there must be a peak ahead (the array eventually drops to -∞). Just follow the uphill direction.

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int left = 0, right = nums.length - 1;

        while (left < right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] < nums[mid + 1]) {
                left = mid + 1;  // peak is to the right
            } else {
                right = mid;     // peak is at mid or to the left
            }
        }

        return left;
    }
}
```

**Dry Run** with `[1, 2, 3, 1]`:

| Step | left | right | mid | nums[mid] | nums[mid+1] | Action |
|------|------|-------|-----|-----------|-------------|--------|
| 1 | 0 | 3 | 1 | 2 | 3 | 2 < 3, left = 2 |
| 2 | 2 | 3 | 2 | 3 | 1 | 3 ≥ 1, right = 2 |
| 3 | left == right (2) | Exit | | | | |

Result: index **2** (nums[2] = 3) ✅

**Dry Run** with `[1, 2, 1, 3, 5, 6, 4]`:

| Step | left | right | mid | nums[mid] | nums[mid+1] | Action |
|------|------|-------|-----|-----------|-------------|--------|
| 1 | 0 | 6 | 3 | 3 | 5 | 3 < 5, left = 4 |
| 2 | 4 | 6 | 5 | 6 | 4 | 6 ≥ 4, right = 5 |
| 3 | 4 | 5 | 4 | 5 | 6 | 5 < 6, left = 5 |
| 4 | left == right (5) | Exit | | | | |

Result: index **5** (nums[5] = 6) ✅

---

## Why Binary Search Works on an Unsorted Array

This is a common interview question: "the array isn't sorted — how can you use binary search?"

The answer: we're not searching for a value, we're searching for a **structural property** (a peak). The key guarantee is `nums[-1] = nums[n] = -∞`:

```
If nums[mid] < nums[mid+1]:
  → We're going uphill to the right
  → The array must come back down eventually (to -∞)
  → So a peak MUST exist somewhere to the right

If nums[mid] >= nums[mid+1]:
  → We're going downhill (or flat) to the right
  → A peak exists at mid or to the left
```

This "guaranteed existence" in one half is what makes binary search valid.

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Check each element |
| Binary Search | O(log n) | O(1) | Follow the uphill direction |

**Interview tip:** The key insight to state: "Since the boundaries are -∞, if I'm on an ascending slope, a peak must exist ahead — the array can't keep going up forever. So I follow the uphill direction with binary search." This explains *why* binary search works on an unsorted array — it's about guaranteed existence, not sorted order.
