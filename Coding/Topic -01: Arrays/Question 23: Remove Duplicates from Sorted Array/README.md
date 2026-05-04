# Remove Duplicates from Sorted Array

**Problem:** Given a sorted integer array `nums`, remove duplicates **in-place** so each element appears only once. Return the new length `k` — the first `k` elements of `nums` should hold the unique values.

**Example:**
- Input: `[1, 1, 2]` → Output: `2`, array becomes `[1, 2, ...]`
- Input: `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` → Output: `5`, array becomes `[0, 1, 2, 3, 4, ...]`

---

## Solution 1: Compare With Next Element

**Approach:** Iterate through the array. If the current element equals the next one, skip it. Otherwise, place it at the `count` position.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int count = 0;

        for (int i = 0; i < nums.length; i++) {
            // If current equals next, skip (it's a duplicate)
            if (i + 1 < nums.length && nums[i] == nums[i + 1]) {
                continue;
            }
            // Otherwise, place the unique element
            nums[count++] = nums[i];
        }

        return count;
    }
}
```

**Dry Run** with `[1, 1, 2]`:

| i | nums[i] | nums[i+1] | Action | count | Array State |
|---|---------|-----------|--------|-------|-------------|
| 0 | 1 | 1 | Skip (duplicate) | 0 | [1, 1, 2] |
| 1 | 1 | 2 | Place at index 0 | 1 | [1, 1, 2] |
| 2 | 2 | — | Place at index 1 | 2 | [1, 2, 2] |

Result: length = `2`, array = `[1, 2]`

---

## Solution 2: Two Pointer (Cleaner — Best for Interview)

**Approach:** Use a slow pointer `i` that tracks where to write the next unique value. A fast pointer `j` scans ahead. Whenever `nums[j] != nums[i]`, we found a new unique element — move `i` forward and write it there.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) return 0;

        int i = 0; // slow pointer — last unique position

        for (int j = 1; j < nums.length; j++) {
            if (nums[j] != nums[i]) {
                i++;
                nums[i] = nums[j];
            }
        }

        return i + 1;
    }
}
```

**Dry Run** with `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`:

| j | nums[j] | nums[i] | Action | i | Array State |
|---|---------|---------|--------|---|-------------|
| 1 | 0 | 0 | Skip | 0 | [0, 0, 1, 1, 1, 2, 2, 3, 3, 4] |
| 2 | 1 | 0 | Write | 1 | [0, 1, 1, 1, 1, 2, 2, 3, 3, 4] |
| 3 | 1 | 1 | Skip | 1 | — |
| 4 | 1 | 1 | Skip | 1 | — |
| 5 | 2 | 1 | Write | 2 | [0, 1, 2, 1, 1, 2, 2, 3, 3, 4] |
| 6 | 2 | 2 | Skip | 2 | — |
| 7 | 3 | 2 | Write | 3 | [0, 1, 2, 3, 1, 2, 2, 3, 3, 4] |
| 8 | 3 | 3 | Skip | 3 | — |
| 9 | 4 | 3 | Write | 4 | [0, 1, 2, 3, 4, 2, 2, 3, 3, 4] |

Result: length = `5`, array = `[0, 1, 2, 3, 4]`

---

## Why Solution 2 is Better for Interviews

| Aspect | Solution 1 | Solution 2 |
|--------|-----------|-----------|
| Logic | Compare with next, skip duplicates | Classic two-pointer pattern |
| Edge case handling | Works but boundary check (`i+1 < length`) adds noise | Clean — no boundary check needed |
| Interview signal | Shows you can solve it | Shows you know the two-pointer pattern (reusable across many problems) |
| Complexity | O(n) time, O(1) space | O(n) time, O(1) space |

Both are correct and optimal. Solution 2 is the one interviewers expect because the two-pointer pattern applies to many similar problems (remove element, move zeroes, etc.).
