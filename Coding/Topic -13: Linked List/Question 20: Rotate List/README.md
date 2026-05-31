# Question 20: Rotate List

**Question:** https://leetcode.com/problems/rotate-list/

## 1. Brute Force Solution

**Approach:**
- Find the length of the linked list.
- Calculate effective rotations (k % length).
- Connect tail to head (make circular), then find the new tail and break the circle.

**Time Complexity:** O(n)
**Space Complexity:** O(1)

## 2. Optimal Solution — Start from here for interviews

**Step-by-Step Process:**
1. **Calculate the Length:** Loop through the list to find its total length.
2. **Adjust k:** Use k = k % length to handle cases where k is larger than the list length.
3. **Identify Split Point:** The list is split at the (length - k)th node. The nodes after this split point become the new head (fpart), and the nodes before (lpart) will be appended after fpart.
4. **Reconnection:** The last node of fpart is connected back to lpart.
5. **Return the New Head:** Return fpart.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class RotateList_BruteForce {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null || k == 0) return head;

        // Calculate the length
        int length = 1;
        ListNode current = head;
        while (current.next != null) {
            current = current.next;
            length++;
        }

        // Connect the tail to the head
        current.next = head;

        k = k % length; // Effective rotations

        for (int i = 0; i < length - k; i++) {
            current = current.next;
        }

        // Set new head and break the loop
        head = current.next;
        current.next = null;

        return head;
    }

    public static void main(String[] args) {
        RotateList_BruteForce solution = new RotateList_BruteForce();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode result = solution.rotateRight(head, 2);

        while (result != null) {
            System.out.print(result.val + " ");
            result = result.next;
        }
        // Expected Output: 4 5 1 2 3
    }
}
```

### 2. Optimal Solution (CODE)

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; this.next = null; }
}

public class RotateRight {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null || k == 0) return head;

        // Step 1: Calculate the total length
        int length = 0;
        ListNode node = head;
        while (node != null) {
            node = node.next;
            length++;
        }

        // Step 2: Update k to avoid unnecessary rotations
        k = k % length;
        if (k == 0) return head;

        // Step 3: Split the list at (length - k) position
        ListNode lpart = head;
        for (int i = 1; i < length - k; i++) {
            head = head.next;
        }

        // fpart starts from the node after (length - k)
        ListNode fpart = head.next;
        head.next = null; // Terminate the first part

        // Step 4: If fpart is null, no rotation needed
        if (fpart == null) return lpart;

        // Traverse to the last node of fpart
        node = fpart;
        while (node.next != null) {
            node = node.next;
        }

        // Reconnect the last node of fpart to lpart
        node.next = lpart;

        return fpart;
    }

    public static void main(String[] args) {
        RotateRight solution = new RotateRight();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode result = solution.rotateRight(head, 2);

        ListNode current = result;
        while (current != null) {
            System.out.print(current.val + " ");  // Expected output: 4 5 1 2 3
            current = current.next;
        }
    }
}
```
