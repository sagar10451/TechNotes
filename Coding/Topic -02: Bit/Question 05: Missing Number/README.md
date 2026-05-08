# Missing Number

**Problem:** Given an array `nums` containing `n` distinct numbers from the range `[0, n]`, find the one number that is missing.

**Example:**
- Input: `[3, 0, 1]` → Output: `2`
- Input: `[0, 1]` → Output: `2`
- Input: `[9, 6, 4, 2, 3, 5, 7, 0, 1]` → Output: `8`

---

## Solution 1: Brute Force — Nested Loops

**Approach:** For each number `i` from 0 to n, scan the array to check if `i` exists. Return the first number not found.

- **Time:** O(n²)
- **Space:** O(1)

```java
class Solution {
    public int missingNumber(int[] nums) {
        for (int i = 0; i <= nums.length; i++) {
            boolean found = false;
            for (int j = 0; j < nums.length; j++) {
                if (nums[j] == i) {
                    found = true;
                    break;
                }
            }
            if (!found) return i;
        }
        return -1;
    }
}
```

---

## Solution 2: Sum Formula

**Approach:** The sum of `[0, n]` is `n*(n+1)/2`. Subtract the actual array sum — the difference is the missing number.

- **Time:** O(n)
- **Space:** O(1)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length;
        int expectedSum = n * (n + 1) / 2;
        int actualSum = 0;

        for (int num : nums) {
            actualSum += num;
        }

        return expectedSum - actualSum;
    }
}
```

**Dry Run** with `[3, 0, 1]`:
- n = 3, expectedSum = 3×4/2 = 6
- actualSum = 3 + 0 + 1 = 4
- Missing = 6 - 4 = **2** ✅

---

## Solution 3: Optimal — XOR

**Approach:** XOR all numbers from `0` to `n`, then XOR all elements in the array. Every number that appears in both cancels out (since `x ^ x = 0`), leaving only the missing number.

- **Time:** O(n)
- **Space:** O(1)
- **Advantage over sum:** No risk of integer overflow for very large n.

```java
class Solution {
    public int missingNumber(int[] nums) {
        int ans = 0;

        // XOR with all numbers 0 to n
        for (int i = 0; i <= nums.length; i++) {
            ans ^= i;
        }

        // XOR with all array elements
        for (int num : nums) {
            ans ^= num;
        }

        return ans;
    }
}
```

**Dry Run** with `[3, 0, 1]` (n = 3):

| Step | Operation | ans |
|------|-----------|-----|
| XOR 0–3 | 0^1^2^3 | 0 (binary: 00 ^ 01 ^ 10 ^ 11 = 00) |
| Wait, let's trace properly: | | |
| i=0 | ans = 0 ^ 0 = 0 | 0 |
| i=1 | ans = 0 ^ 1 = 1 | 1 |
| i=2 | ans = 1 ^ 2 = 3 | 3 |
| i=3 | ans = 3 ^ 3 = 0 | 0 |
| XOR array: | | |
| num=3 | ans = 0 ^ 3 = 3 | 3 |
| num=0 | ans = 3 ^ 0 = 3 | 3 |
| num=1 | ans = 3 ^ 1 = 2 | 2 |

Result: **2** ✅

**Why it works:** `(0^1^2^3) ^ (3^0^1)` = all pairs cancel → only `2` remains.

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Nested Loops | O(n²) | O(1) | Brute force, check each number |
| Sum Formula | O(n) | O(1) | Simple math, slight overflow risk |
| XOR | O(n) | O(1) | Best — no overflow, elegant |

**Interview tip:** Start with brute force (nested loops). Then optimize with sum formula — "expected sum minus actual sum gives the missing number." Then mention XOR as the cleanest approach: "every number that exists in both the range and the array cancels out via XOR, leaving only the missing one." If asked "why XOR over sum?", answer: "XOR avoids potential integer overflow for very large n."
