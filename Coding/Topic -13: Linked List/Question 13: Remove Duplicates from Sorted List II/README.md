# Question 13: Remove Duplicates from Sorted List II

**Question:** 82. Remove Duplicates from Sorted List II

Given the head of a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

## 1. Brute Force Solution — start with this, then explain optimal solution

**Approach:**
- Traverse the linked list and use a hashmap to count occurrences of each value.
- Create a new linked list and add only those nodes that have a count of 1.

**Time Complexity:** O(n)
**Space Complexity:** O(n), due to the additional storage for the hashmap.

## 2. Optimal Solution

**Brief Explanation of Steps:**
1. **Dummy Node Creation:** A dummy node is created and linked to the head of the list. This simplifies the logic for handling edge cases.
2. **Initialization:** Pointers prev and curr are initialized. prev points to the dummy node, and curr points to the head.
3. **Traverse the List:** The while loop continues until curr reaches the end:
   - **Detect Duplicates:** If curr and curr.next have the same value, enter a nested loop to skip all nodes with that value.
   - **Linking:** After skipping the duplicates, prev.next is updated to link to the next unique node.
   - **Update Pointers:** If no duplicates are found, prev is updated to curr, and curr moves to the next node.
4. **Return Modified List:** Return dummy.next.

**Key Points for Interviews:**
- Use of Dummy Node: Simplifies edge case handling.
- Two Pointers: prev helps maintain the last unique node, while curr traverses the list.
- Time Complexity: O(n)
- Space Complexity: O(1)

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class RemoveDuplicatesFromSortedListII_BruteForce {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return head;

        Map<Integer, Integer> countMap = new HashMap<>();
        ListNode current = head;

        // Count occurrences of each value
        while (current != null) {
            countMap.put(current.val, countMap.getOrDefault(current.val, 0) + 1);
            current = current.next;
        }

        // Create a new list for unique values
        ListNode dummy = new ListNode(0);
        ListNode newList = dummy;
        current = head;

        while (current != null) {
            if (countMap.get(current.val) == 1) {
                newList.next = current;
                newList = newList.next;
            }
            current = current.next;
        }

        newList.next = null; // Terminate the new list
        return dummy.next;
    }

    public static void main(String[] args) {
        RemoveDuplicatesFromSortedListII_BruteForce solution = new RemoveDuplicatesFromSortedListII_BruteForce();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(3);
        head.next.next.next.next = new ListNode(4);
        head.next.next.next.next.next = new ListNode(4);

        ListNode result = solution.deleteDuplicates(head);
        while (result != null) {
            System.out.print(result.val + " "); // Expected Output: 1 2
            result = result.next;
        }
    }
}
```

### 2. Optimal Solution (CODE)

```java
class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int x) { val = x; }
}

public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        // Create a dummy node to simplify handling the head of the list.
        ListNode dummy = new ListNode();
        dummy.next = head; // Set the dummy's next to the head of the list.

        ListNode prev = dummy; // Initialize prev to point to the dummy node.
        ListNode curr = head; // Initialize curr to the head of the list.

        // Traverse through the linked list.
        while (curr != null) {
            // Check if there is a next node and if the current node's value equals the next node's value.
            if (curr.next != null && curr.val == curr.next.val) {
                // Move curr to the last duplicate node.
                while (curr.next != null && curr.val == curr.next.val) {
                    curr = curr.next; // Skip all nodes with the same value.
                }
                // Link prev to the node after the last duplicate.
                prev.next = curr.next;
            } else {
                // No duplicates detected, move prev to curr.
                prev = curr;
            }
            // Move curr to the next node in the list.
            curr = curr.next;
        }

        // Return the modified list, starting from the node after the dummy.
        return dummy.next;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: 1 -> 2 -> 3 -> 3 -> 4 -> 4 -> 5
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(3);
        head.next.next.next.next = new ListNode(4);
        head.next.next.next.next.next = new ListNode(4);
        head.next.next.next.next.next.next = new ListNode(5);

        ListNode result = solution.deleteDuplicates(head);
        while (result != null) {
            System.out.print(result.val + " "); // Expected: 1 2 5
            result = result.next;
        }
    }
}
```
