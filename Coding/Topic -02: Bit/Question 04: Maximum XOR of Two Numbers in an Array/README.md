# Maximum XOR of Two Numbers in an Array

**Problem:** Given an integer array `nums`, find the maximum XOR of any two elements in the array.

**Example:**
- Input: `[3, 10, 5, 25, 2, 8]` → Output: `28` (5 ^ 25 = 28)
  - Wait: `5 = 00101`, `25 = 11001`, `5 ^ 25 = 11100 = 28` ✅

---

## Solution 1: Brute Force — Check All Pairs

**Approach:** Try every pair `(i, j)` and track the maximum XOR.

- **Time:** O(n²)
- **Space:** O(1)

```java
class Solution {
    public int findMaximumXOR(int[] nums) {
        int maxXor = 0;

        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                maxXor = Math.max(maxXor, nums[i] ^ nums[j]);
            }
        }

        return maxXor;
    }
}
```

**Dry Run** with `[3, 10, 5, 25, 2, 8]`:

| Pair | XOR | maxXor |
|------|-----|--------|
| 3 ^ 10 | 9 | 9 |
| 3 ^ 25 | 26 | 26 |
| 5 ^ 25 | 28 | 28 |
| 25 ^ 2 | 27 | 28 |
| ... | ... | 28 |

Result: **28** (from 5 ^ 25) ✅

---

## Solution 2: Optimal — Binary Trie

**Approach:** Insert all numbers into a Trie in their 32-bit binary form. For each number, traverse the Trie greedily choosing the **opposite bit** at each level to maximize XOR.

**Why Trie?** XOR is maximized when bits differ. By storing all numbers in a binary Trie, we can greedily pick the path that gives the opposite bit at each position — this finds the best pair for each number in O(32) time.

- **Time:** O(n × 32) = O(n)
- **Space:** O(n × 32) = O(n)

```java
class Solution {
    class TrieNode {
        TrieNode left;  // represents bit 0
        TrieNode right; // represents bit 1
    }

    public int findMaximumXOR(int[] nums) {
        TrieNode root = new TrieNode();

        // Insert all numbers into Trie
        for (int num : nums) {
            insert(root, num);
        }

        // Find max XOR for each number
        int maxXor = 0;
        for (int num : nums) {
            maxXor = Math.max(maxXor, getMaxXor(root, num));
        }

        return maxXor;
    }

    private void insert(TrieNode root, int num) {
        TrieNode curr = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (bit == 0) {
                if (curr.left == null) curr.left = new TrieNode();
                curr = curr.left;
            } else {
                if (curr.right == null) curr.right = new TrieNode();
                curr = curr.right;
            }
        }
    }

    private int getMaxXor(TrieNode root, int num) {
        TrieNode curr = root;
        int xor = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (bit == 0) {
                // Want 1 (opposite) to maximize XOR
                if (curr.right != null) {
                    xor += (1 << i);
                    curr = curr.right;
                } else {
                    curr = curr.left;
                }
            } else {
                // Want 0 (opposite) to maximize XOR
                if (curr.left != null) {
                    xor += (1 << i);
                    curr = curr.left;
                } else {
                    curr = curr.right;
                }
            }
        }
        return xor;
    }
}
```

**Dry Run** — Finding max XOR for `num = 5` (binary `00...00101`):

The Trie contains all numbers. We traverse greedily choosing opposite bits:

```
5 in binary (last 5 bits): 0 0 1 0 1
```

| Bit Position | Bit of 5 | Want | Trie Has Opposite? | Take | XOR bit |
|-------------|----------|------|-------------------|------|---------|
| 31–5 | 0 | 1 | Depends on Trie | ... | ... |
| 4 | 0 | 1 → right | Yes (25 has 1 here) | right | 1 |
| 3 | 0 | 1 → right | Yes (25 has 1 here) | right | 1 |
| 2 | 1 | 0 → left | Yes (25 has 0 here) | left | 1 |
| 1 | 0 | 1 → right | No | left (0) | 0 |
| 0 | 1 | 0 → left | Yes (25 has 0 here) | left | 1 |

XOR result for bits 4–0: `11101` = 28 + leading zeros = **28**

The Trie guided us to pair 5 with 25, giving `5 ^ 25 = 28` ✅

---

## How the Trie Works Visually

```
Insert 5 (00101) and 25 (11001) into Trie (showing last 5 bits):

         root
        /    \
       0      1          ← bit 4
      / \      \
     0   ...    1        ← bit 3
     |          |
     1          0        ← bit 2
     |          |
     0          0        ← bit 1
     |          |
     1          1        ← bit 0
    (5)        (25)

For num=5, at each level we try to go OPPOSITE:
  bit4: 5 has 0, go right (1) → XOR gets 1
  bit3: 5 has 0, go right (1) → XOR gets 1
  bit2: 5 has 1, go left (0)  → XOR gets 1
  bit1: 5 has 0, go right (1) → not available, go left → XOR gets 0
  bit0: 5 has 1, go left (0)  → not available... (simplified)
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(n²) | O(1) | Check all pairs |
| Binary Trie | O(n) | O(n) | Greedy opposite-bit traversal |

**Interview tip:** Start with brute force — "I can check all pairs in O(n²)." Then optimize: "XOR is maximized when bits differ. If I store all numbers in a binary Trie, I can greedily pick the opposite bit at each level for any number — finding its best partner in O(32) time instead of O(n)." The key insight is: **at each bit position, always prefer the opposite bit to maximize XOR.** That greedy choice is what makes the Trie approach work.
