# LeetCode 31 — Next Permutation

## Problem

Given an array of integers, rearrange it into the **next lexicographically greater permutation**. If the array is the last permutation (fully descending), rearrange it to the **first** (fully ascending).

**Examples:**
- `[1, 2, 3]` → `[1, 3, 2]`
- `[3, 2, 1]` → `[1, 2, 3]` (wrap around)
- `[1, 1, 5]` → `[1, 5, 1]`

---

## 1. Brute Force

**Approach:**
1. Generate **all permutations** using backtracking.
2. Sort them in lexicographical order.
3. Find the current permutation in the list.
4. Return the next one (or first if current is last).

| Time | Space |
|------|-------|
| O(n! × n) | O(n!) |

```java
import java.util.*;

public class Solution {
    public void nextPermutation(int[] nums) {
        List<List<Integer>> permutations = new ArrayList<>();
        backtrack(permutations, new ArrayList<>(), nums, new boolean[nums.length]);

        // Sort lexicographically
        Collections.sort(permutations, (a, b) -> {
            for (int i = 0; i < Math.min(a.size(), b.size()); i++) {
                if (!a.get(i).equals(b.get(i))) return a.get(i) - b.get(i);
            }
            return a.size() - b.size();
        });

        // Find current and get next
        List<Integer> current = new ArrayList<>();
        for (int num : nums) current.add(num);

        int idx = permutations.indexOf(current);
        List<Integer> next = (idx + 1 >= permutations.size())
            ? permutations.get(0)
            : permutations.get(idx + 1);

        for (int i = 0; i < nums.length; i++) nums[i] = next.get(i);
    }

    private void backtrack(List<List<Integer>> perms, List<Integer> temp, int[] nums, boolean[] used) {
        if (temp.size() == nums.length) {
            perms.add(new ArrayList<>(temp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            used[i] = true;
            temp.add(nums[i]);
            backtrack(perms, temp, nums, used);
            temp.remove(temp.size() - 1);
            used[i] = false;
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {1, 2, 3};
        s.nextPermutation(nums);
        System.out.println(Arrays.toString(nums)); // [1, 3, 2]
    }
}
```

**Why it's slow:** Generates all n! permutations just to find the next one.

---

## 2. ✅ Optimal — Find Break Point + Swap + Reverse

**Key Idea:** You don't need all permutations. Just find the **rightmost place** where you can make the number slightly bigger.

**Algorithm:**
1. **Find break point:** From the right, find first `i` where `nums[i] < nums[i+1]`. Everything to the right of `i` is in descending order.
2. **Find swap target:** From the right, find first `j` where `nums[j] > nums[i]`. This is the smallest number bigger than `nums[i]` in the suffix.
3. **Swap** `nums[i]` and `nums[j]`.
4. **Reverse** everything after index `i` (turn descending suffix into ascending → smallest possible).

If no break point found (`i < 0`) → array is fully descending → just reverse the whole thing.

| Time | Space |
|------|-------|
| **O(n)** | **O(1)** |

```java
import java.util.Arrays;

public class Solution {
    public void nextPermutation(int[] nums) {
        int n = nums.length;
        int i = n - 2;

        // Step 1: Find break point (first decreasing from right)
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        // Step 2: Find next greater element and swap
        if (i >= 0) {
            int j = n - 1;
            while (nums[j] <= nums[i]) {
                j--;
            }
            swap(nums, i, j);
        }

        // Step 3: Reverse suffix after break point
        reverse(nums, i + 1, n - 1);
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            swap(nums, start, end);
            start++;
            end--;
        }
    }

    public static void main(String[] args) {
        Solution s = new Solution();

        int[] nums1 = {1, 2, 3};
        s.nextPermutation(nums1);
        System.out.println(Arrays.toString(nums1)); // [1, 3, 2]

        int[] nums2 = {3, 2, 1};
        s.nextPermutation(nums2);
        System.out.println(Arrays.toString(nums2)); // [1, 2, 3]

        int[] nums3 = {1, 1, 5};
        s.nextPermutation(nums3);
        System.out.println(Arrays.toString(nums3)); // [1, 5, 1]
    }
}
```

### Dry Run (`nums = [1, 1, 5]`)

**Step 1 — Find break point:**
- `i = 1`: `nums[1] (1) < nums[2] (5)` → break point found at `i = 1`

**Step 2 — Find swap target:**
- `j = 2`: `nums[2] (5) > nums[1] (1)` → swap `nums[1]` and `nums[2]`
- Array: `[1, 5, 1]`

**Step 3 — Reverse suffix after i=1:**
- Only one element after index 1 → no change
- Array: `[1, 5, 1]` ✅

### Dry Run (`nums = [1, 2, 3]`)

```
Step 1: i=1 (nums[1]=2 < nums[2]=3) → break point
Step 2: j=2 (nums[2]=3 > nums[1]=2) → swap → [1, 3, 2]
Step 3: reverse after i=1 → only one element → [1, 3, 2] ✅
```

### Dry Run (`nums = [3, 2, 1]`)

```
Step 1: no break point found (fully descending) → i = -1
Step 2: skip (i < 0)
Step 3: reverse entire array → [1, 2, 3] ✅
```

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n! × n) | O(n!) | Generate all permutations, find next |
| 2 | **Break + Swap + Reverse** | **O(n)** | **O(1)** | **Find rightmost break, swap with next greater, reverse suffix** |

> 🔑 **Key takeaway:** Three steps — (1) find the break point from right where `nums[i] < nums[i+1]`, (2) swap with the smallest element greater than `nums[i]` in the suffix, (3) reverse the suffix to get the smallest arrangement.
