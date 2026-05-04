# Remove Element

**Problem:** Given an integer array `nums` and a value `val`, remove all occurrences of `val` **in-place**. Return the new length `k` — the first `k` elements of `nums` should hold the values not equal to `val`.

**Example:**
- Input: `nums = [3, 2, 2, 3]`, `val = 3` → Output: `2`, array becomes `[2, 2, ...]`
- Input: `nums = [0, 1, 2, 2, 3, 0, 4, 2]`, `val = 2` → Output: `5`, array becomes `[0, 1, 3, 0, 4, ...]`

---

## Solution 1: Single Pass (Keep Non-Matching)

**Approach:** Use a pointer `count` to track where to write the next valid element. Scan through the array — if the element is not `val`, write it at `count` and move forward.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int count = 0;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != val) {
                nums[count++] = nums[i];
            }
        }

        return count;
    }
}
```

**Dry Run** with `nums = [3, 2, 2, 3]`, `val = 3`:

| i | nums[i] | Action | count | Array State |
|---|---------|--------|-------|-------------|
| 0 | 3 | Skip (equals val) | 0 | [3, 2, 2, 3] |
| 1 | 2 | Write at index 0 | 1 | [2, 2, 2, 3] |
| 2 | 2 | Write at index 1 | 2 | [2, 2, 2, 3] |
| 3 | 3 | Skip (equals val) | 2 | [2, 2, 2, 3] |

Result: length = `2`, array = `[2, 2]`

---

## Solution 2: Two Pointer Swap (Best When Few Removals)

**Approach:** Use two pointers — `i` from the start and `n` from the end. When `nums[i] == val`, replace it with the last element and shrink the array. Avoids unnecessary copies when removals are rare.

- **Time:** O(n)
- **Space:** O(1)
- **Note:** Does not preserve original order.

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int i = 0;
        int n = nums.length;

        while (i < n) {
            if (nums[i] == val) {
                nums[i] = nums[n - 1]; // replace with last
                n--;                    // shrink array
            } else {
                i++;
            }
        }

        return n;
    }
}
```

**Dry Run** with `nums = [3, 2, 2, 3]`, `val = 3`:

| Step | i | n | nums[i] | Action | Array State |
|------|---|---|---------|--------|-------------|
| 1 | 0 | 4 | 3 | Swap with last, shrink | [3, 2, 2, 3] → n=3 |
| 2 | 0 | 3 | 3 | Swap with last, shrink | [2, 2, 2, _] → n=2 |
| 3 | 0 | 2 | 2 | Not val, move i | i=1 |
| 4 | 1 | 2 | 2 | Not val, move i | i=2, done |

Result: length = `2`, array = `[2, 2]`

---

## When to Use Which

| Aspect | Solution 1 | Solution 2 |
|--------|-----------|-----------|
| Preserves order | ✅ Yes | ❌ No |
| Writes needed | Every non-val element | Only when val is found |
| Best when | General case | Few elements to remove |
| Interview pick | ✅ Default choice (simple, stable) | Mention as optimization |
| Complexity | O(n) time, O(1) space | O(n) time, O(1) space |

**Interview tip:** Lead with Solution 1 — it's clean and preserves order. Mention Solution 2 as an optimization: "If removals are rare, I can swap with the end to minimize writes, but it won't preserve order."
