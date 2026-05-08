# Search in Rotated Sorted Array

**Problem:** Given a sorted array rotated at some pivot, search for a `target` value. Return its index or `-1` if not found. All elements are unique. Must be O(log n).

**Example:**
- Input: `nums = [4, 5, 6, 7, 0, 1, 2]`, `target = 0` → Output: `4`
- Input: `nums = [4, 5, 6, 7, 0, 1, 2]`, `target = 3` → Output: `-1`

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Check each element.

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

## Solution 2: Optimal — Modified Binary Search

**Approach:** In a rotated sorted array, at least one half (left or right of mid) is always sorted. Determine which half is sorted, then check if the target falls within that sorted range.

- If **left half is sorted** (`nums[left] <= nums[mid]`):
  - If target is in `[nums[left], nums[mid])` → search left
  - Otherwise → search right
- If **right half is sorted**:
  - If target is in `(nums[mid], nums[right]]` → search right
  - Otherwise → search left

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) return mid;

            // Left half is sorted
            if (nums[left] <= nums[mid]) {
                if (target >= nums[left] && target < nums[mid]) {
                    right = mid - 1; // target in left sorted half
                } else {
                    left = mid + 1;  // target in right half
                }
            }
            // Right half is sorted
            else {
                if (target > nums[mid] && target <= nums[right]) {
                    left = mid + 1;  // target in right sorted half
                } else {
                    right = mid - 1; // target in left half
                }
            }
        }

        return -1;
    }
}
```

**Dry Run** with `[4, 5, 6, 7, 0, 1, 2]`, `target = 0`:

| Step | left | right | mid | nums[mid] | Sorted Half | Target in Range? | Action |
|------|------|-------|-----|-----------|-------------|-----------------|--------|
| 1 | 0 | 6 | 3 | 7 | Left [4,5,6,7] | 0 ∉ [4,7) | left = 4 |
| 2 | 4 | 6 | 5 | 1 | Right [1,2] | 0 ∉ (1,2] | right = 4 |
| 3 | 4 | 4 | 4 | 0 | Found! | — | Return 4 |

Result: **4** ✅

**Dry Run** with `[4, 5, 6, 7, 0, 1, 2]`, `target = 5`:

| Step | left | right | mid | nums[mid] | Sorted Half | Target in Range? | Action |
|------|------|-------|-----|-----------|-------------|-----------------|--------|
| 1 | 0 | 6 | 3 | 7 | Left [4,5,6,7] | 5 ∈ [4,7) ✅ | right = 2 |
| 2 | 0 | 2 | 1 | 5 | Found! | — | Return 1 |

Result: **1** ✅

---

## Key Insight: One Half is Always Sorted

```
[4, 5, 6, 7, 0, 1, 2]
         ↑ mid=3 (value 7)

Left half:  [4, 5, 6, 7] ← sorted ✅
Right half: [0, 1, 2]    ← sorted ✅ (but we identify via nums[left] <= nums[mid])

The rotation break can only be in ONE half.
So the OTHER half is guaranteed sorted.
We can definitively check if target is in the sorted half.
```

---

## Why `nums[left] <= nums[mid]` (not `<`)?

When `left == mid` (2 elements remaining), we need `<=` to correctly identify the left half as "sorted" (it's just one element). Using `<` would incorrectly fall into the else branch.

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | Doesn't use sorted property |
| Modified Binary Search | O(log n) | O(1) | Identify sorted half, check if target is there |

**Interview tip:** The key insight: "In a rotated sorted array, one half around mid is always fully sorted. I identify which half is sorted, then check if the target falls within that sorted range. If yes, search there. If no, search the other half." This is the decision logic that makes standard binary search work on a rotated array.
