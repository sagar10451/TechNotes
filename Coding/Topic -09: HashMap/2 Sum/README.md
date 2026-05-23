# Question 01: Two Sum

**Question:** 1. Two Sum

Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target. You may assume that each input would have exactly one solution, and you may not use the same element twice. You can return the answer in any order.

## 1. Brute Force Solution

**Approach:**
- Iterate over every possible pair of elements and check if their sum equals the target.
- If a pair is found, return their indices.

**Time Complexity:**
- O(n^2): The brute force solution checks every possible pair of elements, resulting in quadratic time complexity.

**Space Complexity:**
- O(1): We only use a fixed amount of extra space (ignoring the space used by the input array).

## 2. Optimal Solution (Using HashMap)

- Initialize a HashMap to store numbers and their indices.
- Iterate through the array:
  - Calculate the complement (target - current number).
  - If the complement exists in the map, store the two indices in the result array and return it.
  - Otherwise, add the current number and its index to the map.
- Return the result once the pair is found.

**Time Complexity:** O(n)
**Space Complexity:** O(n)

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

class Solution {
    public static int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];  // Declare the result array to store the indices

        // Loop through each element
        for (int i = 0; i < nums.length; i++) {
            // For each element, loop through the rest to find a pair
            for (int j = i + 1; j < nums.length; j++) {
                // Check if the current pair adds up to the target
                if (nums[i] + nums[j] == target) {
                    // Store the indices in the result array
                    result[0] = i;
                    result[1] = j;
                    return result;  // Return the result array
                }
            }
        }

        // If no solution is found, return the result array (although per problem statement, one solution always exists)
        return result;
    }

    public static void main(String[] args) {
        int[] nums = {2, 7, 11, 15};
        int target = 9;
        int[] result = twoSum(nums, target);
        System.out.println("Indices: " + Arrays.toString(result));  // Output: [0, 1]
    }
}
```

### 2. Optimal Solution (Using HashMap) (CODE)

```java
import java.util.*;

class Solution {
    public static int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> m = new HashMap<>();
        int[] result = new int[2];  // Create result array

        for (int i = 0; i < nums.length; i++) {
            int difference = target - nums[i];

            if (m.containsKey(difference)) {
                result[0] = m.get(difference);  // Store index of complement
                result[1] = i;  // Store current index
                return result;  // Return result array
            }
            m.put(nums[i], i);  // Store the value and its index in the map
        }

        return result;  // Return result (though guaranteed one solution exists in the problem)
    }

    public static void main(String[] args) {
        int[] nums = {2, 7, 11, 15};
        int target = 9;
        int[] result = twoSum(nums, target);
        System.out.println("Indices: " + Arrays.toString(result));  // Output: [0, 1]
    }
}
```

**If asked to return element then:**
```java
result[0] = difference; // Store the complement element
result[1] = nums[i];
```

