# Question 18: Reverse Linked List II

**Question:** 92. Reverse Linked List II

## Approach (this is complex problem, BE VERY CAREFUL WHILE EXPLAINING)

**Steps:**
1. Base condition. If head is null or left == right, there's nothing to reverse.
2. Create a dummy node and link it to the head. Dummy node helps handle cases where we reverse the portion starting from head.
3. Move `prev` to the node just before the `left` position.
4. `cur` points to the `left` node. It will be used to reverse the sublist.
5. Reverse the sublist between `left` and `right` using a for-loop.
6. Return the new head of the list, which is pointed by dummy.next.

FOR More clarity in explaining check code comments.

### Code:

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        // Step 1: Base condition
        if (head == null || left == right) {
            return head;
        }

        // Step 2: Create a dummy node and link it to the head
        ListNode dummy = new ListNode(0);
        dummy.next = head;

        // Step 3: Move `prev` to the node just before the `left` position
        ListNode prev = dummy;
        for (int i = 0; i < left - 1; i++) {
            prev = prev.next;
        }

        // Step 4: `cur` points to the `left` node
        ListNode cur = prev.next;

        // Step 5: Reverse the sublist between `left` and `right`
        for (int i = 0; i < right - left; i++) {
            // `temp` points to the node that comes after `cur`
            ListNode temp = cur.next;
            // Adjust `cur`'s next to skip `temp`
            cur.next = temp.next;
            // Insert `temp` between `prev` and `prev.next`
            temp.next = prev.next;
            prev.next = temp;
        }

        // Step 6: Return the new head
        return dummy.next;
    }
}
```

