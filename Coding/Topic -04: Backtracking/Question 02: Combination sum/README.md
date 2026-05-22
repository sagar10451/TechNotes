# 2. Combination Sum (LeetCode 39)

**Problem:** Given an array of distinct integers `candidates` and a target, find all unique combinations where candidates sum to target. **Same number can be used unlimited times.**

**Example:**
- Input: `candidates = [2,3,6,7]`, `target = 7` → Output: `[[2,2,3],[7]]`

## Solution: Backtracking (Allow Reuse)

**Approach:** Sort candidates. For each number, either include it (and recurse with same index `i` to allow reuse) or skip to next. Stop when remaining < 0.

- **Time:** O(2^t) where t = target/min_candidate
- **Space:** O(target/min_candidate) — recursion depth

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(candidates);
        backtrack(result, new ArrayList<>(), candidates, target, 0);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> temp, int[] cand, int remain, int start) {
        if (remain < 0) return;
        if (remain == 0) {
            result.add(new ArrayList<>(temp));
            return;
        }

        for (int i = start; i < cand.length; i++) {
            temp.add(cand[i]);
            backtrack(result, temp, cand, remain - cand[i], i); // i, not i+1 (reuse allowed)
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Dry Run** `candidates=[2,3,6,7], target=7`:

```
add 2 → remain=5
  add 2 → remain=3
    add 2 → remain=1
      add 2 → remain=-1 ✗
      add 3 → remain=-2 ✗
    add 3 → remain=0 → [2,2,3] ✅
  add 3 → remain=2
    add 3 → remain=-1 ✗
  add 6 → remain=-1 ✗
add 3 → remain=4
  add 3 → remain=1 → no valid
add 6 → remain=1 → no valid
add 7 → remain=0 → [7] ✅
```

Result: `[[2,2,3],[7]]` ✅

**Interview tip:** The key difference from regular combinations: pass `i` (not `i+1`) in the recursive call to allow reusing the same element. Sorting helps prune early (once `cand[i] > remain`, all subsequent are too large).
