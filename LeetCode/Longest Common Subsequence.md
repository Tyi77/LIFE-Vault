> Link: https://web.ntnu.edu.tw/~algo/Subsequence2.html
- 給定兩個陣列，可任意刪除其中的元素，但不可改變元素的順序。請找出這兩個陣列中最長的公共子序列。
- Link是師大演算法的解說，非常清楚，可看
```c++
class Solution {
  public:
    // Function to find the length of the longest common subsequence in two strings.
    int lcs(string& s1, string& s2) {
        int n = s1.size()+1, m = s2.size()+1;
        
        vector<vector<int>> dp(n, vector<int>(m, 0));
        for(int i = 1; i < n; ++i) {
            for (int j = 1; j < m; ++j) {
                if (s1[i-1] == s2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }
        return dp[n-1][m-1];
    }
};
```
