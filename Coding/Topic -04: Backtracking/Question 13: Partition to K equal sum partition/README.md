# Question 13: Partition to K Equal Sum Subsets

**Question:** https://leetcode.com/problems/partition-to-k-equal-sum-subsets/

## Process Explanation (for Interview):

**Objective:** We are given an array `nums` and an integer `k`. We need to determine whether we can partition the array into k subsets such that each subset has an equal sum.

**Key Steps:**

1. **Base Cases:**
   - If k == 1, we can trivially partition the array into one subset (the whole array itself).
   - Calculate the total sum of the array. If this sum is not divisible by k, we cannot partition the array into k subsets with equal sums, so we return false.
   - If k > nums.length (more subsets than elements), return false because it's impossible to partition.

2. **Sorting for Efficiency:**
   - We sort the nums array in descending order to prioritize placing larger elements first. This helps reduce the number of recursive calls and makes it easier to reach target sums for subsets.

3. **Recursive Backtracking:**
   - We use a recursive approach to try placing each number in one of the subsets.
   - For each number, we attempt to add it to each subset and recursively move to the next number.
   - If placing the current number in a subset exceeds the target sum, we skip that subset.
   - To avoid redundant work, we skip subsets that already have the same sum (to prevent symmetrical solutions).

4. **Backtracking:**
   - If placing a number doesn't lead to a solution, we backtrack by removing it from the current subset and trying a different arrangement.

5. **Return Result:**
   - If we successfully assign all numbers into subsets, we return true. If not, we return false.

### Time and Space Complexity:

**Time Complexity:**
- The time complexity is exponential: O(k * 2^n), where n is the length of the nums array and k is the number of subsets. This is because for each element, we are recursively trying to place it in one of the k subsets, leading to a combinatorial explosion of possibilities.

**Space Complexity:**
- The space complexity is O(k + n). We use an array subsets of size k to track the current sum of each subset, and the recursive call stack can go as deep as n.

### Dry Run of the Code:

Input: nums = [4, 3, 2, 3, 5, 2, 1], k = 4

- Total Sum: 4 + 3 + 2 + 3 + 5 + 2 + 1 = 20
- Target Sum for Each Subset: 20 / 4 = 5

**Step-by-Step Execution:**

1. Initial Setup: We start with an empty subsets array: [0, 0, 0, 0].
   We first sort nums to get [5, 4, 3, 3, 2, 2, 1].

2. Recursion:
   - Try placing 5 in the first subset: [5, 0, 0, 0].
   - Try placing 4 in the second subset: [5, 4, 0, 0].
   - Try placing 3 in the third subset: [5, 4, 3, 0].
   - Try placing 3 in the fourth subset: [5, 4, 3, 3].
   - Try placing 2 in the second subset (it fits with 4): [5, 4+2, 3, 3] -> [5, 6, 3, 3]. This exceeds the target, so backtrack.
   - Try placing 2 in the third subset: [5, 4, 3+2, 3] -> [5, 4, 5, 3].
   - Try placing 2 in the fourth subset: [5, 4, 5, 3+2] -> [5, 4, 5, 5].
   - Place the last 1 in the second subset: [5, 4+1, 5, 5] -> [5, 5, 5, 5].

**Result:** All subsets have the target sum of 5, so the answer is **true**.

### Code:

```java
import java.util.Arrays;

class Solution {
    // Method to check if we can partition the array into k subsets with equal sum
    public boolean canPartitionKSubsets(int[] nums, int k) {
        // Special case: If k is 1, no partitioning is needed, so return true
        if (k == 1) {
            return true;
        }

        // Calculate the total sum of all elements
        int totalSum = 0;
        for (int i = 0; i < nums.length; i++) {
            totalSum += nums[i];
        }

        // If total sum is not divisible by k or k is greater than the number of elements, return false
        if (totalSum % k != 0 || k > nums.length) {
            return false;
        }

        // Create an array to store the sum of each subset
        int[] subsets = new int[k];

        // Sort the nums array in descending order to improve backtracking efficiency
        Arrays.sort(nums);
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            left++;
            right--;
        }

        // Try to partition the array into k subsets with equal sum
        return canPartitionKSubsets(nums, 0, totalSum / k, subsets);
    }

    // Helper method to recursively try partitioning into subsets
    public boolean canPartitionKSubsets(int[] nums, int si, int target, int[] subsets) {
        // Base case: If we've assigned all elements, return true
        if (si == nums.length) {
            return true;
        }

        // Try placing the current number in one of the subsets
        for (int i = 0; i < subsets.length; i++) {
            // Check if adding current number exceeds target sum for this subset
            if (subsets[i] + nums[si] > target || (i > 0 && subsets[i] == subsets[i - 1])) {
                continue;  // Skip this subset and try next
            }

            // Place current number in this subset
            subsets[i] += nums[si];

            // Recursively check if we can assign the rest of the elements
            if (canPartitionKSubsets(nums, si + 1, target, subsets)) {
                return true;
            }

            // Backtrack: Remove the current number from this subset
            subsets[i] -= nums[si];
        }

        // If no valid partition found, return false
        return false;
    }

    // Main method to run the test case
    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case
        int[] nums = {4, 3, 2, 3, 5, 2, 1};
        int k = 4;

        boolean result = solution.canPartitionKSubsets(nums, k);
        System.out.println("Can partition: " + result); // Expected output: true
    }
}
```
