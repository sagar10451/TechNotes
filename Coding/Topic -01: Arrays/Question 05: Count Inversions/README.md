# Count Inversions

## Problem

Given an array, count the number of **inversions**. An inversion is a pair `(i, j)` where `i < j` but `arr[i] > arr[j]` — basically, how many pairs are "out of order."

A sorted array has **0** inversions. A reverse-sorted array has the **maximum**.

**Example:**
`arr = [2, 4, 1, 3, 5]` → `3` inversions: `(2,1)`, `(4,1)`, `(4,3)`

---

## 1. Brute Force

**Approach:** Two nested loops — for every pair `(i, j)` where `i < j`, check if `arr[i] > arr[j]`.

| Time | Space |
|------|-------|
| O(n²) | O(1) |

```java
class InversionCount {
    public static int countInversions(int[] arr) {
        int n = arr.length;
        int inversionCount = 0;

        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                if (arr[i] > arr[j]) {
                    inversionCount++;
                }
            }
        }
        return inversionCount;
    }

    public static void main(String[] args) {
        System.out.println(countInversions(new int[]{2, 4, 1, 3, 5})); // 3
        System.out.println(countInversions(new int[]{1, 2, 3, 4, 5})); // 0
        System.out.println(countInversions(new int[]{5, 4, 3, 2, 1})); // 10
    }
}
```

### Dry Run (`arr = [2, 4, 1, 3, 5]`)

| i | j | arr[i] > arr[j]? | Count |
|---|---|-------------------|-------|
| 0 | 2 | 2 > 1 ✅ | 1 |
| 1 | 2 | 4 > 1 ✅ | 2 |
| 1 | 3 | 4 > 3 ✅ | 3 |

Result: `3` ✅

---

## 2. ✅ Optimal — Merge Sort

**Key Idea:** During the merge step of merge sort, if `arr[i] > arr[j]` (left element > right element), then **all remaining elements** in the left half are also greater than `arr[j]` (since left half is sorted). So inversions = `mid - i + 1`.

| Time | Space |
|------|-------|
| **O(n log n)** | **O(n)** |

**How it works:**
1. **Divide:** Split array into two halves recursively.
2. **Conquer:** Count inversions in left half + right half.
3. **Merge:** While merging, if `arr[i] > arr[j]`, add `mid - i + 1` to inversion count (all remaining left elements form inversions with `arr[j]`).

```java
class InversionCount {
    public static int mergeSortAndCount(int[] arr, int[] temp, int left, int right) {
        int inversionCount = 0;
        if (left < right) {
            int mid = (left + right) / 2;

            inversionCount += mergeSortAndCount(arr, temp, left, mid);
            inversionCount += mergeSortAndCount(arr, temp, mid + 1, right);
            inversionCount += mergeAndCount(arr, temp, left, mid, right);
        }
        return inversionCount;
    }

    public static int mergeAndCount(int[] arr, int[] temp, int left, int mid, int right) {
        int i = left, j = mid + 1, k = left;
        int inversionCount = 0;

        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                // All remaining in left half (i to mid) are > arr[j]
                temp[k++] = arr[j++];
                inversionCount += (mid - i + 1);
            }
        }

        while (i <= mid) temp[k++] = arr[i++];
        while (j <= right) temp[k++] = arr[j++];

        for (i = left; i <= right; i++) arr[i] = temp[i];

        return inversionCount;
    }

    public static int countInversions(int[] arr) {
        int[] temp = new int[arr.length];
        return mergeSortAndCount(arr, temp, 0, arr.length - 1);
    }

    public static void main(String[] args) {
        System.out.println(countInversions(new int[]{2, 4, 1, 3, 5})); // 3
        System.out.println(countInversions(new int[]{1, 2, 3, 4, 5})); // 0
        System.out.println(countInversions(new int[]{5, 4, 3, 2, 1})); // 10
    }
}
```

### Dry Run (`arr = [2, 4, 1, 3, 5]`)

```
[2, 4, 1, 3, 5]
       ↓
[2, 4, 1]    [3, 5]         ← split
   ↓            ↓
[2] [4, 1]   [3] [5]       ← split
      ↓
   [4] [1]                  ← 1 inversion (4 > 1)
   [1, 4]                   ← merged

[2] [1, 4]                  ← 1 inversion (2 > 1)
[1, 2, 4]                   ← merged

[3] [5]                     ← 0 inversions
[3, 5]                      ← merged

[1, 2, 4] [3, 5]            ← 1 inversion (4 > 3)
[1, 2, 3, 4, 5]             ← merged

Total: 1 + 1 + 0 + 1 = 3 ✅
```

---

## Test Cases

| Input | Output | Why |
|-------|--------|-----|
| `[2, 4, 1, 3, 5]` | 3 | Three out-of-order pairs |
| `[1, 2, 3, 4, 5]` | 0 | Already sorted |
| `[5, 4, 3, 2, 1]` | 10 | Reverse sorted = n(n-1)/2 = max inversions |

---

## Quick Revision Summary

| # | Approach | Time | Space | Key Idea |
|---|----------|------|-------|----------|
| 1 | Brute Force | O(n²) | O(1) | Check every pair |
| 2 | **Merge Sort** | **O(n log n)** | **O(n)** | **During merge: if left > right, inversions += mid - i + 1** |

> 🔑 **Key takeaway:** Piggyback on merge sort — the merge step naturally reveals inversions. When a right element is smaller than a left element, all remaining left elements are also inversions with it.
