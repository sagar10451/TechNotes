# Question 21: Sort List

**Question:** 148. Sort List

## Approach-1: Merge Sort

### Steps and Explanation:

1. **Base Case:** If the list is empty or has one element, return it as-is.
2. **Cutting the List:** Split into two halves using slow/fast pointer technique.
3. **Recursive Sorting:** Sort each half recursively.
4. **Merging:** Merge the two sorted halves.

**Time Complexity:** O(n log n) — log n levels of recursion, O(n) merge at each level.
**Space Complexity:** O(log n) — due to recursive call stack.

### DRY Run:

Input: 4 -> 2 -> 1 -> 3

- Split into 4->2 and 1->3
- Sort first half: split into 4 and 2, merge → 2->4
- Sort second half: split into 1 and 3, merge → 1->3
- Final merge: merge(2->4, 1->3) → 1->2->3->4

### Code:

```java
public class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        // Step 1: Cut the list into two halves
        ListNode prev = null, slow = head, fast = head;
        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        prev.next = null; // Cut the list into two halves

        // Step 2: Sort each half recursively
        ListNode l1 = sortList(head);
        ListNode l2 = sortList(slow);

        // Step 3: Merge the two sorted halves
        return merge(l1, l2);
    }

    ListNode merge(ListNode l1, ListNode l2) {
        ListNode l = new ListNode(0); // Dummy node
        ListNode p = l;

        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                p.next = l1;
                l1 = l1.next;
            } else {
                p.next = l2;
                l2 = l2.next;
            }
            p = p.next;
        }

        if (l1 != null) p.next = l1;
        if (l2 != null) p.next = l2;

        return l.next;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();

        ListNode head = new ListNode(4);
        head.next = new ListNode(2);
        head.next.next = new ListNode(1);
        head.next.next.next = new ListNode(3);

        ListNode sortedList = solution.sortList(head);

        while (sortedList != null) {
            System.out.print(sortedList.val + " ");
            sortedList = sortedList.next;
        } // Expected output: 1 2 3 4
    }
}

class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}
```

## Approach-2: Constant Space (Bottom-Up Merge Sort)

Only explain approach, say I have never coded bottom up merge sort (iterative merge sort).

**Bottom-up merge sort** is an iterative version that starts with small sorted sublists and progressively merges them into larger sorted sublists.

**How it works:**
1. **Initial Step:** Each element is a sorted sublist of size 1.
2. **Merging Process:** Iteratively merge pairs of sorted sublists. First pass: merge size-1 pairs → size-2 sublists. Second pass: merge size-2 pairs → size-4 sublists.
3. **Repeat:** Continue until the entire list is merged.

**Example:** [38, 27, 43, 3, 9, 82, 10]
- Pass 1: [27,38], [3,43], [9,82], [10]
- Pass 2: [3,27,38,43], [9,10,82]
- Pass 3: [3,9,10,27,38,43,82]

**Time Complexity:** O(n log n)
**Space Complexity:** O(1) auxiliary space (when using linked lists)

**Advantages:** Iterative (no recursion overhead), stable sort, especially useful for linked lists.
