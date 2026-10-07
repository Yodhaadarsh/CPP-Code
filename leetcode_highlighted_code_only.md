# Highlighted LeetCode Problems: C++ Code Only

## 1. Remove Duplicates from Sorted Array (LeetCode #26)

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

## 2. Majority Element (LeetCode #169)

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

## 3. Max Consecutive Ones (LeetCode #485)

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

## 4. Find All Numbers Disappeared in an Array (LeetCode #448)

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

## 5. Matrix Diagonal Sum (LeetCode #1572)

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

## 6. Transpose Matrix (LeetCode #867)

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

## 7. Toeplitz Matrix (LeetCode #766)

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

## 8. Flipping an Image (LeetCode #832)

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

## 9. Valid Anagram (LeetCode #242)

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

## 10. Valid Palindrome (LeetCode #125)

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

## 11. Longest Common Prefix (LeetCode #14)

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

## 12. Find the Index of the First Occurrence in a String (LeetCode #28)

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

