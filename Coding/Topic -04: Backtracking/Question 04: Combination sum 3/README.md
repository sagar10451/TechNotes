# 4. Combination Sum III (LeetCode 216)

**Problem:** Find all combinations of `k` numbers from 1–9 that sum to `n`. Each number used at most once.

**Example:**
- Input: `k = 3, n = 7` → Output: `[[1,2,4]]`
- Input: `k = 3, n = 9` → Output: `[[1,2,6],[1,3,5],[2,3,4]]`

## Solution: Backtracking (Fixed Size, Fixed Range)

**Approach:** Backtrack through numbers 1–9. Stop if size > k or remain < 0. Add to result when size == k and remain == 0.

- **Time:** O(C(9,k)) — at most C(9,k) valid combinations
- **Space:** O(k) — recursion depth

```java
class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), k, n, 1);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int k, int remain, int start) {
        if (temp.size() > k) return;
        if (temp.size() == k && remain == 0) {
            result.add(new ArrayList<>(temp));
            return;
        }

        for (int i = start; i <= 9; i++) {
            temp.add(i);
            backtrack(result, temp, k, remain - i, i + 1);
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Dry Run** `k=3, n=7`:

```
add 1 → remain=6
  add 2 → remain=4
    add 3 → remain=1, size=3 ✗ (remain≠0)
    add 4 → remain=0, size=3 ✅ → [1,2,4]
    add 5 → remain=-1 ✗
  add 3 → remain=3
    add 4 → remain=-1 ✗ (wait: 3-4=-1? No: remain=6-3=3, then 3-4=-1) ✗
    Actually: remain after add 3 = 6-3=3, then add 4 → 3-4=-1 ✗
  add 5 → remain=1, then need 1 more number ≥6 to make 1 → impossible
add 2 → remain=5
  add 3 → remain=2
    add 4+ → all too large ✗
```

Result: `[[1,2,4]]` ✅

**Interview tip:** This is the simplest combination sum variant — fixed range (1–9), no duplicates in input, each used once. The two base cases (size check + sum check) make it clean. Mention it's a constrained version of Combination Sum II with range [1,9].
