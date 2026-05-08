# Binary Search

**Problem:** Given a sorted array `nums` and a `target`, return the index of `target` if found, otherwise return `-1`.

**Example:**
- Input: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9` → Output: `4`
- Input: `nums = [-1, 0, 3, 5, 9, 12]`, `target = 2` → Output: `-1`

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Iterate through the array and check each element.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int search(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) return i;
        }
        return -1;
    }
}
```

---

## Solution 2: Optimal — Binary Search

**Approach:** Since the array is sorted, repeatedly divide the search space in half:
- If `nums[mid] == target` → found, return mid
- If `target > nums[mid]` → search right half (`low = mid + 1`)
- If `target < nums[mid]` → search left half (`high = mid - 1`)

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int search(int[] nums, int target) {
        int low = 0, high = nums.length - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2; // avoids overflow

            if (nums[mid] == target) return mid;
            else if (target > nums[mid]) low = mid + 1;
            else high = mid - 1;
        }

        return -1;
    }
}
```

**Dry Run** with `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9`:

| Step | low | high | mid | nums[mid] | Action |
|------|-----|------|-----|-----------|--------|
| 1 | 0 | 5 | 2 | 3 | 9 > 3, low = 3 |
| 2 | 3 | 5 | 4 | 9 | Found! Return 4 |

Result: **4** ✅

**Dry Run** with `target = 2`:

| Step | low | high | mid | nums[mid] | Action |
|------|-----|------|-----|-----------|--------|
| 1 | 0 | 5 | 2 | 3 | 2 < 3, high = 1 |
| 2 | 0 | 1 | 0 | -1 | 2 > -1, low = 1 |
| 3 | 1 | 1 | 1 | 0 | 2 > 0, low = 2 |
| 4 | low (2) > high (1) | — | — | Exit loop |

Result: **-1** ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Doesn't use sorted property |
| Binary Search | O(log n) | O(1) | Best — halves search space each step |

**Interview tip:** Mention linear scan as brute force, then immediately say "since the array is sorted, I can use binary search to halve the search space each time." Two things to get right: (1) use `low + (high - low) / 2` instead of `(low + high) / 2` to avoid integer overflow, and (2) the loop condition is `low <= high` (inclusive, not strict). These details show you've written binary search correctly before.
