# Question 02: Insert Delete GetRandom O(1)

**Question:** 380. Insert Delete GetRandom O(1)

## Logic Explanation:

**Data Structure Overview:**
- This class uses a combination of an ArrayList and a HashMap to store a set of unique integers.
- The ArrayList allows for efficient access by index, while the HashMap stores the value and its corresponding index in the list.

**Insert Operation:**
- If the value is not already present in the set, it adds the value to the end of the ArrayList and stores its index in the HashMap.
- This ensures that insertion takes constant time, O(1).

**Remove Operation:**
- If the value is present, it retrieves its index from the HashMap.
- To remove an element efficiently, it swaps the value to be removed with the last value in the ArrayList. Then, it removes the last element and updates the index of the swapped value in the HashMap.
- This ensures that the removal operation is done in constant time, O(1).

**GetRandom Operation:**
- The getRandom() method returns a random value from the set by generating a random index and returning the corresponding value from the ArrayList.
- The random access in the ArrayList takes constant time, O(1).

### Time Complexity:
- **Insert:** O(1) average time complexity because inserting into both the ArrayList and HashMap takes constant time.
- **Remove:** O(1) because swapping an element with the last one and updating the HashMap happens in constant time.
- **GetRandom:** O(1) because accessing a random index in the ArrayList is constant time.

### Space Complexity:
- O(N), where N is the number of elements in the set, because we store each element in both the ArrayList and HashMap.

### Code:

```java
import java.util.*;

class RandomizedSet {
    // List to store the values in the set
    List<Integer> nums;
    // HashMap to store the value and its index in the list
    Map<Integer, Integer> idxMap;
    // Random object for generating random numbers
    Random random;

    // Constructor: Initialize the ArrayList, HashMap, and Random object
    public RandomizedSet() {
        nums = new ArrayList<>();
        idxMap = new HashMap<>();
        random = new Random();
    }

    // Insert a value into the set
    public boolean insert(int val) {
        // If the value is already in the set, return false
        if (idxMap.containsKey(val)) {
            return false;
        }
        // Otherwise, insert the value into the set
        // Store the current size of the list as the index of the new element
        idxMap.put(val, nums.size());
        // Add the value to the end of the list
        nums.add(val);
        return true;
    }

    // Remove a value from the set
    public boolean remove(int val) {
        // If the value does not exist in the set, return false
        if (!idxMap.containsKey(val)) {
            return false;
        }

        // Get the index of the value to be removed
        int idx = idxMap.get(val);
        // Get the last index of the list
        int lastIdx = nums.size() - 1;

        // If the value is not at the last index, swap it with the last value in the list
        if (idx != lastIdx) {
            int lastVal = nums.get(lastIdx);
            // Move the last value to the index of the value being removed
            nums.set(idx, lastVal);
            // Update the index of the last value in the HashMap
            idxMap.put(lastVal, idx);
        }

        // Remove the last element from the list
        nums.remove(lastIdx);
        // Remove the value from the HashMap
        idxMap.remove(val);
        return true;
    }

    // Get a random value from the set
    public int getRandom() {
        // Generate a random index within the bounds of the list and return the corresponding value
        return nums.get(random.nextInt(nums.size()));
    }

    public static void main(String[] args) {
        // Initialize the RandomizedSet
        RandomizedSet randomizedSet = new RandomizedSet();

        // Test case operations
        System.out.println("insert(1): " + randomizedSet.insert(1)); // Expected: true
        System.out.println("remove(2): " + randomizedSet.remove(2)); // Expected: false
        System.out.println("insert(2): " + randomizedSet.insert(2)); // Expected: true
        System.out.println("getRandom(): " + randomizedSet.getRandom()); // Expected: 1 or 2
        System.out.println("remove(1): " + randomizedSet.remove(1)); // Expected: true
        System.out.println("insert(2): " + randomizedSet.insert(2)); // Expected: false
        System.out.println("getRandom(): " + randomizedSet.getRandom()); // Expected: 2
    }
}
```
