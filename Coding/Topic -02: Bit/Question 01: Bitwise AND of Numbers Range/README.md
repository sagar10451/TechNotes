# Bitwise AND of Numbers Range

**Problem:** Given two integers `left` and `right` (representing a range `[left, right]`), return the bitwise AND of all numbers in this range.

**Key Insight:** The bitwise AND of a range only preserves the **common prefix** of `left` and `right` in binary. Any bit position where they differ will become 0, because at least one number in the range will have a 0 at that position.

**Example:**
- Input: `left = 5`, `right = 7`
- Binary: `5 = 101`, `6 = 110`, `7 = 111`
- Common prefix: `1__` → Result: `100` = **4**

---

## Solution 1: Right Shift Until Equal (Best for Interview)

**Approach:** Keep right-shifting both `left` and `right` until they're equal — that's the common prefix. Then shift back left by the same amount to restore the magnitude.

- **Time:** O(log n) where n = max(left, right)
- **Space:** O(1)

```java
class Solution {
    public int rangeBitwiseAnd(int left, int right) {
        int shift = 0;

        while (left < right) {
            left >>= 1;
            right >>= 1;
            shift++;
        }

        return left << shift;
    }
}
```

**Dry Run** with `left = 5`, `right = 7`:

| Step | left | right | shift | Binary (left) | Binary (right) |
|------|------|-------|-------|---------------|----------------|
| Start | 5 | 7 | 0 | 101 | 111 |
| Shift 1 | 2 | 3 | 1 | 10 | 11 |
| Shift 2 | 1 | 1 | 2 | 1 | 1 |
| Equal! | — | — | — | — | — |

Result: `1 << 2` = `100` = **4** ✅

---

## Solution 2: Bit-by-Bit Comparison from MSB

**Approach:** Compare bits of `left` and `right` from the most significant bit (31st) down to 0. While they match, set that bit in the result. The moment they differ, stop — all lower bits will be 0.

- **Time:** O(1) — at most 32 iterations
- **Space:** O(1)

```java
class Solution {
    public int rangeBitwiseAnd(int left, int right) {
        int result = 0;

        for (int i = 31; i >= 0; i--) {
            int leftBit = (left >> i) & 1;
            int rightBit = (right >> i) & 1;

            if (leftBit == rightBit) {
                result |= (leftBit << i);
            } else {
                break; // all lower bits will be 0
            }
        }

        return result;
    }
}
```

**Dry Run** with `left = 5`, `right = 7`:

| i | leftBit | rightBit | Match? | Action | result |
|---|---------|----------|--------|--------|--------|
| 31–3 | 0 | 0 | ✅ | Set 0 (no change) | 0 |
| 2 | 1 | 1 | ✅ | Set bit 2 | 100 (4) |
| 1 | 0 | 1 | ❌ | Break! | 4 |

Result: **4** ✅

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Right Shift Until Equal | O(log n) | O(1) | Intuitive, easy to code |
| Bit-by-Bit from MSB | O(1) | O(1) | Explicit bit comparison |

**Interview tip:** Lead with Solution 1 — it's 5 lines and the logic is crystal clear: "I strip away differing bits from the right until left and right share the same prefix, then shift back." The key insight to state: "AND of a range preserves only the common bit prefix — any bit where left and right differ means some number in between has a 0 there, killing that bit." Solution 2 is an alternative if the interviewer asks for a different approach.
