# Product of Array Except Self

**Problem:** Given an integer array `nums`, return an array `output` where `output[i]` is the product of all elements of `nums` except `nums[i]`. Solve it without using division and in O(n) time.

**Example:**
- Input: `[1, 2, 3, 4]`
- Output: `[24, 12, 8, 6]`

---

## Solution 1: Brute Force

**Approach:** For each index, loop through the entire array and multiply all elements except the current one.

- **Time:** O(n²)
- **Space:** O(n)

```java
import java.util.*;

public class ProductOfArrayExceptSelf_BruteForce {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] output = new int[n];

        for (int i = 0; i < n; i++) {
            output[i] = 1;
            for (int j = 0; j < n; j++) {
                if (i != j) {
                    output[i] *= nums[j];
                }
            }
        }
        return output;
    }

    public static void main(String[] args) {
        ProductOfArrayExceptSelf_BruteForce solution = new ProductOfArrayExceptSelf_BruteForce();
        int[] nums = {1, 2, 3, 4};
        System.out.println(Arrays.toString(solution.productExceptSelf(nums)));
        // Output: [24, 12, 8, 6]
    }
}
```

---

## Solution 2: Prefix & Suffix Arrays

**Approach:** Build a left-product array and a right-product array, then multiply them together.

- **Time:** O(n)
- **Space:** O(n) (two extra arrays)

```java
import java.util.*;

public class ProductOfArrayExceptSelf_PrefixSuffix {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] output = new int[n];
        int[] left = new int[n];
        int[] right = new int[n];

        left[0] = 1;
        for (int i = 1; i < n; i++) {
            left[i] = left[i - 1] * nums[i - 1];
        }

        right[n - 1] = 1;
        for (int i = n - 2; i >= 0; i--) {
            right[i] = right[i + 1] * nums[i + 1];
        }

        for (int i = 0; i < n; i++) {
            output[i] = left[i] * right[i];
        }
        return output;
    }

    public static void main(String[] args) {
        ProductOfArrayExceptSelf_PrefixSuffix solution = new ProductOfArrayExceptSelf_PrefixSuffix();
        int[] nums = {1, 2, 3, 4};
        System.out.println(Arrays.toString(solution.productExceptSelf(nums)));
        // Output: [24, 12, 8, 6]
    }
}
```

---

## Solution 3: Optimal (Single Output Array + Running Variable)

**Approach:** Store left products directly in the output array, then traverse from right using a running variable to multiply in the right products. No extra arrays needed.

- **Time:** O(n)
- **Space:** O(1) (excluding output)

```java
import java.util.*;

public class ProductOfArrayExceptSelf_Optimal {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] output = new int[n];

        // Left products into output
        output[0] = 1;
        for (int i = 1; i < n; i++) {
            output[i] = output[i - 1] * nums[i - 1];
        }

        // Multiply right products using a running variable
        int right = 1;
        for (int i = n - 1; i >= 0; i--) {
            output[i] *= right;
            right *= nums[i];
        }

        return output;
    }

    public static void main(String[] args) {
        ProductOfArrayExceptSelf_Optimal solution = new ProductOfArrayExceptSelf_Optimal();
        int[] nums = {1, 2, 3, 4};
        System.out.println(Arrays.toString(solution.productExceptSelf(nums)));
        // Output: [24, 12, 8, 6]
    }
}
```

---

## Summary

| Solution | Time | Space | Notes |
|----------|------|-------|-------|
| Brute Force | O(n²) | O(n) | Nested loops, simple but slow |
| Prefix & Suffix Arrays | O(n) | O(n) | Two extra arrays |
| Optimal (Running Variable) | O(n) | O(1) | Best — no extra arrays |
