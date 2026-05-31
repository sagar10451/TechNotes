# Question 17: Reverse Linked List

**Question:** 206. Reverse Linked List

## Approach-1: Iterative (using temp node)

**Steps:**
1. Initialize: Set prev to null and curr to head.
2. Loop through the list:
   - Store the next node (next = curr.next).
   - Reverse the current node's link (curr.next = prev).
   - Move prev and curr one step forward (prev = curr, curr = next).
3. Return the new head (which is prev).

### Code:

```java
import java.util.*;

public class ReverseLinkedList_Optimal {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;

        while (curr != null) {
            ListNode next = curr.next; // Store next node
            curr.next = prev;          // Reverse the link
            prev = curr;               // Move prev and curr one step forward
            curr = next;
        }
        return prev; // New head of the reversed list
    }

    public static void main(String[] args) {
        ReverseLinkedList_Optimal solution = new ReverseLinkedList_Optimal();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);

        ListNode reversedHead = solution.reverseList(head);

        while (reversedHead != null) {
            System.out.print(reversedHead.val + " ");
            reversedHead = reversedHead.next;
        }
        // Expected Output: 3 2 1
    }
}
```

## Approach-2: Recursion (code simple, but difficult to understand)

**Explanation:**
- **Base Case:** If head is null or head.next is null, return head.
- **Recursive Call:** reverseList(head.next) reverses the rest of the list.
- **Reversing Pointers:** head.next.next = head reverses the link. head.next = null breaks the current link.
- **Return:** Return newHead, the head of the reversed list.

### Code:

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        // Base case: if head is null or only one node, return head
        if (head == null || head.next == null) {
            return head;
        }

        // Recurse on the next node
        ListNode newHead = reverseList(head.next);

        // Reverse the current node's next pointer
        head.next.next = head;
        head.next = null;

        return newHead;
    }
}
```
