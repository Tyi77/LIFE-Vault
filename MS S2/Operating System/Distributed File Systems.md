## 基礎觀念
- 檔案的性質
	- 命名物件
	- Persistence 持久性，除非被刻意刪除不然會被永久保存
- 檔案包含
	- **資料（data）**：一連串可讀寫的項目
	- **中繼資料（metadata）**：包括檔案長度、時間戳記、型態、擁有者、存取權限等資訊
- 檔案系統(File Systems)
	- 功能包括：組織、儲存、檢索、命名、共用與保護檔案
## 分散式檔案系統的需求
- 五種**透明性** (Transparency)
	- **Access** Transparency : 無論檔案在哪裡，都可使用相同的API `open()` 和 `read()` 來存取檔案。
	- **Location** Transparency : 統一的 `namespace` ，不管檔案被搬遷到哪裡都不會變
	- **Mobility** Transparency : 檔案移動時，不需更改client的設定
	- **Performance** Transparency : 系統小變動時，不會影響client
	- **Scaling** Transparency : 節點的增加還是可以維持效能
- 其他三項關鍵能力
	- **Concurrent File Updates（並行更新處理）**    
	    - 多個用戶同時修改同一檔案時，應避免資料衝突與錯誤。        
	- **File Replication（檔案複製）**    
	    - 複製可提升：        
	        - **可擴展性**：讓多台伺服器負責不同用戶存取。            
	        - **容錯能力**：當某伺服器故障時，可改由其他複本提供服務
	- **Hardware & OS Heterogeneity（異質性支援）**
	    - DFS 應支援不同作業系統與硬體平台之間的互通性
	    - 這也與所謂的「開放性（openness）」有關
- 進階需求與語意定義
	- **Fault Tolerance（容錯能力）**    
	    - 系統在伺服器或用戶端失效時仍應保持可用性。        
	- **File Sharing Semantics（檔案共享語意）**    
		- 檔案共享的更新一致性語意需定義明確，例如：    
		    - **One-copy semantics（UNIX）**：所有人看到的是單一一致版本   
		    - **Session semantics（AFS）**：只在開啟與關閉時同步更新       
		    - **Append-only semantics（HDFS）**：只允許附加寫入，不允許修改    
		    - **Copy-on-write**：寫入時自動建立複本 
	- **Security（安全性）**    
		- 包括存取控制、身分認證、資料傳輸加密等
	- **Efficiency（效率）**    
		- 整體操作效能應盡可能接近本地檔案系統
## 命名與掛載機制（Naming & Mounting）
- **Naming** : logical file name 與 physical location 的對應機制 (mapping)
- **三種命名架構**
	1. 掛載遠端目錄（Mount remote directories）
		- 遠端dir直接mount在本地端上，使用本地路徑即可存取遠端檔案
		- e.g. Unix 的 NFS
	2. 檔名包含主機名（Host-based Naming）
		- 檔案名稱直接包含主機名，以此能保證檔案名稱唯一
	3. 完全整合的命名空間（Global Namespace）
- **NFS** : 跨系統共享檔案時還是需要mount點的配置與管理，而非僅靠檔名即可
## 檔案存取(File Access)模型+快取(Cache)相關內容
- **兩種檔案存取模型 (File Accessing Models)**
	1. Remote Service Model（遠端服務模型）
		- 讀寫都靠server，本地端不會存取副本
		- 優點：client不需大量的儲存空間、不需考慮Cache Consistency
		- 適合：大量寫入作業
		- 早期版本的 NFS 主要以此方式運作
	2. Data Caching (upload/download) Model（資料快取模型）
		- 將檔案快取在本地端，必要時才與server通訊
		- 需要維護 Cache Consistency (快取一致性)
		- 適合：多客戶端情景
- **三種快取位置策略 (Cache Location Policies)**
	1. Server Memory Caching（伺服器記憶體快取）
		- 簡單共享
	2. Client Memory Caching（客戶端記憶體快取）
		- 快速暫用
	3. Client Disk Caching（客戶端磁碟快取）
		- 大型快取
- **兩大快取更新策略（Cache Update Policies）(Write 的部分)**
	1. Write-through scheme（即時寫入）
		- 每次更新都要寫入server's disk中
		- 最可靠但效能差
	2. Delayed write scheme（延遲寫入）
		- 先更新client cache的資料，之後再更新到server
		- 三種寫入策略
			1. Write on ejection : 快取容量不夠才寫回server
			2. Periodic write : 定時寫回server
			3. Write on close : 檔案關閉時才寫回server
				- 這就是 AFS 採用的「session semantics」
- **快取驗證策略（Cache Validation Policies）(Read 的部分)**
	- 當使用延遲寫入或跨用戶端快取時，不同用戶的快取可能已不同步，驗證策略的目標是確保快取內容與伺服器主檔案一致
	- 兩大類 : client-initiated 和 server-initiated
		- client-initiated : Before every access, Periodic checking, On open (AFS)

| 策略               | 優點       | 缺點         | 代表系統       |
| ---------------- | -------- | ---------- | ---------- |
| Client-Initiated | 設計簡單，負擔小 | 效能差、一致性弱   | 早期 NFS     |
| Server-Initiated | 效能佳、一致性強 | 複雜度高、需記錄狀態 | AFS、NFS v4 |
## 檔案複製 (File Replications)
- **replica (檔案副本) 的性質**
	- 在server的disk中
	- 注重的是 performance 和 availability
	- persistent
- **Replication Transparency**
	- 兩大挑戰：Naming of Replicas 和 Replication Control
	- Naming of Replicas
		- 對於program 的 logical name 要一樣，就算是在不同server底下的replicas
	- Replication Control
		- Explicit vs. Implicit/lazy Replication : 使用者指定 vs. 系統自己決定
- **多副本更新協議（Multicopy Update Policies）**
	1. Read-Only Replication（唯讀複製）
	2. Read-One Write-All Protocol（R1Wn）
		- 讀取任一副本，寫入所有副本
		- 適合大量讀取的情境
		- 容錯性差：因為只要一個副本寫入錯誤就算失敗
	3. Available-Copies Protocol（可用副本協議）
		- 讀取任一副本，寫入可獲取的副本，允許有副本是無效的
		- 容錯能力高，但需維護consistency
	4. Primary-Copy Protocol（主副本模式）
		- 讀取任一副本，只寫入主副本，其他副本看consistency semantics
		- 容錯性高於 R1Wn
	5. Quorum-Based Protocols（表決式一致性協定）
		- 基本定義
			- 讀的時候找 `r` 個副本當候選，挑出其中版本最大的副本當作閱讀的副本
			- 寫的時候找 `w` 個副本，找出其中最大的版本號，此版本號加一，並寫入所有( `w` 個) 副本
		- 相關條件
			-  $w + r > n$ ($n$ 是副本總數) : 任何「讀操作」與「寫操作」的副本集合**至少重疊 1 個副本**
			- $2w > n$ : 任何兩個寫操作的副本集合也會**有重疊**
		- 延伸變體
			- R1Wn : r=1, w=n
				- 適合讀多寫少
			- Majority Consensus : r = w = $⌊\frac {n}{2}⌋$+1 (必定過半)
				- 簡化衝突檢查
			- Weighted Voting : 每個副本的投票權重不同 ($v$)，只需滿足 $r + w > v$ 和 $2w > v$ 就好
				- 此變體可讓「資料中心副本」有高權重，低效能節點有低權重，提升彈性
## 無狀態 vs 有狀態的檔案服務 (Stateless vs. Stateful File Service)
> p.25-28

| 類型     | Stateless Server（無狀態伺服器） | Stateful Server（有狀態伺服器）      |
| ------ | ------------------------ | ---------------------------- |
| 是否儲存狀態 | ❌ 不儲存用戶端狀態               | ✅ 記錄用戶端的開啟檔案、位置等狀態           |
| 存取模式   | 每次請求需包含完整資訊              | 請求可省略細節，伺服器記得之前的狀態           |
| 開檔/關檔  | 無需顯式 open/close          | 需使用 open() 開始、close() 結束檔案存取 |
| 容錯恢復   | ✅ 崩潰後能馬上重新提供服務           | ❌ 崩潰後需額外恢復協議                 |
| 複雜性    | 較簡單                      | 較高，需管理 session 與失敗情況         |
| 快取驗證   | 無法由伺服器主動推送               | 可由伺服器推送更新通知（如 callback）      |
| 鎖定支援   | ❌ 難以實作                   | ✅ 可支援檔案鎖定                    |
- **總結建議**

| 如果你需要…                | 建議使用…                       |
| --------------------- | --------------------------- |
| 高容錯性，重啟立即可用           | Stateless 伺服器（如 NFS v3）     |
| 支援 callback、lock、高一致性 | Stateful 伺服器（如 AFS, NFS v4） |
## 檔案服務架構與 API 設計：Flat File Service、Directory Service、Client Module
> p.29-32
- **三大模組分工**
	- **Flat File Service** : 有了UFID (Unique File ID)後，對檔案本身做動作
	- **Directory Service** : 提供「名稱 → 檔案」的對應查找。負責處理目錄（目錄也是一種特殊檔案）
	- **Client Module**
		- 每個用戶端執行端都裝有一個 Client Module
		- 整合前兩個服務，對使用者提供統一 API （open, read, write...）
		- 是扮演前端界面的角色，也負責快取等優化操作
## Network File Systems (NFS) 
> p.33-41
- 基本概念
	- NFS可讓遠端系統直接「掛載」在本地端目錄上
	- 通常採用 stateless server 架構 (至少在 NFS v3 以前)
- 好多API
- **掛載與路徑解析機制**
	- client對於server的lookup : 查看掛載的路徑
	- client需要作多次的lookup才能完整的解析遠端的路徑與存取檔案
	- 有cache的機制來記住存取過的路徑可降低解析路徑的成本
- **Cache機制**
	- 都是 client 在發送 read/write 請求，server 負責處理這些請求
	- Server Caching
		- 主要觀念
			- Read-Ahead : 預測用戶會開啟什麼檔案，先cache住這些檔案
			- Delayed-Write : 先放在記憶體中，等到某些條件觸發後再寫入磁碟
		- NFS v3 提供的兩種方法
			- Write-Through Caching : 每次寫入都直接寫進disk中
			- Committed-Write Caching : 先放在記憶體中，等接到 client 發出commit的訊息才寫入disk中。在client的實作，通常是在關閉檔案時會自動發送commit
	- Client Caching
		- 快取 data blocks, file attributes, lookup查詢結果, getattr和readdir結果...在client端
		- 會有資料一致性的問題。驗證快取是否可用...
			- 需符合 ($T$ 現在時間, $T_c$ 本地端最後**驗證**時間, $T_m$ 檔案最後**修改**時間)
				- $T - T_c < freshness\ interval$
				- $T_m(client) = T_m(server)$
	- Dirty Page 管理
		- 當client對於cache的資料做寫入時，會被標記為 dirty page
		- 送回server
			- 等檔案被`close()`了或 `sync()`
			- 背景程序 (如 UNIX 的 `biod`) 非同步處理
## Andrew File System (AFS)
> p.42-53
- 基本觀念
	- **設計來克服 NFS 一致性與擴展性問題的分散式檔案系統**
- **兩大設計理念**
	- Whole-File Serving（整檔傳送）
	- Whole-File Caching（整檔快取）
		- 快取在 local disk 中 (非記憶體)
- **此設計的假設**
	- 檔案都比較小
	- 讀的頻率比寫的頻率高
	- 通常都是一人讀寫的情況

- **運作方式+Callback機制**
	- Vice 是 server 的角色，Venus 是 client 的角色
	- 三大行為
		- **open** : 看cache有沒有檔案，沒就從Vice拿。拿的時候 Vice 會一併檔案傳輸 Callback Promise 給 Venus，並標記為 **valid**
		- **read/write** : 都對 Venus cache 做，跟 Vice 無關
		- **close** : 回傳檔案至 Vice ，Vice 會對於所有擁有此檔案 Callback Promise 的 Venus 傳送 Callback，Venus 收到此  Callback 就會在 cache 中的此 Callback Promise 上標記 **canceled**。這樣重新要 open 此檔案時，就需要重新到 server 拿取。
	- Callback Promise 有 valid 和 canceled 兩種狀態
	- Client 被重啟後，需要透過 Venus 向 Vice 發送 file validation request 來保證所有的 callback promise 是正確的狀態
## Hadoop Distributed File System (HDFS)
> p.55
- 這是為了解決大數據（Big Data）儲存與處理問題所設計的現代分散式檔案系統，廣泛應用於 Hadoop 生態系中（如 MapReduce 、Spark 、Hive 等）
- 架構總覽
	- 