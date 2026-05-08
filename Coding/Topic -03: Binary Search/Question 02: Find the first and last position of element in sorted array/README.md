# Find First and Last Position of Element in Sorted Array

**Problem:** Given a sorted array `nums` and a `target`, find the starting and ending position of `target`. Return `[-1, -1]` if not found. Must be O(log n).

**Example:**
- Input: `nums = [5, 7, 7, 8, 8, 10]`, `target = 8` → Output: `[3, 4]`
- Input: `nums = [5, 7, 7, 8, 8, 10]`, `target = 6` → Output: `[-1, -1]`

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Scan from left to find first occurrence, scan from right to find last occurrence.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int first = -1, last = -1;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) { first = i; break; }
        }

        for (int i = nums.length - 1; i >= 0; i--) {
            if (nums[i] == target) { last = i; break; }
        }

        return new int[]{first, last};
    }
}
```

---

## Solution 2: Optimal — Two Binary Searches

**Approach:** Use binary search twice:
1. **Find first:** When `nums[mid] == target`, save it but keep searching **left** (`right = mid - 1`)
2. **Find last:** When `nums[mid] == target`, save it but keep searching **right** (`left = mid + 1`)

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        return new int[]{findFirst(nums, target), findLast(nums, target)};
    }

    private int findFirst(int[] nums, int target) {
        int left = 0, right = nums.length - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                result = mid;
                right = mid - 1; // keep searching left
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return result;
    }

    private int findLast(int[] nums, int target) {
        int left = 0, right = nums.length - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                result = mid;
                left = mid + 1; // keep searching right
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return result;
    }
}
```

**Dry Run** — `findFirst` with `[5, 7, 7, 8, 8, 10]`, `target = 8`:

| Step | left | right | mid | nums[mid] | Action | result |
|------|------|-------|-----|-----------|--------|--------|
| 1 | 0 | 5 | 2 | 7 | 7 < 8, left = 3 | -1 |
| 2 | 3 | 5 | 4 | 8 | Found! Save, right = 3 | 4 |
| 3 | 3 | 3 | 3 | 8 | Found! Save, right = 2 | 3 |
| 4 | left (3) > right (2) | Exit | | | | 3 |

**Dry Run** — `findLast` with same input:

| Step | left | right | mid | nums[mid] | Action | result |
|------|------|-------|-----|-----------|--------|--------|
| 1 | 0 | 5 | 2 | 7 | 7 < 8, left = 3 | -1 |
| 2 | 3 | 5 | 4 | 8 | Found! Save, left = 5 | 4 |
| 3 | 5 | 5 | 5 | 10 | 10 > 8, right = 4 | 4 |
| 4 | left (5) > right (4) | Exit | | | | 4 |

Result: `[3, 4]` ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Simple but doesn't use sorted property |
| Two Binary Searches | O(log n) | O(1) | Best — two modified binary searches |

**Interview tip:** The key difference from standard binary search: when you find the target, **don't stop**. For first position, keep going left (`right = mid - 1`). For last position, keep going right (`left = mid + 1`). Save the result each time you find a match. This "don't stop on match" pattern is reusable across many binary search variants.
