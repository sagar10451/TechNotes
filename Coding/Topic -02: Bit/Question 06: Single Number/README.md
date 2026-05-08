# Single Number

**Problem:** Given a non-empty array where every element appears **twice** except for one, find that single element.

**Example:**
- Input: `[4, 1, 2, 1, 2]` → Output: `4`
- Input: `[2, 2, 1]` → Output: `1`

---

## Solution 1: HashMap — Count Occurrences

**Approach:** Count occurrences of each number using a HashMap. Return the one with count 1.

- **Time:** O(n)
- **Space:** O(n)

```java
class Solution {
    public int singleNumber(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();

        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        for (int key : map.keySet()) {
            if (map.get(key) == 1) return key;
        }

        return 0;
    }
}
```

**Dry Run** with `[4, 1, 2, 1, 2]`:

| Step | Map State |
|------|-----------|
| num=4 | {4:1} |
| num=1 | {4:1, 1:1} |
| num=2 | {4:1, 1:1, 2:1} |
| num=1 | {4:1, 1:2, 2:1} |
| num=2 | {4:1, 1:2, 2:2} |

Scan map → key `4` has count 1 → return **4** ✅

---

## Solution 2: Optimal — XOR

**Approach:** XOR all elements together. Since `x ^ x = 0` and `x ^ 0 = x`, all pairs cancel out, leaving only the single number.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int singleNumber(int[] nums) {
        int result = 0;

        for (int num : nums) {
            result ^= num;
        }

        return result;
    }
}
```

**Why it works:**
```
XOR properties:
  x ^ x = 0    (same numbers cancel)
  x ^ 0 = x    (XOR with 0 keeps the value)
  XOR is commutative and associative (order doesn't matter)

[4, 1, 2, 1, 2]
= 4 ^ 1 ^ 2 ^ 1 ^ 2
= 4 ^ (1 ^ 1) ^ (2 ^ 2)
= 4 ^ 0 ^ 0
= 4
```

**Dry Run** with `[4, 1, 2, 1, 2]`:

| Step | num | result | Binary |
|------|-----|--------|--------|
| Start | — | 0 | 000 |
| 1 | 4 | 0 ^ 4 = 4 | 100 |
| 2 | 1 | 4 ^ 1 = 5 | 101 |
| 3 | 2 | 5 ^ 2 = 7 | 111 |
| 4 | 1 | 7 ^ 1 = 6 | 110 |
| 5 | 2 | 6 ^ 2 = 4 | 100 |

Result: **4** ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| HashMap | O(n) | O(n) | Count occurrences |
| XOR | O(n) | O(1) | Best — one line of logic, no extra space |

**Interview tip:** Mention HashMap as the straightforward approach, then immediately go to XOR: "Since every number appears twice except one, XOR cancels all pairs — `x ^ x = 0` — leaving only the single number." It's a 3-line solution that shows you understand bit manipulation properties. This is one of the classic XOR interview problems.
