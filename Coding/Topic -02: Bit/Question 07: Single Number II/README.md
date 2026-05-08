# Single Number II

**Problem:** Given an integer array where every element appears **three times** except for one element which appears **once**, find that single element.

**Example:**
- Input: `[2, 2, 3, 2]` → Output: `3`
- Input: `[0, 1, 0, 1, 0, 1, 99]` → Output: `99`

---

## Solution 1: HashMap — Count Occurrences

**Approach:** Count occurrences of each number. Return the one with count 1.

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

---

## Solution 2: Sorting — Step by 3

**Approach:** Sort the array. Iterate in steps of 3. If the current element doesn't equal the next, it's the single number.

- **Time:** O(n log n)
- **Space:** O(1)

```java
class Solution {
    public int singleNumber(int[] nums) {
        Arrays.sort(nums);

        for (int i = 0; i < nums.length; i += 3) {
            if (i + 2 >= nums.length || nums[i] != nums[i + 1]) {
                return nums[i];
            }
        }

        return 0;
    }
}
```

**Dry Run** with `[2, 2, 3, 2]` → sorted: `[2, 2, 2, 3]`:

| i | nums[i] | nums[i+1] | Check | Result |
|---|---------|-----------|-------|--------|
| 0 | 2 | 2 | Same, skip | — |
| 3 | 3 | — | i+2 >= length | Return 3 ✅ |

---

## Solution 3: Optimal — Counting Bits (Mod 3)

**Approach:** For each of the 32 bit positions, count how many numbers have that bit set. If `count % 3 != 0`, that bit belongs to the single number.

**Why it works:** If a number appears 3 times, its contribution to each bit count is divisible by 3. The remainder must come from the single number.

- **Time:** O(32 × n) = O(n)
- **Space:** O(1)

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;

        for (int i = 0; i < 32; i++) {
            int count = 0;

            for (int num : nums) {
                if ((num & (1 << i)) != 0) {
                    count++;
                }
            }

            if (count % 3 != 0) {
                ans |= (1 << i);
            }
        }

        return ans;
    }
}
```

**Dry Run** with `[2, 2, 3, 2]`:

Binary: `2 = 10`, `3 = 11`

| Bit Position | Counts (2,2,3,2) | count | count % 3 | Set in ans? |
|-------------|-------------------|-------|-----------|-------------|
| 0 (ones) | 0+0+1+0 = 1 | 1 | 1 ≠ 0 | ✅ ans = 01 |
| 1 (twos) | 1+1+1+1 = 4 | 4 | 1 ≠ 0 | ✅ ans = 11 |
| 2–31 | 0 | 0 | 0 | ❌ |

Result: `ans = 11` (binary) = **3** ✅

---

## How Bit Counting Works Visually

```
Numbers:  2, 2, 3, 2
Binary:
  2 = 1 0
  2 = 1 0
  3 = 1 1
  2 = 1 0
      ───
Sum:  4  1    ← count of 1s at each position
%3:   1  1    ← remainder gives the single number's bits
Result: 1 1 = 3
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| HashMap | O(n) | O(n) | Simple counting |
| Sorting | O(n log n) | O(1) | Step by 3 after sort |
| Bit Counting (Mod 3) | O(n) | O(1) | Best — no extra space, works for any "appears k times" |

**Interview tip:** Start with HashMap (obvious). Then mention sorting as a space optimization (but worse time). Then go to bit counting: "For each bit position, I count how many numbers have that bit set. Since every number except one appears 3 times, the count mod 3 tells me whether the single number has that bit set." This generalizes to any "every element appears k times except one" — just change `% 3` to `% k`. That generalization is a strong interview signal.
