# Question 19: Reverse Nodes in K-Groups

**Question:** 25. Reverse Nodes in k-Group

## Approach-1: Iterative (use this in Interviews)

**Approach in Brief:**
1. **Count Nodes:** Traverse the list to count nodes and determine how many full k-sized groups we can reverse.
2. **Reverse Each k-Node Group:** For each k nodes, reverse the group in place by adjusting links.
3. **Link Groups Together:** After reversing each group, connect it to the previous part.
4. **Dummy Node:** Use a dummy node to simplify handling of edge cases.

### Code:

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || k == 1) return head;

        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode cur = dummy, nex = dummy, pre = dummy;
        int count = 0;

        // Count the total number of nodes
        while (cur.next != null) {
            cur = cur.next;
            count++;
        }

        // Reverse each k-node group
        while (count >= k) {
            cur = pre.next;
            nex = cur.next;
            for (int i = 1; i < k; i++) {
                cur.next = nex.next;
                nex.next = pre.next;
                pre.next = nex;
                nex = cur.next;
            }
            pre = cur;
            count -= k;
        }

        return dummy.next;
    }

    public static void printList(ListNode head) {
        while (head != null) {
            System.out.print(head.val + " -> ");
            head = head.next;
        }
        System.out.println("null");
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        System.out.println("Original List:");
        printList(head);

        int k = 2;
        ListNode result = solution.reverseKGroup(head, k);
        System.out.println("Reversed in k-groups (k = " + k + "):");
        printList(result);
    }
}
```

## Approach-2: Recursive

**Approach:**
1. **Count Nodes:** Count nodes to see if there are at least k nodes available.
2. **Recursive Reversal:** If we have k nodes, recursively reverse the remaining part starting from (k+1)-th node. Then reverse the first k nodes in place.
3. **Return New Head:** After reversing, cur becomes the new head.

**Time Complexity:** O(n)
**Space Complexity:** O(n/k) due to recursion stack.

### Dry Run:

Input: 1 -> 2 -> 3 -> 4 -> 5, k = 2

- First Call: Count 2 nodes (1,2). Recurse on 3->4->5.
- Second Call: Count 2 nodes (3,4). Recurse on 5.
- Third Call: Count 1 node (5) < k, return 5 as-is.
- Reverse second group: 4 -> 3 -> 5
- Reverse first group: 2 -> 1 -> 4 -> 3 -> 5

### Code:

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode cur = head;
        int count = 0;

        // Count nodes to see if we have enough for reversal
        while (cur != null && count < k) {
            cur = cur.next;
            count++;
        }

        // If we have k nodes, proceed with reversing
        if (count == k) {
            cur = reverseKGroup(cur, k); // Recursively reverse remaining

            // Reverse the first k nodes in place
            while (count > 0) {
                ListNode temp = head.next;
                head.next = cur;
                cur = head;
                head = temp;
                count--;
            }
            head = cur; // cur becomes the new head of this group
        }

        return head;
    }

    public static void printList(ListNode head) {
        while (head != null) {
            System.out.print(head.val + " -> ");
            head = head.next;
        }
        System.out.println("null");
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        System.out.println("Original List:");
        printList(head);

        int k = 2;
        ListNode result = solution.reverseKGroup(head, k);
        System.out.println("Reversed in k-groups (k = " + k + "):");
        printList(result);
    }
}
```
