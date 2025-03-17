> [LeetCode 3356](https://leetcode.com/problems/zero-array-transformation-ii?envType=daily-question&envId=2025-03-13)

- 目的：加快針對一個「連續陣列」「加上同個數」時的情況
	- 使用 difference array 紀錄所有更動的情況
	- 最後使用 prefix sum 填下陣列最後的樣子
### **Example**
#### **Applying Increment Operations Efficiently**
Consider an initial array:
`arr = [5, 3, 8, 6, 2]`

We want to **increase** elements in the following ranges:
1. Increase range `[1, 3]` by `2`
2. Increase range `[2, 4]` by `3`

Instead of modifying `arr` directly, we use a difference array:
`diff = [0, 2, 0, 0, -2, 0] # (Applying first update)`
`diff = [0, 2, 3, 0, -2, -3] # (Applying second update)`

Now, we compute the prefix sum:
`prefixSum = [0, 2, 5, 5, 3]`

Final result:
`arr = [5, 5, 13, 11, 3]`