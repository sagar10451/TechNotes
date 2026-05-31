# Question 09: Odd Even Linked List

**Question:** 328. Odd Even Linked List

## 1. Brute Force Solution

**Approach:**
- Traverse the linked list and maintain two separate lists: one for odd indexed nodes and another for even indexed nodes.
- After separating the nodes, connect the last node of the odd list to the head of the even list.
- Return the head of the modified linked list.

**Time Complexity:** O(N), where N is the number of nodes.
**Space Complexity:** O(1), since we are not using any additional data structures apart from pointers.

## 2. Optimal Solution (In-Place Rearrangement) — for Interview start from here

**Approach:**
- NOTE: Index start from 1 not 0
- 1st is odd, so starting point of odd linked list will be head of LL
- 2nd is even so starting point of even linked list will be head.next
- Now we follow simple formula: odd ka next = even ka next. Then we will do odd = odd.next. Even ka next = odd ka next.
- Starting me ek evenHead pointer bhi maintain karenge, so that end me we will do odd ka next == evenHead, then both odd and even list will be connected.

**Time Complexity:** O(N)
**Space Complexity:** O(1), as this solution modifies the list in place.

---

### 1. Brute Force Solution (CODE)

```java
import java.util.*;

public class OddEvenLinkedList_BruteForce {
    static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) { val = x; }
    }

    public ListNode oddEvenList(ListNode head) {
        if (head == null) return head;

        ListNode odd = head;
        ListNode even = head.next;
        ListNode evenHead = even; // to connect at the end

        while (even != null && even.next != null) {
            odd.next = even.next; // connect odd nodes
            odd = odd.next; // move odd pointer
            even.next = odd.next; // connect even nodes
            even = even.next; // move even pointer
        }

        odd.next = evenHead; // connect odd list to even list
        return head;
    }

    public static void main(String[] args) {
        OddEvenLinkedList_BruteForce solution = new OddEvenLinkedList_BruteForce();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode result = solution.oddEvenList(head);
        while (result != null) {
            System.out.print(result.val + " "); // Output: 1 3 5 2 4
            result = result.next;
        }
    }
}
```

### 2. Optimal Solution (In-Place Rearrangement) (CODE)

```java
import java.util.*;

class ListNode {
    int val;
    ListNode next;

    ListNode(int val) {
        this.val = val;
        this.next = null;
    }
}

public class Solution {
    // Method to rearrange the list such that all odd-indexed nodes come first, followed by even-indexed nodes
    public ListNode oddEvenList(ListNode head) {
        if (head == null) return head;

        ListNode odd = head;              // Pointer to the odd-indexed nodes
        ListNode even = head.next;        // Pointer to the even-indexed nodes
        ListNode evenHead = even;         // Store the head of the even-indexed list

        // Traverse the list, connecting odd and even nodes separately
        while (even != null && even.next != null) {
            odd.next = even.next;         // Link the next odd node
            odd = odd.next;               // Move to the next odd node
            even.next = odd.next;         // Link the next even node
            even = even.next;             // Move to the next even node
        }

        // Link the end of the odd list to the head of the even list
        odd.next = evenHead;

        return head;
    }

    public static void main(String[] args) {
        ListNode node1 = new ListNode(1);
        ListNode node2 = new ListNode(2);
        ListNode node3 = new ListNode(3);
        ListNode node4 = new ListNode(4);
        ListNode node5 = new ListNode(5);

        node1.next = node2;
        node2.next = node3;
        node3.next = node4;
        node4.next = node5;

        Solution solution = new Solution();
        ListNode result = solution.oddEvenList(node1);

        System.out.print("Reordered List: ");
        ListNode current = result;
        while (current != null) {
            System.out.print(current.val + " ");
            current = current.next;
        }
        System.out.println();
    }
}
```

