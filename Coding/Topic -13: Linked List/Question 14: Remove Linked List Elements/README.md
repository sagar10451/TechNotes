# Question 14: Remove Linked List Elements

**Question:** 203. Remove Linked List Elements

Given the head of a linked list and an integer val, remove all the nodes of the linked list that has Node.val == val, and return the new head.

## Optimal Solution

**Approach:**
- Use a dummy node to simplify edge cases (like removing the head node).
- Iterate through the linked list and adjust the next pointers of the nodes to skip over any nodes that have the value val.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

### Code:

```java
import java.util.*;

public class RemoveLinkedListElements_Optimal {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(0); // Dummy node
        dummy.next = head; // Connect dummy to head
        ListNode current = dummy; // Pointer to traverse the list

        while (current.next != null) {
            if (current.next.val == val) {
                current.next = current.next.next; // Skip the node
            } else {
                current = current.next; // Move to the next node
            }
        }

        return dummy.next; // Return the new head
    }

    public static void main(String[] args) {
        RemoveLinkedListElements_Optimal solution = new RemoveLinkedListElements_Optimal();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(6);
        head.next.next.next = new ListNode(3);
        head.next.next.next.next = new ListNode(4);
        head.next.next.next.next.next = new ListNode(5);
        head.next.next.next.next.next.next = new ListNode(6);

        int val = 6;
        ListNode newHead = solution.removeElements(head, val);

        // Print the modified list
        while (newHead != null) {
            System.out.print(newHead.val + " "); // Expected Output: 1 2 3 4 5
            newHead = newHead.next;
        }
    }
}
```
