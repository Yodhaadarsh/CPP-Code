# LeetCode Highlighted Problems - C++ Solutions

Each problem has the LeetCode number, the code (paste directly into LeetCode), a dry run, and a line to remember for the exam.

| Sheet | Problems |
|---|---|
| Arrays | 26, 283, 169, 485, 448, 414, 1295 |
| Matrix | 1572, 867, 1380, 766, 832, 1260, 54, 73 |
| Strings | 242, 859, 520, 824, 696, 125, 14, 28 |

---

# Sheet 1: Arrays

## 1. Remove Duplicates from Sorted Array (LeetCode #26)

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int k = 1;
        for (int i = 1; i < nums.size(); i++)
            if (nums[i] != nums[i-1])
                nums[k++] = nums[i];
        return k;
    }
};
```

**Dry run** `[1,1,2]`:
- k=1
- i=1: nums[1]=1 equals nums[0]=1, skip
- i=2: nums[2]=2 differs from 1, so nums[1]=2, k=2
- Returns 2

**Remember:** `k` is the write pointer. Write only when the value differs from the previous one.

---

## 2. Move Zeroes (LeetCode #283)

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int j = 0;
        for (int i = 0; i < nums.size(); i++)
            if (nums[i] != 0)
                swap(nums[i], nums[j++]);
    }
};
```

**Dry run** `[0,1,0,3]`:
- i=0: value 0, skip
- i=1: value 1, swap with j=0, array `[1,0,0,3]`, j=1
- i=2: value 0, skip
- i=3: value 3, swap with j=1, array `[1,3,0,0]`, j=2

**Remember:** swap each non-zero element into position `j`.

---

## 3. Majority Element (LeetCode #169)

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int cand = 0, cnt = 0;
        for (int x : nums) {
            if (cnt == 0) cand = x;
            cnt += (x == cand) ? 1 : -1;
        }
        return cand;
    }
};
```

**Dry run** `[2,2,1,1,1,2,2]`:

| x | cand | cnt |
|---|---|---|
| 2 | 2 | 1 |
| 2 | 2 | 2 |
| 1 | 2 | 1 |
| 1 | 2 | 0 |
| 1 | 1 | 1 |
| 2 | 1 | 0 |
| 2 | 2 | 1 |

Returns 2.

**Remember:** Boyer-Moore voting. If the count hits 0, pick a new candidate. Matching adds 1, otherwise subtract 1.

---

## 4. Max Consecutive Ones (LeetCode #485)

```cpp
class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int best = 0, cur = 0;
        for (int x : nums) {
            cur = (x == 1) ? cur + 1 : 0;
            best = max(best, cur);
        }
        return best;
    }
};
```

**Dry run** `[1,1,0,1,1,1]`:
- cur goes 1, 2, 0, 1, 2, 3
- best ends at 3

**Remember:** extend the current run on a 1, reset on a 0, and keep the maximum.

---

## 5. Find All Numbers Disappeared in an Array (LeetCode #448)

```cpp
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        for (int x : nums) {
            int i = abs(x) - 1;
            nums[i] = -abs(nums[i]);
        }
        vector<int> res;
        for (int i = 0; i < nums.size(); i++)
            if (nums[i] > 0) res.push_back(i + 1);
        return res;
    }
};
```

**Dry run** `[4,3,2,7,8,2,3,1]`:
- The values mark indices 3, 2, 1, 6, 7, 1, 2, 0 as negative
- Indices 4 and 5 stay positive
- Answer: `[5,6]`

**Remember:** the value `x` means "mark index x-1 negative". Whatever stays positive is the missing number.

---

## 6. Third Maximum Number (LeetCode #414)

```cpp
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        set<int> s(nums.begin(), nums.end());
        if (s.size() < 3) return *s.rbegin();
        auto it = s.rbegin();
        advance(it, 2);
        return *it;
    }
};
```

**Dry run** `[2,2,3,1]`:
- set = {1,2,3}
- From the largest end: 3, then 2, then 1
- Third maximum = 1

**Remember:** a `set` is sorted and unique, so `rbegin()` is the maximum. With fewer than 3 distinct values, return the maximum.

---

## 7. Find Numbers with Even Number of Digits (LeetCode #1295)

```cpp
class Solution {
public:
    int findNumbers(vector<int>& nums) {
        int cnt = 0;
        for (int x : nums)
            if (to_string(x).size() % 2 == 0) cnt++;
        return cnt;
    }
};
```

**Dry run** `[12,345,2,6,7896]`:
- Digit counts: 2, 3, 1, 1, 4
- Even counts: 12 and 7896
- Answer: 2

**Remember:** convert to a string and check whether the length is even.

---

# Sheet 2: Matrix

## 8. Matrix Diagonal Sum (LeetCode #1572)

```cpp
class Solution {
public:
    int diagonalSum(vector<vector<int>>& mat) {
        int n = mat.size(), s = 0;
        for (int i = 0; i < n; i++)
            s += mat[i][i] + mat[i][n-1-i];
        if (n % 2) s -= mat[n/2][n/2];
        return s;
    }
};
```

**Dry run** `[[1,2,3],[4,5,6],[7,8,9]]`:
- i=0: 1 + 3 = 4
- i=1: 5 + 5 = 10, total 14
- i=2: 9 + 7 = 16, total 30
- n is odd, so subtract the centre 5, giving 25

**Remember:** primary is `[i][i]`, secondary is `[i][n-1-i]`. For odd n the centre is counted twice.

---

## 9. Transpose Matrix (LeetCode #867)

```cpp
class Solution {
public:
    vector<vector<int>> transpose(vector<vector<int>>& matrix) {
        int r = matrix.size(), c = matrix[0].size();
        vector<vector<int>> t(c, vector<int>(r));
        for (int i = 0; i < r; i++)
            for (int j = 0; j < c; j++)
                t[j][i] = matrix[i][j];
        return t;
    }
};
```

**Dry run** `[[1,2,3],[4,5,6]]`:
- r=2, c=3, so t is 3x2
- t[0][0]=1, t[0][1]=4, t[1][0]=2, t[1][1]=5, t[2][0]=3, t[2][1]=6
- Result: `[[1,4],[2,5],[3,6]]`

**Remember:** the result has swapped dimensions `t[c][r]`, and `t[j][i] = matrix[i][j]`.

---

## 10. Lucky Numbers in a Matrix (LeetCode #1380)

```cpp
class Solution {
public:
    vector<int> luckyNumbers(vector<vector<int>>& matrix) {
        vector<int> res;
        int r = matrix.size(), c = matrix[0].size();
        for (int i = 0; i < r; i++) {
            int mn = 0;
            for (int j = 1; j < c; j++)
                if (matrix[i][j] < matrix[i][mn]) mn = j;
            bool ok = true;
            for (int k = 0; k < r; k++)
                if (matrix[k][mn] > matrix[i][mn]) ok = false;
            if (ok) res.push_back(matrix[i][mn]);
        }
        return res;
    }
};
```

**Dry run** `[[3,7,8],[9,11,13],[15,16,17]]`:
- Row 0: min is 3 (column 0), column 0 has 15 which is bigger, so no
- Row 1: min is 9 (column 0), 15 is bigger, so no
- Row 2: min is 15 (column 0), it is the column maximum, so yes
- Result: `[15]`

**Remember:** find the row minimum, then check it is the maximum of its column.

---

## 11. Toeplitz Matrix (LeetCode #766)

```cpp
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        for (int i = 1; i < matrix.size(); i++)
            for (int j = 1; j < matrix[0].size(); j++)
                if (matrix[i][j] != matrix[i-1][j-1]) return false;
        return true;
    }
};
```

**Dry run** `[[1,2,3,4],[5,1,2,3],[9,5,1,2]]`:
- Every cell equals its top-left neighbour (1=1, 2=2, 3=3, 5=5, 1=1, 2=2)
- Returns true

**Remember:** compare each cell with its top-left neighbour.

---

## 12. Flipping an Image (LeetCode #832)

```cpp
class Solution {
public:
    vector<vector<int>> flipAndInvertImage(vector<vector<int>>& image) {
        for (auto& row : image) {
            reverse(row.begin(), row.end());
            for (int& x : row) x ^= 1;
        }
        return image;
    }
};
```

**Dry run** `[[1,1,0],[1,0,1],[0,0,0]]`:
- Row `[1,1,0]`: reversed `[0,1,1]`, inverted `[1,0,0]`
- Row `[1,0,1]`: reversed `[1,0,1]`, inverted `[0,1,0]`
- Row `[0,0,0]`: reversed `[0,0,0]`, inverted `[1,1,1]`
- Result: `[[1,0,0],[0,1,0],[1,1,1]]`

**Remember:** reverse each row, then flip each bit with `x ^= 1`.

---

## 13. Shift 2D Grid (LeetCode #1260)

```cpp
class Solution {
public:
    vector<vector<int>> shiftGrid(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> res(m, vector<int>(n));
        k %= (m * n);
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++) {
                int p = (i * n + j + k) % (m * n);
                res[p / n][p % n] = grid[i][j];
            }
        return res;
    }
};
```

**Dry run** 3x3 grid 1..9, k=1:
- Element 9 is at flat index 8, so p = (8+1) % 9 = 0 and res[0][0] = 9
- Element 1 is at index 0, so p = 1 and res[0][1] = 1
- Result: `[[9,1,2],[3,4,5],[6,7,8]]`

**Remember:** treat the grid as a flat array. New index is `(i*n+j+k) % (m*n)`, convert back with `/ n` and `% n`.

---

## 14. Spiral Matrix (LeetCode #54)

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int top = 0, bottom = matrix.size() - 1;
        int left = 0, right = matrix[0].size() - 1;
        while (top <= bottom && left <= right) {
            for (int j = left; j <= right; j++) res.push_back(matrix[top][j]);
            top++;
            for (int i = top; i <= bottom; i++) res.push_back(matrix[i][right]);
            right--;
            if (top <= bottom) {
                for (int j = right; j >= left; j--) res.push_back(matrix[bottom][j]);
                bottom--;
            }
            if (left <= right) {
                for (int i = bottom; i >= top; i--) res.push_back(matrix[i][left]);
                left++;
            }
        }
        return res;
    }
};
```

**Dry run** 3x3 grid 1..9:
- Top row: 1, 2, 3 (top=1)
- Right column: 6, 9 (right=1)
- Bottom row, right to left: 8, 7 (bottom=1)
- Left column, bottom to top: 4 (left=1)
- Next loop: top row gives 5, the remaining checks fail, stop
- Result: `1,2,3,6,9,8,7,4,5`

**Remember:** four boundaries and four loops (right, down, left, up). Shrink a boundary after each loop, and guard the last two loops with `if`.

---

## 15. Set Matrix Zeroes (LeetCode #73)

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int r = matrix.size(), c = matrix[0].size();
        vector<bool> row(r, false), col(c, false);
        for (int i = 0; i < r; i++)
            for (int j = 0; j < c; j++)
                if (matrix[i][j] == 0) row[i] = col[j] = true;
        for (int i = 0; i < r; i++)
            for (int j = 0; j < c; j++)
                if (row[i] || col[j]) matrix[i][j] = 0;
    }
};
```

**Dry run** `[[1,1,1],[1,0,1],[1,1,1]]`:
- The zero at (1,1) sets row[1] and col[1]
- Second pass zeroes row 1 and column 1
- Result: `[[1,0,1],[0,0,0],[1,0,1]]`

**Remember:** two passes. First mark rows and columns, then zero them. This uses O(m+n) space, and the O(1) trick is only needed if the question demands it.

---

# Sheet 3: Strings

## 16. Valid Anagram (LeetCode #242)

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        int cnt[26] = {0};
        for (char c : s) cnt[c - 'a']++;
        for (char c : t) cnt[c - 'a']--;
        for (int x : cnt) if (x != 0) return false;
        return true;
    }
};
```

**Dry run** `"anagram"` vs `"nagaram"`:
- Counting s then subtracting t brings every count back to 0
- Returns true

**Remember:** count up for s, count down for t, and everything must be 0. Alternative: `sort(s); sort(t); return s == t;`

---

## 17. Buddy Strings (LeetCode #859)

```cpp
class Solution {
public:
    bool buddyStrings(string s, string goal) {
        if (s.size() != goal.size()) return false;
        if (s == goal) {
            set<char> st(s.begin(), s.end());
            return st.size() < s.size();
        }
        vector<int> d;
        for (int i = 0; i < s.size(); i++)
            if (s[i] != goal[i]) d.push_back(i);
        return d.size() == 2 && s[d[0]] == goal[d[1]] && s[d[1]] == goal[d[0]];
    }
};
```

**Dry run:**
- `"ab"` vs `"ba"`: d=[0,1], s[0]='a'==goal[1]='a' and s[1]='b'==goal[0]='b', so true
- `"aa"` vs `"aa"`: strings equal and a letter repeats, so true

**Remember:** three cases. Different lengths give false. Equal strings need a repeated letter. Otherwise there must be exactly 2 mismatches that cross-match.

---

## 18. Detect Capital (LeetCode #520)

```cpp
class Solution {
public:
    bool detectCapitalUse(string word) {
        int up = 0;
        for (char c : word) if (isupper(c)) up++;
        return up == word.size() || up == 0 || (up == 1 && isupper(word[0]));
    }
};
```

**Dry run:**
- `"USA"`: up=3 equals length, true
- `"Google"`: up=1 and first letter is uppercase, true
- `"FlaG"`: up=2, false

**Remember:** the count of capitals must be all, none, or exactly one at the start.

---

## 19. Goat Latin (LeetCode #824)

```cpp
class Solution {
public:
    string toGoatLatin(string sentence) {
        stringstream ss(sentence);
        string w, res = "";
        int i = 1;
        while (ss >> w) {
            char f = tolower(w[0]);
            if (f != 'a' && f != 'e' && f != 'i' && f != 'o' && f != 'u')
                w = w.substr(1) + w[0];
            w += "ma" + string(i, 'a');
            res += w + " ";
            i++;
        }
        res.pop_back();
        return res;
    }
};
```

**Dry run** `"I speak"`:
- "I" is a vowel, so "I" + "ma" + "a" = `Imaa`
- "speak" becomes "peaks", then + "ma" + "aa" = `peaksmaaa`
- Result: `"Imaa peaksmaaa"`

**Remember:** `stringstream` splits on spaces. `string(i,'a')` builds i copies of 'a'. Remove the trailing space at the end.

---

## 20. Count Binary Substrings (LeetCode #696)

```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int prev = 0, cur = 1, ans = 0;
        for (int i = 1; i < s.size(); i++) {
            if (s[i] == s[i-1]) cur++;
            else {
                ans += min(prev, cur);
                prev = cur;
                cur = 1;
            }
        }
        return ans + min(prev, cur);
    }
};
```

**Dry run** `"00110011"`:
- Group sizes: 2, 2, 2, 2
- Boundaries add min(prev, cur): 0, then 2, then 2
- Final line adds 2
- Total: 6

**Remember:** answer = sum of min(adjacent group sizes).

---

## 21. Valid Palindrome (LeetCode #125)

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int l = 0, r = s.size() - 1;
        while (l < r) {
            while (l < r && !isalnum(s[l])) l++;
            while (l < r && !isalnum(s[r])) r--;
            if (tolower(s[l]) != tolower(s[r])) return false;
            l++; r--;
        }
        return true;
    }
};
```

**Dry run** `"A man, a plan, a canal: Panama"`:
- Pointers skip spaces and punctuation
- Compares a=a, m=m, a=a, n=n, and so on
- Returns true

**Remember:** two pointers, skip non-alphanumerics, compare in lowercase. Uses O(1) space.

---

## 22. Longest Common Prefix (LeetCode #14)

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string pre = strs[0];
        for (int i = 1; i < strs.size(); i++)
            while (strs[i].find(pre) != 0)
                pre.pop_back();
        return pre;
    }
};
```

**Dry run** `["flower","flow","flight"]`:
- pre = `flower`, shortened to `flow` for "flow"
- For "flight": `flow`, then `flo`, then `fl`
- Result: `"fl"`

**Remember:** start with the first word and chop its last character until every word starts with it. `find(pre) != 0` means "pre is not at the start". An empty `pre` gives `find("") == 0`, so the loop ends.

---

## 23. Find the Index of the First Occurrence in a String (LeetCode #28)

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        int H = haystack.size(), N = needle.size();
        for (int i = 0; i + N <= H; i++)
            if (haystack.substr(i, N) == needle) return i;
        return -1;
    }
};
```

**Dry run** `haystack="sadbutsad"`, `needle="sad"`:
- i=0: `substr(0,3)` is "sad", so it returns 0

**Remember:** slide a window of length N over the haystack and compare. Built-in shortcut: `haystack.find(needle)` returns `string::npos` if not found, so convert that to -1.

---

# Quick Reference Table

| # | Problem | LeetCode No. | Key Idea |
|---|---|---|---|
| 1 | Remove Duplicates from Sorted Array | 26 | Write pointer |
| 2 | Move Zeroes | 283 | Swap non-zero to `j` |
| 3 | Majority Element | 169 | Boyer-Moore voting |
| 4 | Max Consecutive Ones | 485 | Running count, reset on 0 |
| 5 | Find All Numbers Disappeared | 448 | Mark index negative |
| 6 | Third Maximum Number | 414 | Set + reverse iterator |
| 7 | Even Number of Digits | 1295 | `to_string().size() % 2` |
| 8 | Matrix Diagonal Sum | 1572 | `[i][i]` + `[i][n-1-i]`, minus centre |
| 9 | Transpose Matrix | 867 | `t[j][i] = m[i][j]` |
| 10 | Lucky Numbers in a Matrix | 1380 | Row min that is column max |
| 11 | Toeplitz Matrix | 766 | Compare with top-left |
| 12 | Flipping an Image | 832 | Reverse, then XOR 1 |
| 13 | Shift 2D Grid | 1260 | Flat index `(i*n+j+k) % (m*n)` |
| 14 | Spiral Matrix | 54 | Four boundaries |
| 15 | Set Matrix Zeroes | 73 | Row and column marker arrays |
| 16 | Valid Anagram | 242 | Count array of 26 |
| 17 | Buddy Strings | 859 | 3 cases |
| 18 | Detect Capital | 520 | Count uppercase letters |
| 19 | Goat Latin | 824 | stringstream + rules |
| 20 | Count Binary Substrings | 696 | Sum of min of adjacent groups |
| 21 | Valid Palindrome | 125 | Two pointers |
| 22 | Longest Common Prefix | 14 | Chop prefix until it matches |
| 23 | Find Index of First Occurrence | 28 | Sliding window compare |
