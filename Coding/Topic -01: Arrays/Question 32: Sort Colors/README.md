# Sort Colours (Dutch National Flag)

**Problem:** Given an array `nums` with values `0`, `1`, and `2` (representing red, white, blue), sort them **in-place** so all 0s come first, then 1s, then 2s. Do it in one pass without using a library sort.

**Example:**
- Input: `[2, 0, 2, 1, 1, 0]` → Output: `[0, 0, 1, 1, 2, 2]`
- Input: `[2, 0, 1]` → Output: `[0, 1, 2]`

---

## Solution 1: Brute Force — Dummy Array (Three Pass)

**Approach:** Create a new array. First pass: copy all 0s. Second pass: copy all 1s. Third pass: copy all 2s. Copy back.

- **Time:** O(n)
- **Space:** O(n)

```java
class Solution {
    public void sortColors(int[] nums) {
        int[] dummy = new int[nums.length];
        int index = 0;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 0) dummy[index++] = 0;
        }
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 1) dummy[index++] = 1;
        }
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 2) dummy[index++] = 2;
        }

        System.arraycopy(dummy, 0, nums, 0, nums.length);
    }
}
```

---

## Solution 2: Optimal — Dutch National Flag (Single Pass)

**Approach:** Use three pointers:
- `low` — everything before `low` is 0
- `mid` — current element being examined
- `high` — everything after `high` is 2

Traverse with `mid`. Swap 0s to the left, 2s to the right, skip 1s.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;

        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else { // nums[mid] == 2
                swap(nums, mid, high);
                high--;
                // don't increment mid — need to check swapped value
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

**Dry Run** with `[2, 0, 2, 1, 1, 0]`:

| Step | low | mid | high | nums[mid] | Action | Array |
|------|-----|-----|------|-----------|--------|-------|
| 1 | 0 | 0 | 5 | 2 | Swap mid↔high, high-- | [0, 0, 2, 1, 1, 2] |
| 2 | 0 | 0 | 4 | 0 | Swap low↔mid, low++, mid++ | [0, 0, 2, 1, 1, 2] |
| 3 | 1 | 1 | 4 | 0 | Swap low↔mid, low++, mid++ | [0, 0, 2, 1, 1, 2] |
| 4 | 2 | 2 | 4 | 2 | Swap mid↔high, high-- | [0, 0, 1, 1, 2, 2] |
| 5 | 2 | 2 | 3 | 1 | mid++ | [0, 0, 1, 1, 2, 2] |
| 6 | 2 | 3 | 3 | 1 | mid++ | [0, 0, 1, 1, 2, 2] |

mid (4) > high (3) → done. Result: `[0, 0, 1, 1, 2, 2]` ✅

---

## Why We Don't Increment `mid` After Swapping With `high`

When we swap `nums[mid]` with `nums[high]`, the value that comes from `high` could be 0, 1, or 2 — we haven't examined it yet. So we must check it again before moving forward.

When we swap with `low`, the value that comes from `low` is always 0 or 1 (already processed), so it's safe to increment `mid`.

---

## Summary

| Solution | Time | Space | Passes | Notes |
|----------|------|-------|--------|-------|
| Dummy Array | O(n) | O(n) | 3 passes | Simple but extra space |
| Dutch National Flag | O(n) | O(1) | 1 pass | Best — in-place, single pass |

**Interview tip:** Mention the brute force (or even simpler: just count 0s, 1s, 2s and overwrite). Then go to Dutch National Flag. The key detail interviewers test: "why don't you increment `mid` after swapping with `high`?" — because the swapped-in value is unexamined. That one sentence shows you truly understand the algorithm.
