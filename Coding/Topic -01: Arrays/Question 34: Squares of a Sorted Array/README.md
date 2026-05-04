# Squares of a Sorted Array

**Problem:** Given a sorted array `nums` (may contain negatives), return an array of the squares of each number, also sorted in non-decreasing order.

**Key Insight:** The array is sorted but has negatives. Squaring flips the order of negatives: `(-4)² = 16` is larger than `(3)² = 9`. So the largest squares are at the two extremes of the array.

**Example:**
- Input: `[-4, -1, 0, 3, 10]` → Output: `[0, 1, 9, 16, 100]`

---

## Solution 1: Brute Force — Square and Sort

**Approach:** Square every element, then sort the result.

- **Time:** O(n log n) — sorting dominates
- **Space:** O(n)

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int[] res = new int[nums.length];

        for (int i = 0; i < nums.length; i++) {
            res[i] = nums[i] * nums[i];
        }

        Arrays.sort(res);
        return res;
    }
}
```

---

## Solution 2: Two Pointers — Find Minimum Square First

**Approach:**
1. Find the element with the smallest square (the value closest to 0)
2. Place it first in the result
3. Use two pointers expanding outward from that position, always picking the smaller square next

- **Time:** O(n)
- **Space:** O(n)

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];

        // Find index of element with smallest absolute value
        int minIdx = 0;
        for (int i = 1; i < n; i++) {
            if (Math.abs(nums[i]) < Math.abs(nums[minIdx])) {
                minIdx = i;
            }
        }

        // Place smallest square first
        res[0] = nums[minIdx] * nums[minIdx];
        int left = minIdx - 1, right = minIdx + 1;
        int idx = 1;

        // Expand outward, picking smaller square each time
        while (left >= 0 && right < n) {
            int sl = nums[left] * nums[left];
            int sr = nums[right] * nums[right];
            if (sl <= sr) {
                res[idx++] = sl;
                left--;
            } else {
                res[idx++] = sr;
                right++;
            }
        }
        while (left >= 0) {
            res[idx++] = nums[left] * nums[left];
            left--;
        }
        while (right < n) {
            res[idx++] = nums[right] * nums[right];
            right++;
        }

        return res;
    }
}
```

---

## Solution 3: Optimal — Two Pointers from Extremes

**Approach:** The largest squares are at the two ends of the array (leftmost negative or rightmost positive). Use two pointers at both ends, compare their squares, and fill the result array **from the back** (largest first).

- **Time:** O(n)
- **Space:** O(n) for result (O(1) extra beyond output)

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        int left = 0, right = n - 1;
        int j = n - 1; // fill from back

        while (left <= right) {
            int sl = nums[left] * nums[left];
            int sr = nums[right] * nums[right];

            if (sl >= sr) {
                res[j] = sl;
                left++;
            } else {
                res[j] = sr;
                right--;
            }
            j--;
        }

        return res;
    }
}
```

**Dry Run** with `[-4, -1, 0, 3, 10]`:

| Step | left | right | j | sl (left²) | sr (right²) | Pick | res |
|------|------|-------|---|-----------|------------|------|-----|
| 1 | 0 | 4 | 4 | 16 | 100 | right (100) | [_, _, _, _, 100] |
| 2 | 0 | 3 | 3 | 16 | 9 | left (16) | [_, _, _, 16, 100] |
| 3 | 1 | 3 | 2 | 1 | 9 | right (9) | [_, _, 9, 16, 100] |
| 4 | 1 | 2 | 1 | 1 | 0 | left (1) | [_, 1, 9, 16, 100] |
| 5 | 2 | 2 | 0 | 0 | 0 | either (0) | [0, 1, 9, 16, 100] |

Result: `[0, 1, 9, 16, 100]` ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Square + Sort | O(n log n) | O(n) | Simple but sorting is wasteful |
| Two Pointers from Min | O(n) | O(n) | Fill front-to-back from smallest square |
| Two Pointers from Extremes | O(n) | O(n) | Best — cleanest code, fill back-to-front |

**Interview tip:** Start with "square and sort" as brute force. Then the key insight: "the array is sorted, so the largest squares are at the two extremes — the most negative and the most positive compete for the biggest square." That naturally leads to Solution 3: two pointers from both ends, filling the result backwards. It's the cleanest and most elegant. Solution 2 works but has more edge-case code — mention it only if asked about alternatives.
