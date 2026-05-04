# Remove Duplicates from Sorted Array II

**Problem:** Given a sorted integer array `nums`, remove duplicates **in-place** so each element appears **at most twice**. Return the new length `k` — the first `k` elements of `nums` should hold the valid values.

**Example:**
- Input: `[1, 1, 1, 2, 2, 3]` → Output: `5`, array becomes `[1, 1, 2, 2, 3, ...]`
- Input: `[0, 0, 1, 1, 1, 1, 2, 3, 3]` → Output: `7`, array becomes `[0, 0, 1, 1, 2, 3, 3, ...]`

---

## Solution 1: Compare With Element Two Ahead

**Approach:** For each element, check if it equals the element two positions ahead (`nums[i] == nums[i+2]`). If yes, it means there are more than 2 copies — skip it. Otherwise, keep it.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int count = 0;

        for (int i = 0; i < nums.length; i++) {
            // If current equals element 2 ahead, we have > 2 copies — skip
            if (i + 2 < nums.length && nums[i] == nums[i + 2]) {
                continue;
            }
            nums[count++] = nums[i];
        }

        return count;
    }
}
```

**Dry Run** with `[1, 1, 1, 2, 2, 3]`:

| i | nums[i] | nums[i+2] | Action | count | Array State |
|---|---------|-----------|--------|-------|-------------|
| 0 | 1 | 1 | Skip (1 == 1, more than 2) | 0 | [1, 1, 1, 2, 2, 3] |
| 1 | 1 | 2 | Keep → write at index 0 | 1 | [1, 1, 1, 2, 2, 3] |
| 2 | 1 | 2 | Keep → write at index 1 | 2 | [1, 1, 1, 2, 2, 3] |
| 3 | 2 | 3 | Keep → write at index 2 | 3 | [1, 1, 2, 2, 2, 3] |
| 4 | 2 | — | Keep → write at index 3 | 4 | [1, 1, 2, 2, 2, 3] |
| 5 | 3 | — | Keep → write at index 4 | 5 | [1, 1, 2, 2, 3, 3] |

Result: length = `5`, array = `[1, 1, 2, 2, 3]`

---

## Solution 2: Two Pointer — Compare With Written Output (Best for Interview)

**Approach:** Use a pointer `i` to track the write position. For each element, check if it's different from `nums[i - 2]` (the element two positions back in the *output*). If yes, it's safe to write — we haven't placed more than 2 copies yet.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 2) return nums.length;

        int i = 2; // first 2 elements are always valid

        for (int j = 2; j < nums.length; j++) {
            // Compare with element 2 back in the OUTPUT (not input)
            if (nums[j] != nums[i - 2]) {
                nums[i++] = nums[j];
            }
        }

        return i;
    }
}
```

**Dry Run** with `[1, 1, 1, 2, 2, 3]`:

| j | nums[j] | nums[i-2] | Action | i | Array State |
|---|---------|-----------|--------|---|-------------|
| 2 | 1 | 1 (idx 0) | Skip (1 == 1, already 2 copies) | 2 | [1, 1, 1, 2, 2, 3] |
| 3 | 2 | 1 (idx 0) | Write at index 2 | 3 | [1, 1, 2, 2, 2, 3] |
| 4 | 2 | 1 (idx 1) | Write at index 3 | 4 | [1, 1, 2, 2, 2, 3] |
| 5 | 3 | 2 (idx 2) | Write at index 4 | 5 | [1, 1, 2, 2, 3, 3] |

Result: length = `5`, array = `[1, 1, 2, 2, 3]`

---

## Why Solution 2 is Better for Interviews

| Aspect | Solution 1 | Solution 2 |
|--------|-----------|-----------|
| Logic | Compare with input array ahead | Compare with output array behind |
| Generalizable | Harder to adapt | Change `i - 2` to `i - k` for "allow k duplicates" |
| Edge cases | Needs boundary check (`i + 2 < length`) | Clean — no boundary check in loop |
| Interview signal | Correct but less standard | Shows two-pointer mastery, generalizable pattern |
| Complexity | O(n) time, O(1) space | O(n) time, O(1) space |

**Interview tip:** Solution 2 is the classic pattern. If the interviewer asks "what if we allow at most k duplicates?", you just change `2` to `k` — that's a strong signal.
