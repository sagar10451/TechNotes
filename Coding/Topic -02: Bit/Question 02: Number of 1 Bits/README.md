# Number of 1 Bits (Hamming Weight)

**Problem:** Given an integer `n`, return the number of `1` bits in its binary representation.

**Example:**
- Input: `n = 11` (binary `1011`) → Output: `3`
- Input: `n = 128` (binary `10000000`) → Output: `1`

---

## Solution 1: Check Each Bit (Right Shift)

**Approach:** Check the least significant bit using `n & 1`. If it's 1, increment count. Then unsigned right-shift `n` by 1. Repeat until `n` is 0.

- **Time:** O(32) — always checks all 32 bits in worst case
- **Space:** O(1)

```java
class Solution {
    public int hammingWeight(int n) {
        int ones = 0;

        while (n != 0) {
            ones += (n & 1);  // add LSB to count
            n = n >>> 1;       // unsigned right shift
        }

        return ones;
    }
}
```

**Why `>>>` instead of `>>`?** The `>>` operator propagates the sign bit (fills with 1 for negative numbers), creating an infinite loop. `>>>` always fills with 0.

**Dry Run** with `n = 11` (binary `1011`):

| Step | n (binary) | n & 1 | ones | n >>> 1 |
|------|-----------|-------|------|---------|
| 1 | 1011 | 1 | 1 | 101 |
| 2 | 101 | 1 | 2 | 10 |
| 3 | 10 | 0 | 2 | 1 |
| 4 | 1 | 1 | 3 | 0 |

n = 0, exit. Result: **3** ✅

---

## Solution 2: Optimal — Brian Kernighan's Trick (`n & (n-1)`)

**Approach:** The expression `n & (n - 1)` clears the lowest set bit of `n`. Repeat until `n` is 0. The number of iterations equals the number of 1-bits — no wasted iterations on 0-bits.

- **Time:** O(k) where k = number of 1-bits (at most 32)
- **Space:** O(1)

```java
class Solution {
    public int hammingWeight(int n) {
        int ones = 0;

        while (n != 0) {
            n = n & (n - 1);  // clear lowest set bit
            ones++;
        }

        return ones;
    }
}
```

**Why does `n & (n-1)` clear the lowest set bit?**
```
n     = 1100  (12)
n-1   = 1011  (11)
n&n-1 = 1000  (8)   ← lowest 1-bit is gone
```
Subtracting 1 flips all bits from the rightmost 1-bit downward. AND-ing with the original cancels that bit.

**Dry Run** with `n = 11` (binary `1011`):

| Step | n (binary) | n-1 (binary) | n & (n-1) | ones |
|------|-----------|-------------|-----------|------|
| 1 | 1011 | 1010 | 1010 | 1 |
| 2 | 1010 | 1001 | 1000 | 2 |
| 3 | 1000 | 0111 | 0000 | 3 |

n = 0, exit. Result: **3** ✅ (only 3 iterations instead of 4)

---

## Summary

| Solution | Time | Space | Iterations | Notes |
|----------|------|-------|-----------|-------|
| Check Each Bit | O(32) | O(1) | Always up to 32 | Simple, easy to explain |
| Brian Kernighan's | O(k) | O(1) | Only k (number of 1-bits) | Faster, elegant |

**Interview tip:** Start with Solution 1 — it's intuitive and shows you understand bit manipulation basics. Then say "I can optimize: instead of checking every bit, `n & (n-1)` clears the lowest set bit in one step, so I only loop k times where k is the number of 1-bits." Explain *why* it works (subtracting 1 flips bits below the lowest 1) — that's the signal.
