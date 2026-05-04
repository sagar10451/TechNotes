# Summary Ranges

**Problem:** Given a sorted unique integer array `nums`, return the smallest sorted list of ranges that cover all numbers. Each range `[a, b]` is represented as `"a->b"` if `a != b`, or `"a"` if `a == b`.

**Example:**
- Input: `[0, 1, 2, 4, 5, 7]` → Output: `["0->2", "4->5", "7"]`
- Input: `[0, 2, 3, 4, 6, 8, 9]` → Output: `["0", "2->4", "6", "8->9"]`

---

## Solution: Single Pass with Two Pointers

**Approach:** Use pointer `i` to mark the start of a range and `j` to find the end. Advance `j` while elements are consecutive (`nums[j] + 1 == nums[j+1]`). If `i == j`, it's a single number. Otherwise, format as `"start->end"`. Move `i` to `j + 1` and repeat.

- **Time:** O(n) — each element visited once
- **Space:** O(1) — excluding the output list

```java
class Solution {
    public List<String> summaryRanges(int[] nums) {
        int n = nums.length;
        List<String> ans = new ArrayList<>();
        int i = 0;

        while (i < n) {
            int j = i;

            // Extend j while consecutive
            while (j < n - 1 && nums[j] + 1 == nums[j + 1]) {
                j++;
            }

            // Single number or range
            if (i == j) {
                ans.add(String.valueOf(nums[i]));
            } else {
                ans.add(nums[i] + "->" + nums[j]);
            }

            i = j + 1; // move to next range
        }

        return ans;
    }
}
```

**Dry Run** with `[0, 1, 2, 4, 5, 7]`:

| i | j (after inner loop) | Range Found | Output Added | Next i |
|---|----------------------|-------------|-------------|--------|
| 0 | 2 | 0,1,2 consecutive | "0->2" | 3 |
| 3 | 4 | 4,5 consecutive | "4->5" | 5 |
| 5 | 5 | 7 alone | "7" | 6 |

Result: `["0->2", "4->5", "7"]` ✅

**Dry Run** with `[0, 2, 3, 4, 6, 8, 9]`:

| i | j | Range | Output Added |
|---|---|-------|-------------|
| 0 | 0 | 0 alone | "0" |
| 1 | 3 | 2,3,4 consecutive | "2->4" |
| 4 | 4 | 6 alone | "6" |
| 5 | 6 | 8,9 consecutive | "8->9" |

Result: `["0", "2->4", "6", "8->9"]` ✅

---

## Summary

| Aspect | Detail |
|--------|--------|
| Time | O(n) |
| Space | O(1) excluding output |
| Pattern | Two pointers — `i` marks range start, `j` finds range end |
| Key check | `nums[j] + 1 == nums[j+1]` to detect consecutive elements |

**Interview tip:** This is a straightforward problem — there's no brute force to optimize from. The signal comes from clean code and handling edge cases: empty array, single element, all consecutive, no consecutive. Walk through the two-pointer logic clearly: "i anchors the start, j slides forward while consecutive, then I format and jump i past the range."
