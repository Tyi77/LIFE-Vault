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
- Naming : logical file name 與 physical location 的對應機制 (mapping)
- 三種命名架構
	1. 掛載遠端目錄（Mount remote directories）
		- 遠端dir直接mount在本地端上，使用本地路徑即可存取遠端檔案
		- e.g. Unix 的 NFS
	2. 檔名包含主機名（Host-based Naming）
		- 檔案名稱直接包含主機名，以此能保證檔案名稱唯一
	3. 完全整合的命名空間（Global Namespace）
- NFS : 跨系統共享檔案時還是需要mount點的配置與管理，而非僅靠檔名即可
## 檔案存取(File Access)模型+快取(Cache)相關內容
- 兩種檔案存取模型 (File Accessing Models)
	1. Remote Service Model（遠端服務模型）
		- 讀寫都靠server，本地端不會存取副本
		- 優點：client不需大量的儲存空間、不需考慮Cache Consistency
		- 適合：大量寫入作業
		- 早期版本的 NFS 主要以此方式運作
	2. Data Caching (upload/download) Model（資料快取模型）
		- 將檔案快取在本地端，必要時才與server通訊
		- 需要維護 Cache Consistency (快取一致性)
		- 適合：多客戶端情景
- 三種快取位置策略 (Cache Location Policies)
	1. Server Memory Caching（伺服器記憶體快取）
		- 簡單共享
	2. Client Memory Caching（客戶端記憶體快取）
		- 快速暫用
	3. Client Disk Caching（客戶端磁碟快取）
		- 大型快取
- 兩大快取更新策略（Cache Update Policies）(Write 的部分)
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
- 快取驗證策略（Cache Validation Policies）(Read 的部分)
	- 當使用延遲寫入或跨用戶端快取時，不同用戶的快取可能已不同步，驗證策略的目標是確保快取內容與伺服器主檔案一致
	- 兩大類 : client-initiated 和 server-initiated
		- client-initiated : Before every access, Periodic checking, On open (AFS)
		- 重點整理：

| 策略               | 優點       | 缺點         | 代表系統       |
| ---------------- | -------- | ---------- | ---------- |
| Client-Initiated | 設計簡單，負擔小 | 效能差、一致性弱   | 早期 NFS     |
| Server-Initiated | 效能佳、一致性強 | 複雜度高、需記錄狀態 | AFS、NFS v4 |
## 