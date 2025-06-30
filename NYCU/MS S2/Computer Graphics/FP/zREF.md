## Kezia 給的
- [Efficient Environment Map Rendering Based on Decomposition](https://onlinelibrary.wiley.com/doi/10.1111/cgf.15264)
- [LightSlice: matrix slice sampling for the many-lights problem](https://dl.acm.org/doi/10.1145/2070781.2024213#supplementary-materials)

| 步驟                                   | 目的與作法                                                                       | 關聯理論／優點                                     |
| ------------------------------------ | --------------------------------------------------------------------------- | ------------------------------------------- |
| **(a) Matrix Slicing**               | 以 6D (k-d) 分割（x y z + nx ny nz）反覆二分，直到包涵畫素數 <1 萬或包圍盒足夠小，得到 𝑟 個 slice S₁⋯Sᵣ | 保證「幾何近似 ⇒ 光照模式近似」，為後續低秩假設鋪路                 |
| **(b) Slice Sampling → R**           | 隨機抓每個 Sᵢ 一個代表畫素，追蹤全部 n 光源得一整行，將 r 行疊成**縮減矩陣 R**                             | R ≈ 分層抽樣，含陰影資訊，可揭示**全域光的宏觀結構**且只佔原矩陣 ≪1%    |
| **(c) Initial Light Clustering**     | 在 R 上跑 k-means（或相似演算法）把 n 欄粗分成 c 群，得到初步「光樹」 Cᴿ₁…Cᴿ𝑐                        | 快速分出「亮且遍及全圖」VS「暗且局部」兩型光，成本被 r 行攤平           |
| **(d) Per-Slice Cluster Refinement** | 針對每個 Sᵢ，再用 Sᵢ 與鄰近 slice 的代表行組 **局部矩陣 Lᵢ**，把高秩的 Cᴿₜ 進一步細分為 Cᴸᵢₜ₁… ，留下低秩群不動   | 兼顧 **影子遮蔽**（僅出現在少數 slice）與局部光；避免為所有像素渲染零強度欄 |
| **(e) Per-Slice Reconstruction**     | 對 Sᵢ 的每個最終群隨機選一代表光 jₖ，以機率∝‖Rⱼₖ‖，只追蹤 **那一欄對 Sᵢ 的行元素**，加總即得該 slice 色值         | 只渲染最多需要資訊的元素，類似 mrcs 的列抽樣但範圍受 slice 限制      |
| **(f) Anti-aliasing（選擇性）**           | 若對每畫素做 s 個 gather points，則在同群中隨機抽 s 盞代表光對應分派給這些點，可增加陰影取樣密度而不增雜訊             | 兼顧去鋸齒與收斂速度                                  |