# Question 10: Non-Decreasing Subsequences

**Question:** https://leetcode.com/problems/non-decreasing-subsequences/description/

## Explanation for Interview:

**Purpose:**
- The goal of this code is to find all unique increasing subsequences of length 2 or more from an integer array.
- The use of a set (HashSet) ensures that we store only unique subsequences.

**Main logic:**
- The function `findSubsequences` initializes the set and calls the helper recursive function `recursion`.
- The `recursion` method generates subsequences by iterating through the array starting at the current index (curr) and building a temporary list (temp).
- Backtracking is used to explore all possible subsequences. After exploring a path (a subsequence), it removes the last element and explores other possibilities.

**Key Recursion Concept:**
- The recursion starts from index 0, iterating through the array.
- For each element, we either add it to the subsequence (if it's greater than or equal to the last added element) or skip it.
- After each addition, the recursive function is called to explore further subsequences.
- After returning from recursion, the last added element is removed (backtracked) to explore new subsequences without that element.

**Backtracking:**
- After trying all elements following the current one, we backtrack by removing the last element from the temporary list (temp) and try the next possibility.

### Time and Space Complexity:

**Time Complexity:** O(2^n), where n is the length of the input array nums. The reason is that for each element, we have two choices: either include it in the subsequence or skip it. Hence, for n elements, the number of subsequences we can generate is in the order of 2^n.
Additionally, we store and convert the subsequences in a HashSet, which takes extra time but avoids duplicates.

**Space Complexity:** O(2^n * n), since we store all valid subsequences, where each subsequence can be of length n in the worst case. The recursion stack depth will also be O(n), as we can have up to n recursive calls at any given point.

### Dry Run:

Input: nums = [4, 6, 7, 7]

**Initial Call:**
findSubsequences([4, 6, 7, 7])
Starts recursion from index 0 with an empty list temp.

**First Recursion Call:**
- i = 0, temp = []
- Add 4 to temp: temp = [4]
- Recurse with i = 1

**Second Recursion Call:**
- i = 1, temp = [4]
- Add 6: temp = [4, 6] (valid subsequence of size ≥ 2)
- Add to set: {[4, 6]}
- Recurse with i = 2

**Third Recursion Call:**
- i = 2, temp = [4, 6]
- Add 7: temp = [4, 6, 7] (valid subsequence)
- Add to set: {[4, 6], [4, 6, 7]}
- Recurse with i = 3

**Fourth Recursion Call:**
- i = 3, temp = [4, 6, 7]
- Add 7: temp = [4, 6, 7, 7] (valid subsequence)
- Add to set: {[4, 6], [4, 6, 7], [4, 6, 7, 7]}
- Backtrack to temp = [4, 6, 7]
- Return and explore next possibilities.

**Backtracking:**
- Backtrack step by step: temp = [4, 6], temp = [4]
- Explore remaining subsequences by adding other elements and continuing recursion.
- Continue: Similar process continues for i = 1 (starting with 6), i = 2 (starting with 7), and so on.

**Final Set:**
{[4, 6], [4, 6, 7], [4, 6, 7, 7], [4, 7], [4, 7, 7], [6, 7], [6, 7, 7], [7, 7]}

**Output:**
[[4, 6], [4, 6, 7], [4, 6, 7, 7], [4, 7], [4, 7, 7], [6, 7], [6, 7, 7], [7, 7]]

### Code:

```java
import java.util.*;

class Solution {
    // A set to store unique subsequences
    Set<List<Integer>> lists;

    // Main function to find all increasing subsequences
    public List<List<Integer>> findSubsequences(int[] nums) {
        lists = new HashSet<>();  // Initialize the set to avoid duplicate subsequences
        recursion(nums, 0, new ArrayList<>());  // Start the recursive process from the 0th index
        return new ArrayList<>(lists);  // Convert the set to a list and return
    }

    // Recursive helper function to find subsequences
    void recursion(int[] nums, int curr, List<Integer> temp) {
        // Base condition: If the current list size is >= 2, add to the set
        if (temp.size() >= 2)
            lists.add(new ArrayList<>(temp));  // Add a copy of temp to avoid mutation

        // Loop through the array starting from the current index
        for (int i = curr; i < nums.length; i++) {
            // Check if the sequence is increasing or if temp is empty
            if (temp.size() == 0 || temp.get(temp.size() - 1) <= nums[i]) {
                temp.add(nums[i]);  // Add the current element to the temp list
                recursion(nums, i + 1, temp);  // Recurse with the next index
                temp.remove(temp.size() - 1);  // Backtrack by removing the last element
            }
        }
    }

    public static void main(String[] args) {
        // Creating an instance of Solution class
        Solution solution = new Solution();

        // Test case: input array
        int[] nums = {4, 6, 7, 7};

        // Call the findSubsequences method
        List<List<Integer>> subsequences = solution.findSubsequences(nums);

        // Output the results
        System.out.println("Increasing Subsequences:");
        for (List<Integer> subsequence : subsequences) {
            System.out.println(subsequence);  // Print each subsequence
        }
    }
}
```
