# Question 02: Longest Consecutive Sequence

**Question:** 128. Longest Consecutive Sequence

Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence. You must write an algorithm that runs in O(n) time.

## 1. Brute Force Solution (Sorting-based)

**Approach:**
- Sort the array and then iterate through the sorted array to find the longest consecutive sequence.
- If two consecutive elements differ by more than 1, reset the count. Track the maximum length of consecutive sequences.

**Steps:**
1. Sort the array.
2. Iterate through the array and track consecutive elements.
3. Update the longest consecutive sequence length accordingly.

**Time Complexity:**
- O(n log n) due to sorting.

**Space Complexity:**
- O(1) if sorting is done in place, otherwise O(n).

## 2. Optimal Solution (HashSet-based)

**Approach:**
- Use a HashSet to store all numbers in the array. This allows for O(1) lookups.
- Iterate through the array, and for each number, check if it is the start of a sequence (i.e., its previous number `num - 1` is not in the set).
- If it's the start of a sequence, iterate through the sequence to find its length, and keep track of the longest sequence found.

**Steps:**
1. Add all elements to a HashSet.
2. For each element, check if it's the start of a sequence by checking if `num - 1` exists in the set.
3. If it's the start, count the length of the consecutive sequence starting from that number.
4. Track the maximum length of consecutive sequences.

**Time Complexity:**
- O(n), since we perform constant-time lookups and operations using the HashSet.

**Space Complexity:**
- O(n), due to the storage of elements in the HashSet.

---

### 1. Brute Force Solution (Sorting-based) (CODE)

```java
import java.util.*;

public class LongestConsecutiveSequence_BruteForce {
    public static int longestConsecutive(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }

        Arrays.sort(nums);

        int longestStreak = 1;
        int currentStreak = 1;

        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i - 1]) {
                if (nums[i] == nums[i - 1] + 1) {
                    currentStreak++;
                } else {
                    longestStreak = Math.max(longestStreak, currentStreak);
                    currentStreak = 1;
                }
            }
        }

        return Math.max(longestStreak, currentStreak);
    }

    public static void main(String[] args) {
        int[] nums = {100, 4, 200, 1, 3, 2};
        System.out.println(longestConsecutive(nums));  // Output: 4
    }
}
```

### 2. Optimal Solution (HashSet-based) (CODE)

```java
import java.util.*;

class Solution {
    public static int longestConsecutive(int[] nums) {
        // Create a set to hold the unique elements of the input array
        Set<Integer> numSet = new HashSet<>();

        // Add all the elements from nums array to the set
        for (int i = 0; i < nums.length; i++) {
            numSet.add(nums[i]);
        }

        int longest = 0;

        // Iterate through the nums array to find the longest consecutive sequence
        for (int i = 0; i < nums.length; i++) {
            int num = nums[i];

            // Check if it's the start of a sequence (if num - 1 is not present in the set)
            if (!numSet.contains(num - 1)) {
                int length = 1;

                // Continue checking for consecutive elements
                while (numSet.contains(num + length)) {
                    length++;
                }

                // Update the longest sequence length
                longest = Math.max(longest, length);
            }
        }

        return longest;       
    }

    public static void main(String[] args) {
        int[] nums = {100, 4, 200, 1, 3, 2};
        System.out.println(longestConsecutive(nums));  // Output: 4
    }
}
```
