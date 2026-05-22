# 3. Combination Sum II (LeetCode 40)

**Problem:** Same as Combination Sum, but each number can only be used **once**, and the result must not contain duplicate combinations. Input may have duplicate numbers.

**Example:**
- Input: `candidates = [10,1,2,7,6,1,5]`, `target = 8`
- Output: `[[1,1,6],[1,2,5],[1,7],[2,6]]`

## Solution: Backtracking (Skip Duplicates)

**Approach:** Sort the array. Use `i+1` in recursion (no reuse). Skip duplicates with: `if (i > start && cand[i] == cand[i-1]) continue`.

- **Time:** O(2^n)
- **Space:** O(target/min_candidate)

```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
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
            if (i > start && cand[i] == cand[i - 1]) continue; // skip duplicates
            temp.add(cand[i]);
            backtrack(result, temp, cand, remain - cand[i], i + 1); // i+1, no reuse
            temp.remove(temp.size() - 1);
        }
    }
}
```

**Dry Run** `candidates=[1,1,2,5,6,7,10], target=8` (sorted):

```
add 1 (idx 0) → remain=7
  add 1 (idx 1) → remain=6
    add 6 → remain=0 → [1,1,6] ✅
  add 2 → remain=5
    add 5 → remain=0 → [1,2,5] ✅
  add 7 → remain=0 → [1,7] ✅
skip 1 (idx 1, duplicate of idx 0)
add 2 → remain=6
  add 6 → remain=0 → [2,6] ✅
```

Result: `[[1,1,6],[1,2,5],[1,7],[2,6]]` ✅

**Interview tip:** The duplicate skip condition `i > start` (not `i > 0`) is crucial. It allows using the same value at different recursion depths (like [1,1,6]) but prevents picking the same value at the same level twice.
