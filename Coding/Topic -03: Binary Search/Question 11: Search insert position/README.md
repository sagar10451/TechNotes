# Search Insert Position

**Problem:** Given a sorted array and a `target`, return the index if found. If not, return the index where it would be inserted to keep the array sorted. Must be O(log n).

**Example:**
- Input: `nums = [1, 3, 5, 6]`, `target = 5` → Output: `2` (found)
- Input: `nums = [1, 3, 5, 6]`, `target = 2` → Output: `1` (insert between 1 and 3)
- Input: `nums = [1, 3, 5, 6]`, `target = 7` → Output: `4` (insert at end)

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Find the first element ≥ target. That's either the target's index or its insertion point.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] >= target) return i;
        }
        return nums.length; // insert at end
    }
}
```

---

## Solution 2: Optimal — Binary Search

**Approach:** Standard binary search. If target is found, return mid. If not, after the loop `left` points to the correct insertion position (first element > target).

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }

        return left; // insertion position
    }
}
```

**Dry Run** with `[1, 3, 5, 6]`, `target = 2`:

| Step | left | right | mid | nums[mid] | Action |
|------|------|-------|-----|-----------|--------|
| 1 | 0 | 3 | 1 | 3 | 3 > 2, right = 0 |
| 2 | 0 | 0 | 0 | 1 | 1 < 2, left = 1 |
| 3 | left (1) > right (0) | Exit | | | |

Result: `left = 1` → insert at index **1** ✅

**Dry Run** with `[1, 3, 5, 6]`, `target = 5`:

| Step | left | right | mid | nums[mid] | Action |
|------|------|-------|-----|-----------|--------|
| 1 | 0 | 3 | 1 | 3 | 3 < 5, left = 2 |
| 2 | 2 | 3 | 2 | 5 | Found! Return 2 |

Result: **2** ✅

---

## Why `left` is the Insertion Position

After the loop exits (target not found):
- `right` points to the last element < target
- `left` points to the first element > target (= `right + 1`)
- This is exactly where target should be inserted

```
[1, 3, 5, 6]  target = 2
      ↑
   right=0  left=1
   (1 < 2)  (3 > 2)  → insert at index 1
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Find first element ≥ target |
| Binary Search | O(log n) | O(1) | Standard BS, return `left` on miss |

**Interview tip:** This is the most basic "lower bound" binary search. The key insight: after binary search fails to find the target, `left` naturally lands at the insertion position. No special handling needed — just `return left`. This same pattern is used in many problems (find first ≥ target, count elements < target, etc.).
