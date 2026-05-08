# Find Smallest Letter Greater Than Target

**Problem:** Given a sorted array of characters `letters` (wraps around) and a `target` character, find the smallest character that is **strictly greater** than `target`. If no such character exists, wrap around and return the first character.

**Example:**
- Input: `letters = ['c', 'f', 'j']`, `target = 'a'` → Output: `'c'`
- Input: `letters = ['c', 'f', 'j']`, `target = 'c'` → Output: `'f'`
- Input: `letters = ['c', 'f', 'j']`, `target = 'j'` → Output: `'c'` (wrap around)

---

## Solution 1: Brute Force — Linear Scan

**Approach:** Iterate through the array and find the first letter greater than target (since array is sorted, the first one found is the smallest).

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public char nextGreatestLetter(char[] letters, char target) {
        for (char letter : letters) {
            if (letter > target) {
                return letter;
            }
        }
        return letters[0]; // wrap around
    }
}
```

Note: Since the array is sorted, the first letter greater than target is already the smallest — no need to track a minimum.

---

## Solution 2: Optimal — Binary Search

**Approach:** Binary search for the leftmost letter strictly greater than target:
- If `letters[mid] <= target` → answer is to the right (`left = mid + 1`)
- If `letters[mid] > target` → could be the answer, search left (`right = mid - 1`)

After the loop, `left` points to the answer. If `left == letters.length`, wrap around to index 0.

- **Time:** O(log n)
- **Space:** O(1)

```java
class Solution {
    public char nextGreatestLetter(char[] letters, char target) {
        int left = 0, right = letters.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (letters[mid] <= target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        // If left goes out of bounds, wrap around
        return left < letters.length ? letters[left] : letters[0];
    }
}
```

**Dry Run** with `['c', 'f', 'j']`, `target = 'c'`:

| Step | left | right | mid | letters[mid] | Action |
|------|------|-------|-----|-------------|--------|
| 1 | 0 | 2 | 1 | 'f' | 'f' > 'c', right = 0 |
| 2 | 0 | 0 | 0 | 'c' | 'c' ≤ 'c', left = 1 |
| 3 | left (1) > right (0) | Exit | | | |

Result: `letters[1]` = **'f'** ✅

**Dry Run** with `['c', 'f', 'j']`, `target = 'j'` (wrap around case):

| Step | left | right | mid | letters[mid] | Action |
|------|------|-------|-----|-------------|--------|
| 1 | 0 | 2 | 1 | 'f' | 'f' ≤ 'j', left = 2 |
| 2 | 2 | 2 | 2 | 'j' | 'j' ≤ 'j', left = 3 |
| 3 | left (3) > right (2) | Exit | | | |

`left = 3 >= letters.length` → wrap around: `letters[0]` = **'c'** ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Linear Scan | O(n) | O(1) | First element > target in sorted array |
| Binary Search | O(log n) | O(1) | Best — find leftmost element > target |

**Interview tip:** This is a standard "lower bound" binary search variant. The key detail: we use `<=` (not `<`) in the condition because we need **strictly greater** than target (equal doesn't count). After the loop, `left` is the insertion point for "first element > target." If it's out of bounds, wrap around. Clean and simple.
