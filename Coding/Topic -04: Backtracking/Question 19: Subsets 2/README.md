# Question 19: Subsets II

**Question:** https://leetcode.com/problems/subsets-ii/description/

## Explanation for Interview:

This code generates all possible subsets of an integer array nums which may contain duplicate elements. The key here is to avoid generating duplicate subsets.

Backtracking is used to build subsets:
- Start with an empty set and explore adding each element.
- To avoid duplicates, we skip over consecutive duplicate elements if they have already been added at this level.

**Steps:**
1. Sort the input array to bring duplicate elements next to each other. This makes it easier to skip them.
2. Start backtracking from the first element (start = 0).
3. In each recursion, add the current subset (tempList) to the result list.
4. If the current element is the same as the previous one, skip it to prevent duplicate subsets.
5. Recurse and continue exploring different subsets.
6. Backtrack by removing the last added element to explore other possible subsets.

### Dry Run:

Input: nums = [1, 2, 2]

- Start with tempList = []. Add it to the result: result = [[]].
- Add 1: tempList = [1]. Add it to the result: result = [[], [1]].
  - Add 2: tempList = [1, 2]. Add it to the result: result = [[], [1], [1, 2]].
    - Add another 2: tempList = [1, 2, 2]. Add it to the result: result = [[], [1], [1, 2], [1, 2, 2]].
  - Backtrack to tempList = [1, 2], remove 2, backtrack to tempList = [1], remove 1, and now skip to next 2.
- Start fresh with tempList = [2]. Add it to the result: result = [[], [1], [1, 2], [1, 2, 2], [2]].
  - Add the last 2: tempList = [2, 2]. Add it to the result: result = [[], [1], [1, 2], [1, 2, 2], [2], [2, 2]].
- Backtrack to tempList = [2], remove 2, and continue.

**Final Subsets:** [[], [1], [1, 2], [1, 2, 2], [2], [2, 2]]

### Time and Space Complexity:

- **Time Complexity:** O(2^n * n) — There are 2^n possible subsets for an array of size n, and for each subset, we might copy the elements, which takes O(n) time. Duplicates are skipped, but it doesn't change the worst-case time complexity.
- **Space Complexity:** O(n) — The maximum depth of the recursion tree is n due to the recursion stack and temporary lists.

### Code:

```java
import java.util.*;

class Solution {
    // Main function to find all subsets including duplicates
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();  // Result list to store all subsets
        Arrays.sort(nums);  // Sort the array to handle duplicates effectively
        backtrack(list, new ArrayList<>(), nums, 0);  // Call the backtracking function
        return list;  // Return the final list of subsets
    }

    // Backtracking helper function to generate subsets
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int start) {
        list.add(new ArrayList<>(tempList));  // Add the current subset to the result list

        // Iterate through the array starting from the 'start' index
        for (int i = start; i < nums.length; i++) {
            // Skip duplicates by checking if the current number is the same as the previous one
            // and if the previous one was already processed in this recursive call.
            if (i > start && nums[i] == nums[i - 1])
                continue;  // Skip the duplicate elements

            tempList.add(nums[i]);  // Add the current number to the subset
            backtrack(list, tempList, nums, i + 1);  // Recurse to continue adding to the subset
            tempList.remove(tempList.size() - 1);  // Backtrack: remove the last added element to explore new subsets
        }
    }

    // Main method to test the function
    public static void main(String[] args) {
        Solution solution = new Solution();  // Create an instance of the Solution class
        int[] nums = {1, 2, 2};  // Test case: Input array with duplicates

        // Call the subsetsWithDup method and store the result
        List<List<Integer>> result = solution.subsetsWithDup(nums);

        // Print the result
        System.out.println("Subsets (with duplicates handled): ");
        for (List<Integer> subset : result) {
            System.out.println(subset);
        }
    }
}
```
