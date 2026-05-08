# Reverse Bits

**Problem:** Reverse the bits of a given 32-bit unsigned integer.

**Example:**
- Input: `43261596` → Binary: `00000010100101000001111010011100`
- Reversed: `00111001011110000010100101000000` → Output: `964176192`

---

## Solution: Bit-by-Bit Reversal

**Approach:** Build the result bit by bit. In each of 32 iterations:
1. Shift `res` left by 1 (make room for new bit)
2. Extract the LSB of `n` using `n & 1` and OR it into `res`
3. Right-shift `n` by 1 to process the next bit

After 32 iterations, `res` holds the reversed bits.

- **Time:** O(1) — always exactly 32 iterations
- **Space:** O(1)

```java
class Solution {
    public int reverseBits(int n) {
        int res = 0;

        for (int i = 0; i < 32; i++) {
            res = (res << 1) | (n & 1);  // shift res left, add LSB of n
            n = n >> 1;                   // move to next bit of n
        }

        return res;
    }
}
```

**Dry Run** with `n = 11` (binary `00000000000000000000000000001011`):

Showing first few and last iterations for clarity:

| i | n (last 4 bits) | n & 1 | res (last 4 bits) | Action |
|---|----------------|-------|-------------------|--------|
| 0 | 1011 | 1 | 0001 | res = 0<<1 \| 1 = 1 |
| 1 | 0101 | 1 | 0011 | res = 1<<1 \| 1 = 3 |
| 2 | 0010 | 0 | 0110 | res = 3<<1 \| 0 = 6 |
| 3 | 0001 | 1 | 1101 | res = 6<<1 \| 1 = 13 |
| 4 | 0000 | 0 | 11010 | res = 13<<1 \| 0 = 26 |
| 5–31 | 0000 | 0 | keeps shifting left | res keeps doubling |

After 32 iterations: `res = 11010000...0` (binary) = `3489660928`

Verification: reverse of `00000000000000000000000000001011` is `11010000000000000000000000000000` ✅

---

**Dry Run** with `n = 43261596`:

```
n binary:   00000010100101000001111010011100
reversed:   00111001011110000010100101000000 = 964176192 ✅
```

The loop processes bit 0 (rightmost) first and places it at bit 31 (leftmost) of `res`, bit 1 goes to bit 30, and so on.

---

## How It Works Visually

```
n:   [bit31][bit30]...[bit1][bit0]    (original)
res: [bit0] [bit1] ...[bit30][bit31]  (reversed)

Each iteration:
  - Extract rightmost bit of n
  - Push it into res from the right
  - After 32 pushes, the first extracted bit is now at position 31
```

---

## Summary

| Aspect | Detail |
|--------|--------|
| Time | O(1) — fixed 32 iterations |
| Space | O(1) |
| Key operation | `res = (res << 1) | (n & 1)` |
| Why it works | First bit extracted ends up shifted left 31 times → becomes MSB |

**Interview tip:** This is a straightforward bit manipulation problem. The key line to explain is `res = (res << 1) | (n & 1)` — "I shift my result left to make room, then OR in the current last bit of n." Walk through 3–4 iterations on the whiteboard to show how bits end up in reversed positions. If asked for follow-up optimization (called multiple times), mention you could use a lookup table to reverse 8 bits at a time.
