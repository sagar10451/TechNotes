# Question 07: Sort Characters by Frequency

**Question:** 451. Sort Characters By Frequency

Given a string s, sort it in decreasing order based on the frequency of the characters. The frequency of a character is the number of times it appears in the string. Return the sorted string. If there are multiple answers, return any of them.

## Optimal Solution:

**Approach:**
1. First iterate through array and store elements in hashmap with their frequencies.
2. Then insert all map elements to Max heap.
3. Then we can pop element one by one and generate our result string.

**Time Complexity:** O(n) + k log k, where k is the number of unique characters (at most 26 for lowercase English letters). Time to insert map element O(n), time to insert k characters in max heap: k(log k), k element, ek element ko insert karne me logk time.

**Space Complexity:** O(n) for the frequency map.

### Code:

```java
import java.util.*;

class Solution {
    public String frequencySort(String s) {
        Map<Character, Integer> m = new HashMap<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i); // Get the current character
            if (m.containsKey(c)) {
                m.put(c, m.get(c) + 1); // Increment the frequency if it already exists
            } else {
                m.put(c, 1); // Initialize the frequency if it's the first occurrence
            }
        }

        // Use a priority queue to sort characters by frequency
        PriorityQueue<Map.Entry<Character, Integer>> maxHeap = new PriorityQueue<>(
            (a, b) -> b.getValue() - a.getValue()
        );
        maxHeap.addAll(m.entrySet()); //adding all elements from map to PQ

        // Build the result string
        StringBuilder result = new StringBuilder();
        while (!maxHeap.isEmpty()) {
            // Get the current entry (character and its frequency)
            Map.Entry<Character, Integer> entry = maxHeap.poll();

            // Get the character and its frequency
            char currentChar = entry.getKey();
            int frequency = entry.getValue();

            // Append the character 'frequency' times
            for (int i = 0; i < frequency; i++) {
                result.append(currentChar);
            }
        }

        return result.toString();
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        String input = "tree";
        String result = s.frequencySort(input);
        System.out.println(result); // Output: "eert" or "tree"
    }
}
```
