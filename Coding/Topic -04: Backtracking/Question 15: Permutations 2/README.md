# Question 15: Permutations II

**Question:** https://leetcode.com/problems/permutations-ii/description/

## Explanation for Interview:

**Purpose:** The function generates all unique permutations of an array of integers, including those with duplicates.

**Backtracking Approach:**
- **Sorting:** We start by sorting the array to handle duplicates more efficiently.
- **used[] Array:** A boolean array (`used[]`) is used to track whether an element has been included in the current permutation.
- **Skip Duplicates:** If the current element is the same as the previous one and the previous one has not been used, we skip it to avoid generating duplicate permutations.
- **Recursive Exploration:** The function recursively adds elements to the tempList until it forms a complete permutation, at which point it is added to the result list (list).
- **Backtracking:** After each recursion, we "backtrack" by unmarking the element as unused and removing it from tempList to explore other potential permutations.

### Dry Run (For nums = [1, 1, 2]):

- Initial state: nums = [1, 1, 2] (sorted), tempList = [], used = [false, false, false]
- First level of recursion:
  - Add 1: tempList = [1], used = [true, false, false]
    - Add another 1: tempList = [1, 1], used = [true, true, false]
      - Add 2: tempList = [1, 1, 2], used = [true, true, true]
      - Complete permutation [1, 1, 2] → Backtrack
    - Remove 1, tempList = [1], used = [true, false, false]
    - Add 2: tempList = [1, 2], used = [true, false, true]
      - Add 1: tempList = [1, 2, 1], used = [true, true, true]
      - Complete permutation [1, 2, 1] → Backtrack
  - Continue similarly for other possibilities.

### Time and Space Complexity:

**Time Complexity:**
- Sorting the array takes O(n log n).
- The backtracking function generates permutations in O(n!), and for each permutation, a copy is created, taking O(n) time.
- Overall time complexity: O(n! * n) for generating all permutations + O(n log n) for sorting.
- Final time complexity: O(n! * n).

**Space Complexity:**
- The `used[]` array takes O(n) space.
- The recursive call stack can go as deep as O(n).
- Space complexity: O(n) for tempList, used[], and the recursion stack.
- Total space complexity: O(n), excluding the output list.

### Code:

```java
import java.util.*;

public class Solution {
    // Main function to find all unique permutations
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();
        Arrays.sort(nums);  // Sort the array to ensure duplicates are adjacent
        backtrack(list, new ArrayList<>(), nums, new boolean[nums.length]);  // Call the backtracking function
        return list;
    }

    // Helper function to perform backtracking
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, boolean[] used) {
        // If the current list size is equal to the length of nums, we have a complete permutation
        if (tempList.size() == nums.length) {
            list.add(new ArrayList<>(tempList));  // Add a copy of the current permutation to the result list
        } else {
            // Iterate through the elements of nums
            for (int i = 0; i < nums.length; i++) {
                // Skip this element if it has already been used or it's a duplicate and the previous one wasn't used
                if (used[i] || (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])) continue;

                // Mark the current element as used
                used[i] = true;

                // Add the current element to the temporary list
                tempList.add(nums[i]);

                // Recur with the next element
                backtrack(list, tempList, nums, used);

                // Backtrack: unmark the element and remove it from the tempList
                used[i] = false;
                tempList.remove(tempList.size() - 1);
            }
        }
    }

    // Main method to test the function
    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] nums = {1, 1, 2};  // Example test case

        // Call the permuteUnique function and get the result
        List<List<Integer>> result = solution.permuteUnique(nums);

        // Print the result
        System.out.println("Unique permutations:");
        for (List<Integer> permutation : result) {
            System.out.println(permutation);
        }
    }
}
```
