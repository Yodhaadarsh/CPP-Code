# Detailed Explanations: Highlighted LeetCode Problems (C++)

For every problem: what it asks, the idea, the step-by-step algorithm, commented code, a line-by-line walkthrough, a dry run, complexity, and an exam tip.

**Complexity notation:** n = input size, r x c = matrix rows and columns, H and N = haystack and needle lengths.

---

# Sheet 1: Arrays

## 1. Remove Duplicates from Sorted Array (LeetCode #26)

**Problem:** Given a sorted array, remove duplicates in place so each value appears once. Return `k`, the number of unique values. The first `k` positions must hold the unique values in order.

**Idea:** In a sorted array, equal values are adjacent. So an element is a new value exactly when it differs from the element just before it. Keep a write pointer `k` that marks where the next unique value goes.

**Algorithm:**

1. Set `k = 1` (the first element is always unique).
2. For `i` from 1 to n-1: if `nums[i] != nums[i-1]`, set `nums[k] = nums[i]` and increase `k` by 1.
3. Return `k`.

**Code:**

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int k = 1;                              // nums[0] is always unique, so next write slot is 1
        for (int i = 1; i < nums.size(); i++) { // i is the read pointer
            if (nums[i] != nums[i-1])           // new value found
                nums[k++] = nums[i];            // write it at k, then move k forward
        }
        return k;                               // k = count of unique values
    }
};
```

**Line by line:**
1. `k = 1` because the first element can never be a duplicate of anything before it.
2. The loop starts at `i = 1` so that `nums[i-1]` is always valid.
3. If `nums[i]` equals the previous element it is a duplicate, so we do nothing.
4. If it differs, we copy it to the write slot `nums[k]` and advance `k`.

**Dry run** `[0,0,1,1,2]`:

| i | nums[i] | nums[i-1] | Action | k | Array state |
|---|---|---|---|---|---|
| 1 | 0 | 0 | same, skip | 1 | 0 0 1 1 2 |
| 2 | 1 | 0 | differs, nums[1]=1 | 2 | 0 1 1 1 2 |
| 3 | 1 | 1 | same, skip | 2 | 0 1 1 1 2 |
| 4 | 2 | 1 | differs, nums[2]=2 | 3 | 0 1 2 1 2 |

Returns 3. The first 3 elements are `0 1 2`.

**Complexity:** Time O(n), Space O(1).
**Exam tip:** read pointer `i`, write pointer `k`, compare with the previous element.

---

## 2. Move Zeroes (LeetCode #283)

**Problem:** Move all 0s to the end in place, keeping the order of the non-zero elements.

**Idea:** Let `j` be the position where the next non-zero value should go. Everything before `j` is already non-zero. When `i` finds a non-zero, swap it into position `j`.

**Algorithm:**

1. Set `j = 0` (position for the next non-zero value).
2. For `i` from 0 to n-1: if `nums[i] != 0`, swap `nums[i]` with `nums[j]` and increase `j` by 1.
3. Stop. All zeros are now at the end, and the non-zero order is unchanged.

**Code:**

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int j = 0;                              // next position for a non-zero value
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] != 0)
                swap(nums[i], nums[j++]);       // bring non-zero to front, zero goes behind
        }
    }
};
```

**Line by line:**
1. `j` starts at 0.
2. For each non-zero `nums[i]`, swap it with `nums[j]`, then move `j` forward.
3. If `i == j` (no zeros seen yet), the swap is with itself and does nothing harmful.
4. Zeros are never touched directly. They get pushed back by the swaps.

**Dry run** `[0,1,0,3,12]`:

| i | nums[i] | Action | j | Array |
|---|---|---|---|---|
| 0 | 0 | skip | 0 | 0 1 0 3 12 |
| 1 | 1 | swap(i=1, j=0) | 1 | 1 0 0 3 12 |
| 2 | 0 | skip | 1 | 1 0 0 3 12 |
| 3 | 3 | swap(i=3, j=1) | 2 | 1 3 0 0 12 |
| 4 | 12 | swap(i=4, j=2) | 3 | 1 3 12 0 0 |

**Complexity:** Time O(n), Space O(1).
**Exam tip:** this is the same write-pointer pattern as problem 1, but it uses a swap instead of an overwrite.

---

## 3. Majority Element (LeetCode #169)

**Problem:** Find the element that appears more than n/2 times (it is guaranteed to exist).

**Idea (Boyer-Moore voting):** Keep a candidate and a counter. Matching elements give the candidate a vote, other elements cancel one vote. Every cancellation removes one majority element and one non-majority element. The majority element appears more than all the others combined, so it survives to the end.

**Algorithm:**

1. Set `cand = 0` and `cnt = 0`.
2. For each element `x` in the array:
   1. If `cnt == 0`, set `cand = x`.
   2. If `x == cand`, increase `cnt` by 1, otherwise decrease `cnt` by 1.
3. Return `cand`.

**Code:**

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int cand = 0, cnt = 0;
        for (int x : nums) {
            if (cnt == 0) cand = x;             // no votes left, pick a new candidate
            cnt += (x == cand) ? 1 : -1;        // vote for or against the candidate
        }
        return cand;
    }
};
```

**Line by line:**
1. When `cnt` is 0, the previous candidate has been fully cancelled, so the current element becomes the candidate.
2. A match increases `cnt`, a mismatch decreases it.
3. Because a majority is guaranteed, no check pass is needed.

**Dry run** `[2,2,1,1,1,2,2]`:

| x | cnt before | cand after pick | cnt after |
|---|---|---|---|
| 2 | 0 | 2 | 1 |
| 2 | 1 | 2 | 2 |
| 1 | 2 | 2 | 1 |
| 1 | 1 | 2 | 0 |
| 1 | 0 | 1 | 1 |
| 2 | 1 | 1 | 0 |
| 2 | 0 | 2 | 1 |

Returns 2.

**Complexity:** Time O(n), Space O(1).
**Exam tip:** other approaches are a hash map (O(n) space) or sorting then taking `nums[n/2]` (O(n log n)). Boyer-Moore is the best answer.

---

## 4. Max Consecutive Ones (LeetCode #485)

**Problem:** In a binary array, find the longest run of consecutive 1s.

**Idea:** Keep the length of the current run. A 1 extends it, a 0 resets it. Track the best length seen.

**Algorithm:**

1. Set `best = 0` and `cur = 0`.
2. For each element `x`:
   1. If `x == 1`, increase `cur` by 1, otherwise set `cur = 0`.
   2. Set `best = max(best, cur)`.
3. Return `best`.

**Code:**

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int best = 0, cur = 0;
        for (int x : nums) {
            cur = (x == 1) ? cur + 1 : 0;       // extend run or reset
            best = max(best, cur);              // remember the longest run
        }
        return best;
    }
};
```

**Dry run** `[1,1,0,1,1,1]`:

| x | cur | best |
|---|---|---|
| 1 | 1 | 1 |
| 1 | 2 | 2 |
| 0 | 0 | 2 |
| 1 | 1 | 2 |
| 1 | 2 | 2 |
| 1 | 3 | 3 |

Returns 3.

**Complexity:** Time O(n), Space O(1).
**Exam tip:** update `best` inside the loop, so a run that ends at the last element is still counted.

---

## 5. Find All Numbers Disappeared in an Array (LeetCode #448)

**Problem:** The array has n numbers, each in the range 1 to n. Some appear twice, some are missing. Return the missing numbers.

**Idea:** Use the array itself as a "seen" table. Value `x` maps to index `x-1`. When we see `x`, make `nums[x-1]` negative. After the pass, any index that is still positive was never pointed to, so `index+1` is missing.

**Algorithm:**

1. For each element `x`: compute `i = abs(x) - 1` and make `nums[i]` negative (`nums[i] = -abs(nums[i])`).
2. Create an empty list `res`.
3. For `i` from 0 to n-1: if `nums[i] > 0`, add `i + 1` to `res`.
4. Return `res`.

**Code:**

```cpp
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        for (int x : nums) {
            int i = abs(x) - 1;                 // abs because nums[i] may already be negative
            nums[i] = -abs(nums[i]);            // mark as seen (stays negative if already marked)
        }
        vector<int> res;
        for (int i = 0; i < nums.size(); i++)
            if (nums[i] > 0) res.push_back(i + 1);   // never marked, so i+1 is missing
        return res;
    }
};
```

**Why `abs` twice:** the value we read (`x`) may already have been turned negative by an earlier step, so `abs(x)` recovers the real value. `-abs(nums[i])` forces the slot negative without flipping it back if it was already negative.

**Dry run** `[4,3,2,7,8,2,3,1]`:

| x read | index marked | Array after |
|---|---|---|
| 4 | 3 | 4 3 2 -7 8 2 3 1 |
| 3 | 2 | 4 3 -2 -7 8 2 3 1 |
| 2 | 1 | 4 -3 -2 -7 8 2 3 1 |
| -7 | 6 | 4 -3 -2 -7 8 2 -3 1 |
| 8 | 7 | 4 -3 -2 -7 8 2 -3 -1 |
| 2 | 1 | unchanged (already negative) |
| -3 | 2 | unchanged |
| -1 | 0 | -4 -3 -2 -7 8 2 -3 -1 |

Positive values remain at indices 4 and 5, so the answer is `[5, 6]`.

**Complexity:** Time O(n), Space O(1) extra (the output list is not counted).
**Exam tip:** this trick only works because the values are in the range 1 to n.

---

## 6. Third Maximum Number (LeetCode #414)

**Problem:** Return the third largest **distinct** number. If there are fewer than 3 distinct numbers, return the maximum.

**Idea:** A `set` removes duplicates and keeps values sorted ascending. The reverse iterator starts at the largest, so moving it two steps lands on the third largest.

**Algorithm:**

1. Insert all elements into a set (removes duplicates, sorts ascending).
2. If the set has fewer than 3 elements, return the largest element.
3. Start at the largest element, move 2 steps toward smaller values.
4. Return the element reached.

**Code:**

```cpp
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        set<int> s(nums.begin(), nums.end());   // unique + sorted
        if (s.size() < 3) return *s.rbegin();   // fewer than 3 distinct, return maximum
        auto it = s.rbegin();                   // points to the largest
        advance(it, 2);                         // move two steps toward smaller values
        return *it;                             // third largest
    }
};
```

**Dry run** `[2,2,3,1]`: set = {1, 2, 3}. Size is 3. `rbegin()` points to 3, after 2 steps it points to 1. Returns 1.
For `[1,2]`: set = {1, 2}, size 2, so return the max, 2.

**Complexity:** Time O(n log n), Space O(n).
**Exam tip:** there is also an O(n) method with three variables (`first`, `second`, `third`, initialised to `LONG_MIN`) that you can mention if asked for better time.

---

## 7. Find Numbers with Even Number of Digits (LeetCode #1295)

**Problem:** Count how many numbers have an even number of digits.

**Idea:** Convert each number to a string. Its length is the digit count.

**Algorithm:**

1. Set `cnt = 0`.
2. For each number `x`: convert it to a string and take its length.
3. If the length is even, increase `cnt` by 1.
4. Return `cnt`.

**Code:**

```cpp
class Solution {
public:
    int findNumbers(vector<int>& nums) {
        int cnt = 0;
        for (int x : nums)
            if (to_string(x).size() % 2 == 0) cnt++;   // even digit count
        return cnt;
    }
};
```

**Dry run** `[12,345,2,6,7896]`:

| x | string | length | even? |
|---|---|---|---|
| 12 | "12" | 2 | yes |
| 345 | "345" | 3 | no |
| 2 | "2" | 1 | no |
| 6 | "6" | 1 | no |
| 7896 | "7896" | 4 | yes |

Count = 2.

**Complexity:** Time O(n x d) where d is digits per number, Space O(d).
**Exam tip:** the no-string version divides by 10 in a loop and counts the divisions.

---

# Sheet 2: Matrix

## 8. Matrix Diagonal Sum (LeetCode #1572)

**Problem:** For a square matrix, return the sum of both diagonals. A cell on both diagonals is counted once.

**Idea:** In row `i`, the main diagonal cell is `[i][i]` and the anti-diagonal cell is `[i][n-1-i]`. Add both for every row. If `n` is odd, the centre cell lies on both diagonals and was added twice, so subtract it once.

**Algorithm:**

1. Let `n` be the number of rows, and set `sum = 0`.
2. For `i` from 0 to n-1: add `mat[i][i]` and `mat[i][n-1-i]` to `sum`.
3. If `n` is odd, subtract the centre element `mat[n/2][n/2]` (it was added twice).
4. Return `sum`.

**Code:**

```cpp
class Solution {
public:
    int diagonalSum(vector<vector<int>>& mat) {
        int n = mat.size(), s = 0;
        for (int i = 0; i < n; i++)
            s += mat[i][i] + mat[i][n-1-i];     // main + anti diagonal
        if (n % 2) s -= mat[n/2][n/2];          // odd n: centre counted twice
        return s;
    }
};
```

**Dry run** `[[1,2,3],[4,5,6],[7,8,9]]`:

| i | mat[i][i] | mat[i][2-i] | running sum |
|---|---|---|---|
| 0 | 1 | 3 | 4 |
| 1 | 5 | 5 | 14 |
| 2 | 9 | 7 | 30 |

n = 3 is odd, so subtract `mat[1][1] = 5`. Answer 25.

**Complexity:** Time O(n), Space O(1).
**Exam tip:** for even `n` the diagonals never share a cell, so no subtraction.

---

## 9. Transpose Matrix (LeetCode #867)

**Problem:** Return the transpose, which flips the matrix over its main diagonal so rows become columns.

**Idea:** The result of an r x c matrix is c x r. The element at `[i][j]` moves to `[j][i]`.

**Algorithm:**

1. Let `r` = rows and `c` = columns. Create a new matrix `t` with `c` rows and `r` columns.
2. For each `i` in 0..r-1 and each `j` in 0..c-1: set `t[j][i] = matrix[i][j]`.
3. Return `t`.

**Code:**

```cpp
class Solution {
public:
    vector<vector<int>> transpose(vector<vector<int>>& matrix) {
        int r = matrix.size(), c = matrix[0].size();
        vector<vector<int>> t(c, vector<int>(r));   // c rows, r columns
        for (int i = 0; i < r; i++)
            for (int j = 0; j < c; j++)
                t[j][i] = matrix[i][j];             // swap the indices
        return t;
    }
};
```

**Dry run** `[[1,2,3],[4,5,6]]` (r=2, c=3), so `t` is 3 x 2:

| i, j | value | goes to |
|---|---|---|
| 0,0 | 1 | t[0][0] |
| 0,1 | 2 | t[1][0] |
| 0,2 | 3 | t[2][0] |
| 1,0 | 4 | t[0][1] |
| 1,1 | 5 | t[1][1] |
| 1,2 | 6 | t[2][1] |

Result `[[1,4],[2,5],[3,6]]`.

**Complexity:** Time O(r x c), Space O(r x c).
**Exam tip:** an in-place transpose only works for square matrices. This problem allows rectangular, so a new matrix is required.

---

## 10. Lucky Numbers in a Matrix (LeetCode #1380)

**Problem:** A lucky number is the minimum in its row **and** the maximum in its column. Return all lucky numbers. (All numbers in the matrix are distinct.)

**Idea:** For each row, find its minimum and remember its column `mn`. Then scan that column. If any value is larger than the row minimum, it is not the column maximum, so it is not lucky.

**Algorithm:**

1. Create an empty list `res`.
2. For each row `i`:
   1. Find the column `mn` of the smallest element in the row.
   2. Scan column `mn`. If any element is greater than `matrix[i][mn]`, reject this row.
   3. If no element is greater, add `matrix[i][mn]` to `res`.
3. Return `res`.

**Code:**

```cpp
class Solution {
public:
    vector<int> luckyNumbers(vector<vector<int>>& matrix) {
        vector<int> res;
        int r = matrix.size(), c = matrix[0].size();
        for (int i = 0; i < r; i++) {
            int mn = 0;                                   // column of the row minimum
            for (int j = 1; j < c; j++)
                if (matrix[i][j] < matrix[i][mn]) mn = j;
            bool ok = true;
            for (int k = 0; k < r; k++)                   // scan that column
                if (matrix[k][mn] > matrix[i][mn]) ok = false;
            if (ok) res.push_back(matrix[i][mn]);
        }
        return res;
    }
};
```

**Dry run** `[[1,10,4,2],[9,3,8,7],[15,16,17,12]]`:

| Row | Row min (column) | Column check | Lucky? |
|---|---|---|---|
| 0 | 1 (col 0) | column 0 has 15 > 1 | no |
| 1 | 3 (col 1) | column 1 has 10 and 16 > 3 | no |
| 2 | 12 (col 3) | column 3 is 2, 7, 12, and 12 is the max | yes |

Result `[12]`.

**Complexity:** Time O(r x (c + r)), Space O(1) extra.
**Exam tip:** at most one lucky number can exist when values are distinct.

---

## 11. Toeplitz Matrix (LeetCode #766)

**Problem:** A matrix is Toeplitz if every top-left to bottom-right diagonal has the same value throughout.

**Idea:** All cells on a diagonal are equal exactly when each cell equals its top-left neighbour `[i-1][j-1]`.

**Algorithm:**

1. For `i` from 1 to r-1 and `j` from 1 to c-1: if `matrix[i][j] != matrix[i-1][j-1]`, return false.
2. If no mismatch is found, return true.

**Code:**

```cpp
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        for (int i = 1; i < matrix.size(); i++)          // start at row 1
            for (int j = 1; j < matrix[0].size(); j++)   // start at column 1
                if (matrix[i][j] != matrix[i-1][j-1]) return false;
        return true;
    }
};
```

**Why start at 1:** the first row and first column have no top-left neighbour, so they begin diagonals and need no check.

**Dry run** `[[1,2,3,4],[5,1,2,3],[9,5,1,2]]`:
- (1,1)=1 vs (0,0)=1, ok. (1,2)=2 vs (0,1)=2, ok. (1,3)=3 vs (0,2)=3, ok.
- (2,1)=5 vs (1,0)=5, ok. (2,2)=1 vs (1,1)=1, ok. (2,3)=2 vs (1,2)=2, ok.
- Returns true.

For `[[1,2],[2,2]]`: (1,1)=2 vs (0,0)=1, mismatch, returns false.

**Complexity:** Time O(r x c), Space O(1).

---

## 12. Flipping an Image (LeetCode #832)

**Problem:** For each row of a binary matrix, reverse it, then invert it (0 becomes 1, 1 becomes 0).

**Idea:** Use `reverse` for the flip. Use XOR with 1 for the invert, since `0 ^ 1 = 1` and `1 ^ 1 = 0`.

**Algorithm:**

1. For each row of the image:
   1. Reverse the row.
   2. For each element `x` in the row, set `x = x XOR 1` (0 becomes 1, 1 becomes 0).
2. Return the image.

**Code:**

```cpp
class Solution {
public:
    vector<vector<int>> flipAndInvertImage(vector<vector<int>>& image) {
        for (auto& row : image) {                  // & so we modify the real row, not a copy
            reverse(row.begin(), row.end());       // horizontal flip
            for (int& x : row) x ^= 1;             // invert each bit
        }
        return image;
    }
};
```

**Dry run** `[[1,1,0],[1,0,1],[0,0,0]]`:

| Row | After reverse | After invert |
|---|---|---|
| 1 1 0 | 0 1 1 | 1 0 0 |
| 1 0 1 | 1 0 1 | 0 1 0 |
| 0 0 0 | 0 0 0 | 1 1 1 |

**Complexity:** Time O(r x c), Space O(1).
**Exam tip:** `auto&` and `int&` matter. Without `&` you would change copies and the matrix would not change.

---

## 13. Shift 2D Grid (LeetCode #1260)

**Problem:** Shift the grid `k` times. In one shift, each element moves one cell to the right, the last element of a row goes to the first cell of the next row, and the very last element goes to `[0][0]`.

**Idea:** Read the grid row by row as one long list (row-major order). A shift by `k` moves every element `k` places forward in that list, wrapping around. The flat index of `[i][j]` is `i*n + j`. Convert back with row = `p / n` and column = `p % n`.

**Algorithm:**

1. Let `m` = rows, `n` = columns. Set `k = k mod (m*n)`.
2. Create a result grid `res` of size m x n.
3. For each cell `(i, j)`:
   1. Compute the new flat position `p = (i*n + j + k) mod (m*n)`.
   2. Set `res[p / n][p % n] = grid[i][j]`.
4. Return `res`.

**Code:**

```cpp
class Solution {
public:
    vector<vector<int>> shiftGrid(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> res(m, vector<int>(n));
        k %= (m * n);                                  // shifting m*n times returns to the start
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++) {
                int p = (i * n + j + k) % (m * n);     // new flat position, wraps around
                res[p / n][p % n] = grid[i][j];        // flat position back to row, column
            }
        return res;
    }
};
```

**Dry run** 3x3 grid `1..9`, k = 1 (m = n = 3, m*n = 9):

| Value | (i,j) | flat | p = (flat+1) % 9 | goes to |
|---|---|---|---|---|
| 1 | 0,0 | 0 | 1 | res[0][1] |
| 2 | 0,1 | 1 | 2 | res[0][2] |
| 3 | 0,2 | 2 | 3 | res[1][0] |
| ... | | | | |
| 8 | 2,1 | 7 | 8 | res[2][2] |
| 9 | 2,2 | 8 | 0 | res[0][0] |

Result `[[9,1,2],[3,4,5],[6,7,8]]`.

**Complexity:** Time O(m x n), Space O(m x n).
**Exam tip:** if `k` is a multiple of `m*n`, `k %= m*n` makes it 0 and the grid is unchanged.

---

## 14. Spiral Matrix (LeetCode #54)

**Problem:** Return all elements of the matrix in spiral order (right, down, left, up, then inward).

**Idea:** Keep four boundaries for the part not yet visited. Walk one side, then shrink that boundary. Repeat until the boundaries cross.

**Algorithm:**

1. Set `top = 0`, `bottom = r-1`, `left = 0`, `right = c-1`, and an empty list `res`.
2. While `top <= bottom` and `left <= right`:
   1. Add the top row from `left` to `right`, then `top = top + 1`.
   2. Add the right column from `top` to `bottom`, then `right = right - 1`.
   3. If `top <= bottom`: add the bottom row from `right` to `left`, then `bottom = bottom - 1`.
   4. If `left <= right`: add the left column from `bottom` to `top`, then `left = left + 1`.
3. Return `res`.

**Code:**

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int top = 0, bottom = matrix.size() - 1;
        int left = 0, right = matrix[0].size() - 1;
        while (top <= bottom && left <= right) {
            for (int j = left; j <= right; j++)        // 1) top row, left to right
                res.push_back(matrix[top][j]);
            top++;
            for (int i = top; i <= bottom; i++)        // 2) right column, top to bottom
                res.push_back(matrix[i][right]);
            right--;
            if (top <= bottom) {                       // 3) bottom row, right to left
                for (int j = right; j >= left; j--)
                    res.push_back(matrix[bottom][j]);
                bottom--;
            }
            if (left <= right) {                       // 4) left column, bottom to top
                for (int i = bottom; i >= top; i--)
                    res.push_back(matrix[i][left]);
                left++;
            }
        }
        return res;
    }
};
```

**Why the two `if` checks:** after the first two sides, the remaining area may be a single row or single column. Without the checks, the bottom row loop would repeat the row just printed (and the left column loop would repeat a column).

**Dry run** `[[1,2,3,4],[5,6,7,8],[9,10,11,12]]` (3 x 4):

| Step | Boundaries before (t,b,l,r) | Output added |
|---|---|---|
| top row | 0,2,0,3 | 1 2 3 4 |
| right column | t=1: 1,2,0,3 | 8 12 |
| bottom row | r=2: 1,2,0,2 | 11 10 9 |
| left column | b=1: 1,1,0,2 | 5 |
| second loop, top row | 1,1,1,2 | 6 7 |
| right column | top becomes 2, so top > bottom | nothing |
| bottom row | skipped by the `if` (top > bottom) | nothing |
| left column | `if` passes (left <= right) but the range is empty | nothing |

Then `left` becomes 2 and `left > right`, so the loop ends.

Result `1 2 3 4 8 12 11 10 9 5 6 7`.

**Complexity:** Time O(r x c), Space O(1) extra.
**Exam tip:** remember "right, down, left, up" and put `if` guards on the last two.

---

## 15. Set Matrix Zeroes (LeetCode #73)

**Problem:** If a cell is 0, set its whole row and column to 0, in place.

**Idea:** If we zero cells while scanning, the new zeros would wrongly trigger more rows and columns. So do two passes. First, record which rows and columns contain a zero. Second, zero every cell in a recorded row or column.

**Algorithm:**

1. Create a row marker array of size r and a column marker array of size c, all false.
2. Pass 1: for every cell with value 0, mark its row and its column as true.
3. Pass 2: for every cell, if its row or its column is marked, set the cell to 0.
4. Stop.

**Code:**

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int r = matrix.size(), c = matrix[0].size();
        vector<bool> row(r, false), col(c, false);     // markers
        for (int i = 0; i < r; i++)                    // pass 1: record
            for (int j = 0; j < c; j++)
                if (matrix[i][j] == 0) row[i] = col[j] = true;
        for (int i = 0; i < r; i++)                    // pass 2: apply
            for (int j = 0; j < c; j++)
                if (row[i] || col[j]) matrix[i][j] = 0;
    }
};
```

**Dry run** `[[0,1,2,0],[3,4,5,2],[1,3,1,5]]`:
- Pass 1: zeros at (0,0) and (0,3), so `row[0]`, `col[0]`, `col[3]` are true.
- Pass 2: row 0 becomes all 0. Columns 0 and 3 become 0 in every row.
- Result `[[0,0,0,0],[0,4,5,0],[0,3,1,0]]`.

**Complexity:** Time O(r x c), Space O(r + c).
**Exam tip:** LeetCode's follow-up asks for O(1) space. That version stores the markers in the first row and first column of the matrix itself.

---

# Sheet 3: Strings

## 16. Valid Anagram (LeetCode #242)

**Problem:** Check whether `t` is an anagram of `s` (same letters, same counts).

**Idea:** Count letters of `s` up and letters of `t` down in a 26-slot array. If they are anagrams, every slot returns to 0.

**Algorithm:**

1. If the lengths of `s` and `t` differ, return false.
2. Create `cnt[26]` filled with 0.
3. For each character `c` in `s`, increase `cnt[c - 'a']` by 1.
4. For each character `c` in `t`, decrease `cnt[c - 'a']` by 1.
5. If any `cnt` value is not 0, return false. Otherwise return true.

**Code:**

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;        // different lengths can't match
        int cnt[26] = {0};
        for (char c : s) cnt[c - 'a']++;               // 'a' maps to 0, 'b' to 1, ...
        for (char c : t) cnt[c - 'a']--;
        for (int x : cnt) if (x != 0) return false;
        return true;
    }
};
```

**Dry run** `s = "rat"`, `t = "car"`: after counting, `r:0`, `a:0`, `t:+1`, `c:-1`. Not all zero, so false.
For `"anagram"` and `"nagaram"` every letter cancels, so true.

**Complexity:** Time O(n), Space O(1) (fixed 26 slots).
**Exam tip:** `c - 'a'` converts a lowercase letter to an index 0 to 25. If Unicode is allowed, use `unordered_map<char,int>` instead.

---

## 17. Buddy Strings (LeetCode #859)

**Problem:** Can you swap exactly two characters in `s` to make it equal to `goal`?

**Idea:** There are three cases.
1. Different lengths: impossible.
2. `s == goal`: swapping must change nothing, so we need two identical letters to swap.
3. Otherwise: there must be exactly two mismatched positions, and swapping them must fix both.

**Algorithm:**

1. If the lengths differ, return false.
2. If `s == goal`: return true if some letter appears more than once, otherwise false.
3. Collect the indices where `s[i] != goal[i]` into a list `d`.
4. Return true only if `d` has exactly 2 indices and `s[d0] == goal[d1]` and `s[d1] == goal[d0]`. Otherwise return false.

**Code:**

```cpp
class Solution {
public:
    bool buddyStrings(string s, string goal) {
        if (s.size() != goal.size()) return false;
        if (s == goal) {
            set<char> st(s.begin(), s.end());
            return st.size() < s.size();               // a duplicate letter exists
        }
        vector<int> d;                                 // mismatched positions
        for (int i = 0; i < s.size(); i++)
            if (s[i] != goal[i]) d.push_back(i);
        return d.size() == 2
            && s[d[0]] == goal[d[1]]
            && s[d[1]] == goal[d[0]];                  // cross-match
    }
};
```

**Why the duplicate check:** `"ab"` and `"ab"` cannot be made equal by a swap, because swapping `a` and `b` changes the string. `"aa"` and `"aa"` can, because swapping the two `a`s changes nothing. The set is smaller than the string exactly when a letter repeats.

**Dry run:**
- `"ab"` vs `"ba"`: mismatches at 0 and 1. `s[0]='a'` equals `goal[1]='a'`, `s[1]='b'` equals `goal[0]='b'`, so true.
- `"abcaa"` vs `"abcbb"`: mismatches at 3 and 4. `s[3]='a'` vs `goal[4]='b'`, no match, so false.

**Complexity:** Time O(n), Space O(1).

---

## 18. Detect Capital (LeetCode #520)

**Problem:** A word is valid if all letters are capitals, none are capitals, or only the first letter is a capital.

**Idea:** Count uppercase letters and check the three allowed patterns.

**Algorithm:**

1. Count the uppercase letters in the word as `up`.
2. Return true if `up` equals the word length (all capitals).
3. Return true if `up == 0` (no capitals).
4. Return true if `up == 1` and the first letter is uppercase.
5. Otherwise return false.

**Code:**

```cpp
class Solution {
public:
    bool detectCapitalUse(string word) {
        int up = 0;
        for (char c : word) if (isupper(c)) up++;
        return up == word.size()                      // ALL capitals
            || up == 0                                // no capitals
            || (up == 1 && isupper(word[0]));         // only first is capital
    }
};
```

**Dry run:**

| Word | up | Rule that applies | Result |
|---|---|---|---|
| USA | 3 | up equals length (3) | true |
| leetcode | 0 | up is 0 | true |
| Google | 1 | one capital and it is first | true |
| FlaG | 2 | none | false |
| gOogle | 1 | one capital but not first | false |

**Complexity:** Time O(n), Space O(1).

---

## 19. Goat Latin (LeetCode #824)

**Problem:** Convert each word of a sentence by these rules:
1. If it starts with a vowel, append `"ma"`.
2. If it starts with a consonant, move the first letter to the end, then append `"ma"`.
3. Add one `'a'` for the word's 1-based position (1st word gets `a`, 2nd gets `aa`, and so on).

**Idea:** Split into words with `stringstream`, apply the rules to each word, and join with spaces.

**Algorithm:**

1. Split the sentence into words. Set `i = 1` and `res` to an empty string.
2. For each word:
   1. If its first letter (in lowercase) is not a vowel, move that letter to the end of the word.
   2. Append `"ma"` and then `i` copies of `'a'`.
   3. Add the word and a space to `res`, then increase `i` by 1.
3. Remove the trailing space from `res`.
4. Return `res`.

**Code:**

```cpp
class Solution {
public:
    string toGoatLatin(string sentence) {
        stringstream ss(sentence);                     // splits on spaces
        string w, res = "";
        int i = 1;                                     // word position
        while (ss >> w) {
            char f = tolower(w[0]);                    // vowels can be uppercase
            if (f != 'a' && f != 'e' && f != 'i' && f != 'o' && f != 'u')
                w = w.substr(1) + w[0];                // consonant: move first letter to end
            w += "ma" + string(i, 'a');                // "ma" + i copies of 'a'
            res += w + " ";
            i++;
        }
        res.pop_back();                                // remove the trailing space
        return res;
    }
};
```

**Dry run** `"I speak Goat Latin"`:

| i | Word | Vowel start? | After rule 1 or 2 | After adding "ma" + a's |
|---|---|---|---|---|
| 1 | I | yes | I | Imaa |
| 2 | speak | no | peaks | peaksmaaa |
| 3 | Goat | no | oatG | oatGmaaaa |
| 4 | Latin | no | atinL | atinLmaaaaa |

Result `"Imaa peaksmaaa oatGmaaaa atinLmaaaaa"`.

**Complexity:** Time O(n²) worst case, because the i-th word gets i extra letters. Space O(n²) for the output.
**Exam tip:** `tolower` is needed because `'I'` and `'A'` are vowels too.

---

## 20. Count Binary Substrings (LeetCode #696)

**Problem:** Count substrings that have equal numbers of 0s and 1s, with all the 0s grouped together and all the 1s grouped together (like `0011` or `10`).

**Idea:** Split the string into groups of equal characters. For two neighbouring groups of sizes `a` and `b`, the number of valid substrings across that boundary is `min(a, b)`. Example: groups `000` and `11` give `01`, `0011` which is `min(3,2) = 2`.

**Algorithm:**

1. Set `prev = 0`, `cur = 1`, `ans = 0`.
2. For `i` from 1 to n-1:
   1. If `s[i] == s[i-1]`, increase `cur` by 1.
   2. Otherwise, add `min(prev, cur)` to `ans`, set `prev = cur`, and set `cur = 1`.
3. Add `min(prev, cur)` to `ans` (last boundary).
4. Return `ans`.

**Code:**

```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int prev = 0, cur = 1, ans = 0;                // prev = last group size, cur = current group size
        for (int i = 1; i < s.size(); i++) {
            if (s[i] == s[i-1]) cur++;                 // same group continues
            else {                                     // group ended
                ans += min(prev, cur);                 // count across the boundary
                prev = cur;
                cur = 1;                               // new group starts
            }
        }
        return ans + min(prev, cur);                   // the final boundary
    }
};
```

**Dry run** `"00110011"`:

| i | s[i] | Action | prev | cur | ans |
|---|---|---|---|---|---|
| 1 | 0 | same | 0 | 2 | 0 |
| 2 | 1 | change, add min(0,2)=0 | 2 | 1 | 0 |
| 3 | 1 | same | 2 | 2 | 0 |
| 4 | 0 | change, add min(2,2)=2 | 2 | 1 | 2 |
| 5 | 0 | same | 2 | 2 | 2 |
| 6 | 1 | change, add min(2,2)=2 | 2 | 1 | 4 |
| 7 | 1 | same | 2 | 2 | 4 |

After the loop: `ans + min(2,2) = 6`.

**Complexity:** Time O(n), Space O(1).
**Exam tip:** do not forget the last line after the loop, because the last group never triggers a "change".

---

## 21. Valid Palindrome (LeetCode #125)

**Problem:** After removing non-alphanumeric characters and ignoring case, is the string a palindrome?

**Idea:** Two pointers from both ends. Skip characters that are not letters or digits. Compare the rest in lowercase.

**Algorithm:**

1. Set `l = 0` and `r = n-1`.
2. While `l < r`:
   1. Move `l` right while `s[l]` is not a letter or digit.
   2. Move `r` left while `s[r]` is not a letter or digit.
   3. If `lowercase(s[l]) != lowercase(s[r])`, return false.
   4. Increase `l` by 1 and decrease `r` by 1.
3. Return true.

**Code:**

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int l = 0, r = s.size() - 1;
        while (l < r) {
            while (l < r && !isalnum(s[l])) l++;       // skip junk on the left
            while (l < r && !isalnum(s[r])) r--;       // skip junk on the right
            if (tolower(s[l]) != tolower(s[r])) return false;
            l++; r--;
        }
        return true;
    }
};
```

**Line by line:**
1. `l < r` inside the skip loops stops the pointers from crossing while skipping.
2. `isalnum` is true for letters and digits.
3. If the pointers meet on the same character, comparing it with itself is harmless.

**Dry run** `"A man, a plan, a canal: Panama"`: the first comparison is `A` vs `a` (equal after `tolower`), then `m` vs `m`, `a` vs `a`, and so on, skipping spaces, commas and the colon. All pairs match, so true.
For `"race a car"`: `r` vs `r`, `a` vs `a`, `c` vs `c`, `e` vs `a`, mismatch, false.
For `"0P"`: `'0'` vs `'p'`, mismatch, false. This shows digits are kept.

**Complexity:** Time O(n), Space O(1).

---

## 22. Longest Common Prefix (LeetCode #14)

**Problem:** Find the longest prefix shared by all strings in the array.

**Idea:** Start with the first word as the prefix. For each other word, remove characters from the end of the prefix until the word starts with it.

**Algorithm:**

1. Set `pre` to the first string.
2. For each remaining string `s`:
   1. While `pre` is not a prefix of `s`, remove the last character of `pre`.
3. Return `pre`.

**Code:**

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string pre = strs[0];
        for (int i = 1; i < strs.size(); i++)
            while (strs[i].find(pre) != 0)             // pre is not at the start of strs[i]
                pre.pop_back();                        // chop the last character
        return pre;
    }
};
```

**Why `find(pre) != 0` works:** `find` returns the index where `pre` first appears. Index 0 means `pre` is at the start, which is what we want. Any other result (including `npos` for not found) means we must shorten `pre`. If `pre` becomes empty, `find("")` returns 0 and the loop ends.

**Dry run** `["flower","flow","flight"]`:

| Word | Loop | pre afterwards |
|---|---|---|
| flow | "flower" not at start of "flow", chop, becomes "flowe", "flow", now found at 0 | flow |
| flight | "flow" no, "flo" no, "fl" yes | fl |

Returns `"fl"`.
For `["dog","racecar","car"]`: `pre` shrinks from `dog` down to empty, so returns `""`.

**Complexity:** Time O(total characters), Space O(1) extra.

---

## 23. Find the Index of the First Occurrence in a String (LeetCode #28)

**Problem:** Return the first index where `needle` appears in `haystack`, or -1.

**Idea:** Slide a window of length `N` over the haystack and compare it with the needle.

**Algorithm:**

1. Let `H` be the haystack length and `N` the needle length.
2. For `i` from 0 to H-N:
   1. Take the substring of `haystack` starting at `i` with length `N`.
   2. If it equals `needle`, return `i`.
3. Return -1 (no match).

**Code:**

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        int H = haystack.size(), N = needle.size();
        for (int i = 0; i + N <= H; i++)               // last valid start is H-N
            if (haystack.substr(i, N) == needle)       // window equals needle
                return i;
        return -1;
    }
};
```

**Line by line:**
1. `i + N <= H` stops the window from running past the end of the haystack.
2. `substr(i, N)` takes `N` characters starting at `i`.
3. The first match is returned immediately, so it is the first occurrence.

**Dry run** `haystack = "mississippi"`, `needle = "issip"` (N = 5, H = 11, so i runs 0 to 6):

| i | Window | Match? |
|---|---|---|
| 0 | missi | no |
| 1 | issis | no |
| 2 | ssiss | no |
| 3 | sissi | no |
| 4 | issip | yes, return 4 |

For `"leetcode"` and `"leeto"`: no window matches, returns -1.

**Complexity:** Time O(H x N), Space O(N) for the substring copy.
**Exam tip:** `haystack.find(needle)` does the same in one line (convert `npos` to -1). The faster algorithm is KMP at O(H + N).

---

# Complexity Summary

| # | Problem | Time | Space |
|---|---|---|---|
| 26 | Remove Duplicates | O(n) | O(1) |
| 283 | Move Zeroes | O(n) | O(1) |
| 169 | Majority Element | O(n) | O(1) |
| 485 | Max Consecutive Ones | O(n) | O(1) |
| 448 | Disappeared Numbers | O(n) | O(1) |
| 414 | Third Maximum | O(n log n) | O(n) |
| 1295 | Even Digits | O(n x d) | O(d) |
| 1572 | Diagonal Sum | O(n) | O(1) |
| 867 | Transpose | O(r x c) | O(r x c) |
| 1380 | Lucky Numbers | O(r x (r + c)) | O(1) |
| 766 | Toeplitz | O(r x c) | O(1) |
| 832 | Flipping an Image | O(r x c) | O(1) |
| 1260 | Shift 2D Grid | O(m x n) | O(m x n) |
| 54 | Spiral Matrix | O(r x c) | O(1) |
| 73 | Set Matrix Zeroes | O(r x c) | O(r + c) |
| 242 | Valid Anagram | O(n) | O(1) |
| 859 | Buddy Strings | O(n) | O(1) |
| 520 | Detect Capital | O(n) | O(1) |
| 824 | Goat Latin | O(n²) | O(n²) |
| 696 | Count Binary Substrings | O(n) | O(1) |
| 125 | Valid Palindrome | O(n) | O(1) |
| 14 | Longest Common Prefix | O(total chars) | O(1) |
| 28 | First Occurrence | O(H x N) | O(N) |
