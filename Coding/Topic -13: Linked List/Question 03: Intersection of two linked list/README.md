# Question 03: Intersection of Two Linked Lists

**Question:** 160. Intersection of Two Linked Lists

Given the heads of two singly linked-lists headA and headB, return the node at which the two lists intersect. If the two linked lists have no intersection, return null.

## Optimal Solution (Two Pointer Technique)

**Approach:**
- Use two pointers, one starting at headA and one at headB.
- When a pointer reaches the end of its list, redirect it to the head of the other list.
- Both pointers will traverse the same total distance (lenA + lenB), so they will meet at the intersection node or both reach null simultaneously if there's no intersection.

**Why it works:**
- Pointer A travels: listA + listB (after redirect)
- Pointer B travels: listB + listA (after redirect)
- Both travel the same total distance, so they align at the intersection point.

**Time Complexity:** O(n + m), where n and m are the lengths of the two lists.
**Space Complexity:** O(1), no extra space used.

### Code:

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // Check if either head is null; if so, there's no intersection
        if (headA == null || headB == null)
            return null;

        // Initialize two pointers, one for each list
        ListNode a = headA;
        ListNode b = headB;

        // Traverse both lists until the pointers meet (either at the intersection or at null)
        while (a != b) {
            // If pointer 'a' reaches the end of list A, reset it to the head of list B
            // This way, on the next iteration, 'a' will continue from the start of list B
            if (a == null) {
                a = headB;
            } else {
                a = a.next;  // Otherwise, move 'a' to the next node in list A
            }

            // If pointer 'b' reaches the end of list B, reset it to the head of list A
            // On the next iteration, 'b' will continue from the start of list A
            if (b == null) {
                b = headA;
            } else {
                b = b.next;  // Otherwise, move 'b' to the next node in list B
            }
        }

        // If the lists intersect, 'a' and 'b' will be at the intersection node when they are equal
        // If they don't intersect, both 'a' and 'b' will be null after the second iteration
        return a;  // Return the intersection node, or null if there is no intersection
    }
}
```
