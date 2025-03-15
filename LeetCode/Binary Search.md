> [LeetCode 簡單題](https://leetcode.com/problems/search-insert-position)
> [LeetCode 進階題](https://leetcode.com/problems/zero-array-transformation-ii?envType=daily-question&envId=2025-03-13)
> [LeetCode 進階題](https://leetcode.com/problems/house-robber-iv?envType=daily-question&envId=2025-03-15)
> [Medium上的完整教學](https://medium.com/appworks-school/binary-search-%E9%82%A3%E4%BA%9B%E8%97%8F%E5%9C%A8%E7%B4%B0%E7%AF%80%E8%A3%A1%E7%9A%84%E9%AD%94%E9%AC%BC-%E4%B8%80-%E5%9F%BA%E7%A4%8E%E4%BB%8B%E7%B4%B9-dd2cd804aee1)
# Binary Search 的適用條件
- 最大值或最小值問題
- 有一個有限的查找範圍。例如初始的最小與最大值是array中的最小與最大值
- 搜尋答案行為要符合「單調遞增」或「單調遞減」
# 固定模式
- 一定是 while (left <= right)
- left 和 right 分別是「答案」「有可能的」最小值與最大值
- 會有個「評估function」來評估 mid 是否符合條件
- 如果符合條件，ans=mid。然後再調整 left 和 right 來搜尋最佳解答
- 如何調整 left 跟 right？
	- 主要看題目要的是什麼。如果題目要的是「最小」，那 ans=mid 後面就會接 right = mid - 1。如果題目要的是「最大」，那 ans=mid 後面就會接 left = mid + 1。
# 舉例
- 題目範例：陣列中k個數值，k個數值的index不能相鄰。要求符合條件的不同k個數值的組合中，哪一個組合的所得出的最大值，是所有組合中最小的，回傳此最小值 (上面LeetCode 進階題 2 的題目)
	- left 和 right 分別就為此陣列的最小值與最大值
	- 評估function：評估是否能在陣列中找到 k 個 <= mid 且不相鄰的數值
	- 因為找的是「最小」，所以 ans=mid 後接的是 right = mid - 1。