# Question 18: Subsets

**Question:** https://leetcode.com/problems/subsets/description/

## Explanation for Interview:

The problem is to generate all possible subsets (power set) of an integer array nums.

This is solved using backtracking. We start with an empty list and explore all possible subsets by either including or excluding each element.

**Step-by-step approach:**
1. We initialize a list `list` to store all subsets.
2. We start backtracking from the first index (start = 0).
3. In each recursive call, we add the current `tempList` (which represents one subset) to `list`.
4. We loop through the array, and for each element, we add it to `tempList`, recursively call the function to explore further elements, and then backtrack (i.e., remove the last element).
5. This ensures we explore all possible combinations of elements, generating all subsets.

### Dry Run:

Input: nums = [1, 2, 3]

- Start with an empty list: []. Add it to the result: list = [[]].
- Add 1 to the tempList: [1]. Add it to the result: list = [[], [1]].
  - Add 2: [1, 2]. Add it to the result: list = [[], [1], [1, 2]].
    - Add 3: [1, 2, 3]. Add it to the result: list = [[], [1], [1, 2], [1, 2, 3]].
  - Backtrack to [1, 2], remove 2, add 3: [1, 3]. Add it to the result: list = [[], [1], [1, 2], [1, 2, 3], [1, 3]].
- Backtrack to [1], remove 1, add 2: [2]. Add it to the result: list = [[], [1], [1, 2], [1, 2, 3], [1, 3], [2]].
  - Add 3: [2, 3]. Add it to the result: list = [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3]].
- Backtrack to [2], remove 2, add 3: [3]. Add it to the result: list = [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]].
- Backtrack to []. The process is complete.

**Final Subsets:** [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]

### Time Complexity:

- **Time Complexity:** O(2^n * n) — The total number of subsets of an array of size n is 2^n. For each subset, we may need to copy it into our result list, which takes O(n) time in the worst case.
- **Space Complexity:** O(n) — The maximum depth of the recursion tree is n, and at each level, we store a temporary list of size n.

### Code:

```java
import java.util.*;

class Solution {
    // Main function to find all subsets of the given array
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();  // List to store all subsets
        Arrays.sort(nums);  // Sort the array to ensure non-decreasing order of elements in each subset
        backtrack(list, new ArrayList<>(), nums, 0);  // Start the backtracking process
        return list;  // Return the final list of subsets
    }

    // Backtracking helper function to generate subsets
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int start) {
        list.add(new ArrayList<>(tempList));  // Add the current subset (tempList) to the result list

        // Iterate through the array starting from the 'start' index
        for (int i = start; i < nums.length; i++) {
            tempList.add(nums[i]);  // Add current element to the temporary subset
            backtrack(list, tempList, nums, i + 1);  // Recur to add further elements to the subset
            tempList.remove(tempList.size() - 1);  // Backtrack by removing the last element added
        }
    }

    // Main method to test the subsets function with a sample test case
    public static void main(String[] args) {
        Solution solution = new Solution();  // Create an instance of the Solution class
        int[] nums = {1, 2, 3};  // Test case: Input array

        // Call the subsets method and store the result
        List<List<Integer>> result = solution.subsets(nums);

        // Print the result
        System.out.println("Subsets: ");
        for (List<Integer> subset : result) {
            System.out.println(subset);
        }
    }
}
```
