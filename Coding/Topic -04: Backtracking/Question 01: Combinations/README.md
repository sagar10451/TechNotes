# 1. Combinations (LeetCode 77)

**Problem:** Given two integers `n` and `k`, return all possible combinations of `k` numbers chosen from range `[1, n]`.

**Example:**
- Input: `n = 4, k = 2` → Output: `[[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]`

## Solution: Backtracking

**Approach:** Recursively build combinations by choosing numbers from `start` to `n`. At each step, add a number, recurse with `k-1`, then backtrack. Prune by stopping when not enough numbers remain (`i <= n - k + 1`).

- **Time:** O(C(n,k)) — generates all combinations
- **Space:** O(k) — recursion depth

```java
class Solution {
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), 1, n, k);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> comb, int start, int n, int k) {
        if (k == 0) {
            result.add(new ArrayList<>(comb));
            return;
        }

        for (int i = start; i <= n - k + 1; i++) { // pruning
            comb.add(i);
            backtrack(result, comb, i + 1, n, k - 1);
            comb.remove(comb.size() - 1); // backtrack
        }
    }
}
```

**Dry Run** `n=4, k=2`:

```
start=1: add 1 → [1]
  start=2: add 2 → [1,2] ✅ → backtrack → [1]
  start=3: add 3 → [1,3] ✅ → backtrack → [1]
  start=4: add 4 → [1,4] ✅ → backtrack → [1]
  backtrack → []
start=2: add 2 → [2]
  start=3: add 3 → [2,3] ✅ → backtrack → [2]
  start=4: add 4 → [2,4] ✅ → backtrack → [2]
  backtrack → []
start=3: add 3 → [3]
  start=4: add 4 → [3,4] ✅ → backtrack → [3]
  backtrack → []
```

Result: `[[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]` ✅

**Interview tip:** The pruning `i <= n - k + 1` is key — it ensures enough numbers remain to complete the combination. Without it, the code still works but explores dead-end branches.
