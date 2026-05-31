# Question 20: Zigzag Conversion

**Question:** 6. Zigzag Conversion

## Approach:

**Steps:**
1. Edge cases: if numRows >= string length or numRows == 1, return original string.
2. Special case for numRows == 2: alternate even/odd indexed characters.
3. General case: Fill first row (step = 2*n), middle rows (alternating vertical/diagonal), last row (step = 2*n).

**Time Complexity:** O(n)
**Space Complexity:** O(n)

### Dry Run:

Input: "PAYPALISHIRING", numRows = 3
```
P   A   H   N      → row 0: positions 0,4,8,12
A P L S I I G      → row 1: positions 1,3,5,7,9,11,13
Y   I   R          → row 2: positions 2,6,10
```
Output: "PAHNAPLSIIGYIR"

### Code:

```java
class Solution {
    public String convert(String s, int numRows) {
        char[] original = s.toCharArray();
        char[] res = new char[original.length];
        int n = numRows - 1;
        int i = 0, j = 0, k = 0;

        if(numRows >= original.length || numRows == 1) return s;

        if(numRows == 2){
            for(i = 0; i < original.length; i += 2){
                res[k] = original[i];
                ++k;
            }
            for(i = 1; i < original.length; i += 2){
                res[k] = original[i];
                ++k;
            }
            return new String(res);
        }

        // Fill first row
        while(j < original.length){
            res[k] = original[j];
            ++k;
            j += 2 * n;
        }

        // Fill middle rows
        for(i = 1; i < n; ++i){
            for(j = 0; true; ++j){
                boolean lastChanged = false;
                if(j % 2 == 0){
                    if(j * n + i < original.length){
                        res[k] = original[j * n + i];
                        ++k;
                        lastChanged = true;
                    } else if (!lastChanged) break;
                } else {
                    if((j + 1) * n < original.length + i){
                        res[k] = original[(j + 1) * n - i];
                        ++k;
                        lastChanged = true;
                    } else if (!lastChanged) break;
                }
            }
        }

        // Fill last row
        for(i = 0; k < original.length; i += 2){
            res[k] = original[(i + 1) * n];
            ++k;
        }

        return new String(res);
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        System.out.println(solution.convert("PAYPALISHIRING", 3)); // "PAHNAPLSIIGYIR"
        System.out.println(solution.convert("AB", 1)); // "AB"
    }
}
```
