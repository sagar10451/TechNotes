# Median of Two Sorted Arrays

**Problem:** Given two sorted arrays `nums1` and `nums2`, return the median of the combined sorted array. Must be O(log(m+n)).

**Example:**
- Input: `nums1 = [1, 3]`, `nums2 = [2]` → Output: `2.0`
- Input: `nums1 = [1, 2]`, `nums2 = [3, 4]` → Output: `2.5`

---

## Solution 1: Brute Force — Merge and Sort

**Approach:** Merge both arrays into one, sort it, find the median.

- **Time:** O((m+n) log(m+n))
- **Space:** O(m+n)

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int[] merged = new int[nums1.length + nums2.length];
        System.arraycopy(nums1, 0, merged, 0, nums1.length);
        System.arraycopy(nums2, 0, merged, nums1.length, nums2.length);
        Arrays.sort(merged);

        int n = merged.length;
        if (n % 2 == 0) {
            return (merged[n / 2 - 1] + merged[n / 2]) / 2.0;
        } else {
            return merged[n / 2];
        }
    }
}
```

---

## Solution 2: Optimal — Binary Search on Partition

**Approach:** Binary search on the smaller array to find a partition that splits both arrays into left and right halves such that:
- All left elements ≤ all right elements
- Left half has exactly `(m+n+1)/2` elements

Then the median is derived from the boundary elements.

- **Time:** O(log(min(m, n)))
- **Space:** O(1)

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // Always binary search on the smaller array
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int x = nums1.length, y = nums2.length;
        int low = 0, high = x;

        while (low <= high) {
            int partitionX = (low + high) / 2;
            int partitionY = (x + y + 1) / 2 - partitionX;

            int maxX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            int minX = (partitionX == x) ? Integer.MAX_VALUE : nums1[partitionX];
            int maxY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            int minY = (partitionY == y) ? Integer.MAX_VALUE : nums2[partitionY];

            if (maxX <= minY && maxY <= minX) {
                // Found correct partition
                if ((x + y) % 2 == 0) {
                    return (Math.max(maxX, maxY) + Math.min(minX, minY)) / 2.0;
                } else {
                    return Math.max(maxX, maxY);
                }
            } else if (maxX > minY) {
                high = partitionX - 1; // move left in nums1
            } else {
                low = partitionX + 1;  // move right in nums1
            }
        }

        throw new IllegalArgumentException("Input arrays are not sorted");
    }
}
```

**How the Partition Works:**

```
nums1: [1, 3]       nums2: [2]

We want to split into left half and right half:
  Left half has (m+n+1)/2 = 2 elements
  Right half has 1 element

partitionX = 1 (take 1 element from nums1)
partitionY = 2 - 1 = 1 (take 1 element from nums2)

nums1:  [1 | 3]     maxX = 1, minX = 3
nums2:  [2 |  ]     maxY = 2, minY = ∞

Check: maxX(1) ≤ minY(∞) ✅ and maxY(2) ≤ minX(3) ✅

Odd total → median = max(maxX, maxY) = max(1, 2) = 2.0 ✅
```

**Dry Run** with `nums1 = [1, 2]`, `nums2 = [3, 4]`:

x = 2, y = 2, total = 4 (even)

| Step | low | high | partX | partY | maxX | minX | maxY | minY | Check |
|------|-----|------|-------|-------|------|------|------|------|-------|
| 1 | 0 | 2 | 1 | 1 | 1 | 2 | 3 | 4 | 1≤4 ✅, 3≤2 ❌ → low=2 |
| 2 | 2 | 2 | 2 | 0 | 2 | ∞ | -∞ | 3 | 2≤3 ✅, -∞≤∞ ✅ → Found! |

Even total → median = (max(2,-∞) + min(∞,3)) / 2 = (2 + 3) / 2 = **2.5** ✅

---

## Visual Explanation of the Partition Concept

```
Goal: Split combined sorted array into two equal halves

nums1:  [...left1... | ...right1...]
nums2:  [...left2... | ...right2...]
         ←── left half ──→  ←── right half ──→

Conditions for correct partition:
  1. maxX ≤ minY  (largest on left of nums1 ≤ smallest on right of nums2)
  2. maxY ≤ minX  (largest on left of nums2 ≤ smallest on right of nums1)

If maxX > minY → we took too many from nums1, move partition left
If maxY > minX → we took too few from nums1, move partition right
```

---

## Why Binary Search on the Smaller Array?

- We binary search on `partitionX` (0 to x), so the search space is the length of nums1
- Using the smaller array minimizes the search space: O(log(min(m,n)))
- `partitionY` is derived: `(x+y+1)/2 - partitionX` — no separate search needed

---

## Edge Cases (MIN_VALUE / MAX_VALUE)

```
partitionX = 0 → nothing taken from nums1 left → maxX = -∞ (always valid)
partitionX = x → everything taken from nums1 → minX = +∞ (always valid)
Same logic for partitionY
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Merge + Sort | O((m+n) log(m+n)) | O(m+n) | Simple but slow |
| Binary Search on Partition | O(log(min(m,n))) | O(1) | Best — partition-based |

**Interview tip:** This is one of the hardest binary search problems. The key idea: "I'm not searching for a value — I'm searching for the correct partition point that splits both arrays into a valid left and right half." Walk through the conditions: maxX ≤ minY and maxY ≤ minX. Explain why you search on the smaller array (smaller search space). Use -∞/+∞ for empty partitions. Draw the partition diagram — it makes the logic click visually.
