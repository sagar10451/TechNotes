# 5. Factor Combinations (LeetCode 254)

**Problem:** Given integer `n`, return all possible combinations of its factors (excluding 1 and n). Factors in each combination must be in non-decreasing order.

**Example:**
- Input: `n = 12` → Output: `[[2,6],[2,2,3],[3,4]]`
- Input: `n = 8` → Output: `[[2,4],[2,2,2]]`

## Solution: Backtracking on Factors

**Approach:** For each factor `i` from `start` to `√remain`, if `i` divides `remain`:
1. Add `[...current, i, remain/i]` as a valid combination
2. Recurse with `remain/i` to find deeper factorizations

The bound `i <= remain/i` ensures factors are in non-decreasing order and avoids duplicates.

- **Time:** O(2^m) where m = number of factors of n
- **Space:** O(log n) — recursion depth (max factors in a combination)

```java
class Solution {
    public List<List<Integer>> getFactors(int n) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), n, 2);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int remain, int start) {
        for (int i = start; i <= remain / i; i++) { // only up to √remain
            if (remain % i == 0) {
                temp.add(i);

                // Add [current factors..., i, remain/i] as a combination
                List<Integer> combination = new ArrayList<>(temp);
                combination.add(remain / i);
                result.add(combination);

                // Recurse to further factorize remain/i
                backtrack(result, temp, remain / i, i);

                temp.remove(temp.size() - 1); // backtrack
            }
        }
    }
}
```

**Dry Run** `n = 12`:

```
i=2: 12%2==0
  temp=[2], add [2,6] to result ✅
  recurse(remain=6, start=2):
    i=2: 6%2==0
      temp=[2,2], add [2,2,3] to result ✅
      recurse(remain=3, start=2): no factors ≤ √3
      backtrack → [2]
  backtrack → []
i=3: 12%3==0
  temp=[3], add [3,4] to result ✅
  recurse(remain=4, start=3): no factors 3 ≤ √4
  backtrack → []
i=4: 4 > 12/4=3? No, 4 > 3 → stop
```

Result: `[[2,6],[2,2,3],[3,4]]` ✅

**Interview tip:** The clever part is `i <= remain/i` — this ensures each factor is ≤ the remaining quotient, which automatically produces non-decreasing order and avoids duplicates like [6,2]. Also, adding `[...temp, remain/i]` at each step captures the "two-factor" split before recursing deeper.
