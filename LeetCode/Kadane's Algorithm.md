> [LeetCode 1749](https://leetcode.com/problems/maximum-absolute-sum-of-any-subarray?envType=daily-question&envId=2025-02-26)
> [GeeksforGeeks](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)
- Kadane’s Algorithm 是一種用來解決**最大子陣列和問題**（Maximum Subarray Problem）的演算法，主要用來找出一個包含實數陣列中連續子陣列的最大總和。
- 下面為陣列只包含整數的範例
```c++
int maxSubarraySum(vector<int>& nums) {
    int maxAns = nums[0];
    int curr = 0;
    for (int& num : nums) {
	    curr = max(curr, curr + num);
	    maxAns = max(maxAns, curr);
    }
    return maxAns;
}

int main() {
    vector<int> arr = {2, 3, -8, 7, -1, 2, 3};
    cout << maxSubarraySum(arr);
    return 0;
}
```
