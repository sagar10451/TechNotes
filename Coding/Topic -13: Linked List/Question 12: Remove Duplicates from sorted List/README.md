# Question 12: Remove Duplicates from Sorted List

**Question:** 83. Remove Duplicates from Sorted List

Given the head of a sorted linked list, delete all duplicates such that each element appears only once. Return the linked list sorted as well.

## Approach:

**Steps:**
1. Take 2 pointers, prev (jo head ko point karega) and curr (jo head ke next ko point karega), aur while loop challenge: while(curr!=null)
2. Agar (prev ka value == curr ka value) Then: prev.next = curr.next — isse duplicate value skip ho jayega.
3. Now we will update curr: curr = curr.next
4. Agar (prev ka value != curr ka value) Toh bas dono pointer ko ek baar aage badha denge: prev = curr; curr = curr.next

**Time Complexity:** O(n)
**Space Complexity:** O(1)

### Code:

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return head;

        ListNode prev = head;
        ListNode curr = head.next;

        while (curr != null) {
            if (prev.val == curr.val) {
                prev.next = curr.next;
                curr = curr.next;
            } else {
                prev = curr;
                curr = curr.next;
            }
        }
        return head;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        // Test case: 1 -> 1 -> 2 -> 3 -> 3
        ListNode head = new ListNode(1);
        head.next = new ListNode(1);
        head.next.next = new ListNode(2);
        head.next.next.next = new ListNode(3);
        head.next.next.next.next = new ListNode(3);

        ListNode result = solution.deleteDuplicates(head);

        while (result != null) {
            System.out.print(result.val + " "); // Expected: 1 2 3
            result = result.next;
        }
    }
}
```

