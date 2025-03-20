> [LeetCode 3108 Hard](https://leetcode.com/problems/minimum-cost-walk-in-weighted-graph/description/?envType=daily-question&envId=2025-03-20)
## ChatGPT 上的內容
- Disjoint Set Union（DSU），又稱為Union-Find，是一種用來管理不相交集合（Disjoint Sets）的數據結構。它常用於解決網絡連通性、最小生成樹（如Kruskal算法）等問題。其核心在於支持兩個基本操作：**find** 和 **union**。
---
### 主要操作
1. **find(x)：**  
    找到元素 x 所屬集合的代表（根節點）。這個操作會沿著父節點鏈向上查找，直到遇到自身作為父節點的節點。為了加速後續查找，我們通常會在查找過程中進行「路徑壓縮」（Path Compression），將沿途所有節點直接連接到根節點，從而使樹變得更扁平。
    
2. **union(x, y)：**  
    將包含 x 和 y 的兩個集合合併成一個集合。首先通過find操作找到兩個元素的根節點，如果它們不相同，則將其中一個集合的根節點指向另一個集合的根節點。為了保持樹的平衡，常用「按秩合併」（Union by Rank）或「按大小合併」（Union by Size）的策略，即將深度較小的樹接到深度較大的樹上。
---
### 操作步驟
1. **初始化：**
    - 為每個元素建立一個集合，令 `parent[i] = i`。
    - 初始化每個集合的秩（rank）或大小（size），通常初始秩設為 0。
2. **find 操作（==帶路徑壓縮==）：**
    - 當我們執行 `find(x)` 時，如果 `parent[x]` 不等於 x，則遞歸查找 `parent[x]` 的根節點，並將 `parent[x]` 更新為該根節點。
    - 這樣做的目的是讓之後的查找操作能更快地直接訪問根節點。
    **示例偽代碼：**
    ```
    function find(x):
        if parent[x] ≠ x:
            parent[x] = find(parent[x])
        return parent[x]
    ```
3. **union 操作（==按秩合併==）：**
    - 執行 `union(x, y)` 時，首先通過find操作獲得 x 和 y 的根節點：`rootX = find(x)` 和 `rootY = find(y)`。
    - 如果 `rootX` 與 `rootY` 不同，則根據秩來決定哪個根成為合併後的根：
        - 如果 `rank[rootX] < rank[rootY]`，則設置 `parent[rootX] = rootY`。
        - 如果 `rank[rootX] > rank[rootY]`，則設置 `parent[rootY] = rootX`。
        - 如果秩相等，則可以任選一個作為根，並將其秩增加 1。
    **示例偽代碼：**
    ```
    function union(x, y):
        rootX = find(x)
        rootY = find(y)
        if rootX == rootY:
            return
        if rank[rootX] < rank[rootY]:
            parent[rootX] = rootY
        else if rank[rootX] > rank[rootY]:
            parent[rootY] = rootX
        else:
            parent[rootY] = rootX
            rank[rootX] += 1
    ```
---
### 總結
- **Disjoint Set Union** 用於快速判斷兩個元素是否屬於同一集合以及將兩個集合合併。
- **find** 操作通過==路徑壓縮==優化，使查找更高效。
- **union** 操作通過==按秩合併（或按大小合併）==來保持樹的平衡，避免退化成鏈表結構，從而保證操作的平均時間複雜度接近於 $O(\alpha(n))$，其中 $\alpha$ 為阿克曼函數的反函數，非常緩慢增長。

這些特性使得 DSU 成為許多圖論和集合問題中極其實用的工具。