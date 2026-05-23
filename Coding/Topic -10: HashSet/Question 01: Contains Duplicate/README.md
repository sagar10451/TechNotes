# Question 01: Contains Duplicate

**Question:** 217. Contains Duplicate

Given an integer array nums, return true if any value appears at least twice in the array, and return false if every element is distinct.

## 1. Brute Force Solution

**Approach:**
- Use a nested loop to compare each element with every other element in the array.
- If any duplicate is found, return true; otherwise, return false.

**Time Complexity:**
- O(n^2): The brute force solution checks every possible pair of elements, resulting in quadratic time complexity.

**Space Complexity:**
- O(1): This solution uses a constant amount of extra space.

## 2. Other Solution (Using sorting)

1. Sort the array
2. Then traverse the array and compare adjacent elements.
3. If same element found return true. Else false.

- TC: O(n log n)
- SC: O(1)

## 3. Optimal Solution (Using HashSet)

**Approach:**
- Hashset allows us to insert element in O(1) time.
- Traverse through array, and check if current element exists in hashset, if no insert it.
- If yes that is our answer.

**Time Complexity:**
- O(n): The optimal solution has linear time complexity as it processes each element once.

**Space Complexity:**
- O(n): The space complexity is linear because we may need to store up to n elements in the HashSet in the worst case.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class ContainsDuplicateBruteForce {
    public static boolean containsDuplicate(int[] nums) {
        // Check every possible pair of elements
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                // If a duplicate is found
                if (nums[i] == nums[j]) {
                    return true;
                }
            }
        }
        return false; // No duplicates found
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3, 1}; // Example input
        System.out.println(containsDuplicate(nums));  // Output: true
    }
}
```

### 3. Optimal Solution (Using HashSet) (CODE)

```java
import java.util.*;

public class Solution {
    public static boolean containsDuplicate(int[] nums) {
        Set<Integer> s = new HashSet<>(); // Set to track seen numbers

        // Use normal for loop
        for (int i = 0; i < nums.length; i++) {
            int num = nums[i]; // Get the current number

            // If the number is already in the set, we have a duplicate
            if (s.contains(num)) {
                return true;
            }
            s.add(num); // Add the number to the set
        }
        return false; // No duplicates found
    }

    public static void main(String[] args) {
        int[] nums = { 1, 2, 3, 1 }; // Example input
        System.out.println(containsDuplicate(nums)); // Output: true
    }
}
```
