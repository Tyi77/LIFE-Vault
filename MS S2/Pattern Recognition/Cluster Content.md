## Set08 大綱
1. **Clustering Fundamentals**
    - 定義：何謂聚類 (clustering) 與分類 (classification) 之區別
    - 硬劃分 (crisp partition) 與模糊劃分 (fuzzy partition)
2. **Clustering Criterion & Proximity Measures**
    - 聚類準則 (色彩、形狀…) 如何轉為成本函數
    - 點–點、點–集、集–集相似度/距離度量
3. **Clustering Relational Data**
    - 只用樣本間關係 (similarity/dissimilarity) 進行聚類
4. **Sequential Clustering (序列演算法)**    
    - 样本逐一加入的決策流程        
    - 閾值 (Θ) 與最大群數 (q) 的選擇        
5. **Cluster Validity (群體效度評估)**    
    - 內部評估指標 (internal validity)：只用聚類結果本身計算        
    - 外部評估指標 (external validity)：引入真實標籤或先驗結構        
6. **常見內部效度指標**    
    - Dunn’s Index ($D_m$) ↗ $$D_m = \frac{ \min_{i \ne j} d(C_i, C_j) }{ \max_k \left[ \operatorname{diam}(C_k) \right] }$$
    - Davies–Bouldin Index ($DB_m$) ↙
    - Xie–Beni Index (XB) ↙
    - Silhouette Index (SI) ↗
7. **Objective Evaluation (客觀評估)**    
    - Rand Index (RI)
    - Adjusted Rand Index (ARI)        
    - Normalized Mutual Information (NMI)        
    - Clustering Accuracy (Hungarian 演算法對應)        

> **重點**：Set08 著重於**聚類的基本概念**與**如何評價聚類結果的好壞**，從最基礎的劃分方式（硬／模糊）、相似度度量，到一系列內部與外部的效度指標，並詳細介紹各種指標的計算與適用情境，為後續選擇與調整聚類算法的超參數提供依據。

---
## Set09 大綱
1. **Clustering Algorithms Overview**    
    - Optimization‐based, Hierarchical, Density‐based, Graph‐based        
2. **K-Means (Lloyd’s Algorithm)**    
    - 原理、流程、成本函數與交替優化        
    - Superpixel (SLIC) 應用
3. **Fuzzy K-Means (FKM)**    
    - 模糊隸屬度 ==(fuzzification factor q)== (q > 1)
	    - **小 q**（接近 1）→ 劃分較「硬」，接近 k-Means
	    - **大 q** → 劃分較「軟」，群邊界更模糊、收斂更平穩但更慢。
    - 更新方程式與成本
    - 每個點都會有不同比例的不同cluster數值，不會是非黑及白的被分配到哪個cluster
4. **Mixture Models & EM Algorithm**    
    - 高斯混合 (GMM) 表示為聚類        
    - E-步與 M-步迭代流程        
5. **Hierarchical Clustering**    
    - Agglomerative (bottom-up) vs. Divisive (top-down)        
    - Single/Complete/Average/Ward linkage        
    - Dendrogram 可視化        
6. **Density-Based Clustering**    
    - DBSCAN：核心點、ε 邻域、MinPts        
    - OPTICS：reachability distance 排序結果        
7. **Mode-Seeking (Mean-Shift)**    
    - 核密度估計、帶寬參數        
    - 迭代移動至局部密度峰值        
8. **Graph-Cut & Spectral Clustering**    
    - 最小切割、normalized/ratio cut        
    - Laplacian 矩陣構建與特徵向量放鬆        
    - k-means 在特徵空間中的應用        

> **重點**：Set09 聚焦於**各類聚類演算法的機制與實作**，逐一介紹從最經典的 k-means，到可處理非球形、噪聲（DBSCAN、OPTICS）、階層結構（hierarchical）、再到基於圖論與頻譜的 spectral clustering。每種方法的**假設、流程、優缺點**皆有清晰闡述，並輔以實例圖示，便於理解各算法在不同資料特性下的適用場景。

## 筆記
- Internal Cluster Validity Criteria
	- To **maximize** $$\cfrac {Seperation (distances)\ between\ clusters}{Scatters (sizes)\ of\ clusters}$$
	- or **minimize** its inverse in some form