# Question 14: Permutations

**Question:** https://leetcode.com/problems/permutations/description/

## Explanation:

**permute(int[] nums):**
- Initializes an empty list `list` to store the permutations.
- Calls the backtracking function `backtrack` with an empty `tempList` and the input array `nums`.

**backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums):**
- **Base case:** If `tempList` contains all elements (its size equals the length of nums), it adds the current permutation (a deep copy) to the list.
- **Recursion:** It loops through the elements in nums. If an element is already present in tempList (i.e., it's part of the current permutation), it skips that element.
- Adds the current element to tempList and recurses by calling backtrack again.
- **Backtracking:** After the recursive call, the last element is removed from tempList to explore other permutations.

### Dry Run:

For nums = [1, 2, 3]:

- tempList = []
  - Add 1 → tempList = [1]
    - Add 2 → tempList = [1, 2]
      - Add 3 → tempList = [1, 2, 3] → Add to result → Backtrack → tempList = [1, 2]
    - Add 3 → tempList = [1, 3]
      - Add 2 → tempList = [1, 3, 2] → Add to result → Backtrack → tempList = [1, 3] → Backtrack → tempList = [1]
  - Add 2 → tempList = [2]
    - Add 1 → tempList = [2, 1]
      - Add 3 → tempList = [2, 1, 3] → Add to result → Backtrack → tempList = [2, 1]
    - Add 3 → tempList = [2, 3]
      - Add 1 → tempList = [2, 3, 1] → Add to result → Backtrack → tempList = [2, 3] → Backtrack → tempList = [2] → Backtrack → tempList = []
  - Continue similarly for 3...

**Result:** [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]

### Time Complexity:

The time complexity is O(N! * N):
- There are N! permutations possible for an array of size N.
- For each permutation, adding it to the result list takes O(N) time (copying the list).
- Hence, the overall time complexity is O(N! * N).

### Space Complexity:

The space complexity is O(N):
- This is the space used by the recursion stack and the temporary list tempList.
- The result list is not considered in space complexity because it's part of the output.

**Note:** The reason we use `list.add(new ArrayList<>(tempList))` (i.e., creating a new ArrayList from tempList) is to make a copy of the current permutation at that point in the recursion. By using `new ArrayList<>(tempList)`, we create a deep copy of tempList at its current state. This way, the exact permutation at that point in the recursion is stored, and future changes to tempList won't impact the previously stored permutations.

### Code:

```java
import java.util.*;

class Solution {
    // Main function to generate all permutations of the given array `nums`
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();  // This will store the result (all permutations)
        // Arrays.sort(nums); // Sorting is not necessary for generating permutations
        backtrack(list, new ArrayList<>(), nums);  // Start the backtracking process
        return list;  // Return the list of all permutations
    }

    // Backtracking helper function
    private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums) {
        // Base condition: If the temporary list has the same size as the input array
        if (tempList.size() == nums.length) {
            list.add(new ArrayList<>(tempList));  // Add the current permutation (deep copy) to the result list
        } else {
            // Explore each element in `nums` to create permutations
            for (int i = 0; i < nums.length; i++) {
                if (tempList.contains(nums[i])) continue;  // If the number is already used in the current permutation, skip it
                tempList.add(nums[i]);  // Add the current number to the temporary list
                backtrack(list, tempList, nums);  // Recursively build the permutation with the next numbers
                tempList.remove(tempList.size() - 1);  // Remove the last number to backtrack and try the next option
            }
        }
    }

    // Example main function to run the permutation method
    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] nums = {1, 2, 3};

        // Call the permute method and print the results
        List<List<Integer>> result = solution.permute(nums);
        System.out.println("Permutations: ");
        for (List<Integer> permutation : result) {
            System.out.println(permutation);
        }
    }
}
```
