
# Question 15: Remove Nth Node From End of List

**Question:** 19. Remove Nth Node From End of List

## Approach-1: Two Pass

### Explanation of the Steps:

**First Pass (Calculating Length):**
- We initialize a pointer temp to traverse the list and calculate the total length.

**Edge Case:**
- If the node to be removed is the head (i.e., n == length), we simply return head.next.

**Second Pass (Removing the Node):**
- We traverse the list a second time but only up to the node just before the one we want to remove.
- We then adjust the next pointer to skip the target node.

**Time Complexity:** O(n) — traverse the list twice.
**Space Complexity:** O(1)

### Dry Run:

Input: 1 -> 2 -> 3 -> 4 -> 5, n = 2

- First Pass: length = 5
- Position from start: 5 - 2 = 3
- Second Pass: traverse to node at position 3 (value 3)
- Adjust: 3.next = 3.next.next (skip 4)
- Result: 1 -> 2 -> 3 -> 5

### Code (with comments and main method):

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // First, initialize a temporary pointer to the head of the list
        ListNode temp = head;
        int length = 0;

        // First pass: Calculate the total length of the linked list
        while (temp != null) {
            length++;
            temp = temp.next;
        }

        // Calculate the position of the node to remove from the start
        int positionFromStart = length - n;

        // Edge case: If the node to remove is the head
        if (positionFromStart == 0) {
            return head.next;
        }

        // Initialize another pointer to the head
        ListNode current = head;

        // Second pass: Traverse to the node just before the one we need to remove
        for (int i = 1; i < positionFromStart; i++) {
            current = current.next;
        }

        // Remove the nth node by skipping it
        current.next = current.next.next;

        return head;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode result = solution.removeNthFromEnd(head, 2);
        printList(result);
    }

    public static void printList(ListNode head) {
        while (head != null) {
            System.out.print(head.val + " ");
            head = head.next;
        }
        System.out.println();
    }
}
```

## Approach-2: Single Pass (using slow and fast pointers)

### Explanation:

**Using a Dummy Node:** Simplifies edge cases like removing the head node.

**Two Pointers:**
- Both pointers initially point to the dummy node.
- The first pointer is moved n + 1 steps ahead to maintain a gap of n nodes.

**Moving Both Pointers:** Both move simultaneously until first reaches the end. At this point, second is just before the node to remove.

**Removing the Node:** second.next = second.next.next

**Time Complexity:** O(n) — single pass.
**Space Complexity:** O(1)

### Dry Run:

Input: 1 -> 2 -> 3 -> 4 -> 5, n = 2

- Move first 3 steps (n+1) ahead: first = 3
- Move both together until first = null: second ends at 3
- Remove: 3.next = 5 (skipping 4)
- Result: 1 -> 2 -> 3 -> 5

### Code (with comments and main method):

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // Create a dummy node, which simplifies edge cases like removing the head node
        ListNode dummy = new ListNode(0);
        dummy.next = head;

        // Initialize two pointers, both starting from the dummy node
        ListNode first = dummy;
        ListNode second = dummy;

        // Move the first pointer n + 1 steps ahead
        for (int i = 0; i <= n; i++) {
            first = first.next;
        }

        // Move both pointers together until the first pointer reaches the end
        while (first != null) {
            first = first.next;
            second = second.next;
        }

        // At this point, the second pointer is at the node just before the one to be removed
        second.next = second.next.next; // Remove the nth node

        // Return the modified list starting from the original head (dummy.next)
        return dummy.next;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        ListNode result = solution.removeNthFromEnd(head, 2);
        printList(result);
    }

    public static void printList(ListNode head) {
        while (head != null) {
            System.out.print(head.val + " ");
            head = head.next;
        }
        System.out.println();
    }
}
```
