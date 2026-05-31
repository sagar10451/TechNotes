
# Question 06: Middle of the Linked List

**Question:** 876. Middle of the Linked List

Given the head of a singly linked list, return the middle node of the linked list. If there are two middle nodes, return the second middle node.

**There is difference between this two pointers approach and Floyd Cycle detection algorithm:**
- **Two Pointers:** Method: The fast pointer moves twice as fast as the slow pointer. By the time the fast pointer reaches the end, the slow pointer is at the middle of the list.
- **Floyd Cycle Detection:** Method: Similarly uses slow and fast pointers, but in this case, the fast pointer and slow pointer will eventually meet if a cycle exists. Once they meet, a second pass determines the start of the cycle.

## 1. Brute Force Solution (Count and Traverse)

**Approach:**
- Traverse the linked list to determine its length.
- Calculate the middle index based on the length.
- Traverse the list again to reach the middle node and return it.

**Time Complexity:** O(n): Two passes through the linked list.
**Space Complexity:** O(1): No additional space is used.

## 2. Optimal Solution (Two Pointer Technique)

**Approach:**
- (basically this is the property of slow and fast pointer)
- Use two pointers, slow and fast.
- Move slow by one step and fast by two steps.
- When fast reaches the end of the list, slow will be at the middle node.

**Time Complexity:** O(n): Single pass through the list.
**Space Complexity:** O(1): No additional space is used.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class MiddleOfLinkedList_BruteForce {
    public static ListNode middleNode(ListNode head) {
        int length = 0;
        ListNode current = head;

        // First pass to count the length
        while (current != null) {
            length++;
            current = current.next;
        }

        // Calculate the middle index
        int middleIndex = length / 2;

        // Second pass to reach the middle node
        current = head;
        for (int i = 0; i < middleIndex; i++) {
            current = current.next;
        }

        return current; // Return the middle node
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode middle = middleNode(head);
        System.out.println(middle.val); // Output: 3
    }
}

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}
```

### 2. Optimal Solution (Two Pointer Technique) (CODE)

```java
import java.util.*;

public class MiddleOfLinkedList_Optimal {
    public static ListNode middleNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;

        // Move fast pointer twice as fast as slow pointer
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        return slow; // Return the middle node
    }

    public static void main(String[] args) {
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode middle = middleNode(head);
        System.out.println(middle.val); // Output: 3
    }
}

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}
```

