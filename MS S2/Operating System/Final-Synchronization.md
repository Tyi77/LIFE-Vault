## 分散式同步的核心議題包括：

1. **時鐘同步與事件排序（Clock synchronization & Event ordering）**  
    ➤ 為了讓事件能以一致順序處理，各節點必須對「時間」有共識   
2. **互斥（Mutual exclusion）**  
    ➤ 當多個節點想要存取共享資源時，需確保同一時間只有一個節點能使用該資源   
3. **領導者選舉（Leader Election）**  
    ➤ 當需要集中協調的任務出現時，需從節點中選出一位「領導者」。
4. **死結偵測與處理（Deadlock detection and handling）**  
    ➤ 若程序間形成循環等待資源的情形，需偵測並解除死結   
## Clock Synchronization
- 時鐘的困難：

	- 每台電腦有自己的實體時鐘。    
	- 不同電腦的晶體振盪頻率可能略有不同，導致：    
	    - **Clock skew（時鐘偏移）**：任兩個時鐘的讀數差。        
	    - **Clock drift（時鐘漂移）**：振盪頻率差異導致的累積誤差。
- UTC 是國際標準時間
	- External Synchronization : 跟 UTC 同步
	- Internal Synchronization : 各時鐘彼此同步
	- 上面兩個都有一個誤差 boundary `D` ，同步的概念是對這個誤差interval對齊，而不是真正的完全精準對齊
	- 注意事項
		- 若外部同步成立 → 一定也內部同步    
		- 但內部同步不代表與 UTC 同步，可能整體一起偏移
- 完美同步 UTC : $C_p​(t)=t$ 且 $\cfrac{dC}{dt}​=1$  (C 是時鐘值，t 是 UTC 的值)
	- 為確保任意兩時鐘之差不超過誤差上限 D，需每隔：$$\frac{D}{2\rho}$$秒重新同步。 ($\rho$ 是所有時鐘的最大漂移率 (drifting rate))
## Clock Synchronization Algorithms（時鐘同步演算法）

> p7-15
- 分為兩大類：

	1. **集中式演算法（Centralized Algorithms）**  
	    - Cristian's Algorithm（1989）      
	    - Berkeley Algorithm（1989）      
	2. **分散式演算法（Decentralized Algorithms）**  
	    - 平均法（Averaging Algorithms，例如 NTP）
- The Cristian's Algorithm
	- 流程
		- 假設：有一台主機 S 擁有可接收 UTC 的能力   
		- 其他主機需每隔 $\cfrac{D}{2\rho}$ 秒向 S 發送同步請求
		- S 回覆其目前的 UTC 時間 $C_{\text{UTC}}$
	- 客戶端(Client)根據來回延遲估算當前時間：$$C_{\text{UTC}} + \cfrac{t_1 - t_0 - I}{2}​$$
		- $t_0$​：發出請求時間		    
		- $t_1​$：接收回應時間		    
		- $I$：S 的處理延遲，可一起回傳​
	- 問題：

		- 若客戶端的時鐘比 UTC 快，則會需要「倒轉時鐘」，對某些應用不允許
		- 解法是「減慢」時鐘速度，而不是倒退
- The Berkeley Algorithm
	- 沒有 UTC 接收裝置   
	- 指定某主機為**時間守護者（time daemon）**：    
		1. 向其他主機詢問時鐘偏差（skew）。        
		2. 收到所有偏差後，計算平均值。        
		3. 要求所有主機根據平均偏差調整時鐘。
- The Network Time Protocol （NTP）
	- Cristian 和 Berkeley 的方法適用於小型網路。    
	- **NTP** 是 Internet 上使用的標準協定，由許多層級的伺服器組成。    

	- 階層結構
		- Stratum 1：連接 UTC 源的主伺服器（Primary servers）    
		- Stratum 2：與 Stratum 1 同步的次伺服器    
		- Stratum 3：與 Stratum 2 同步的伺服器…依此類推    
		- 每層同步會引入一定誤差
	- 三種同步模式
		1. **Multicast mode（多播模式）**    
		    - 適用於區域網路（LAN）        
		    - 一台伺服器定時廣播時間訊息        
		    - 準確度較低，但適用於非關鍵應用        
		2. **Procedure-call mode（過程呼叫模式）**    
		    - 類似 Cristian's 方法        
		    - 伺服器回應請求者的時間查詢        
		    - 準確度比 multicast 高
		3. **Symmetric mode（對等模式）**：用於較低層級伺服器(lower strata)之間的高精度同步

			- 伺服器彼此交換時間訊息    
	- 使用不可靠傳輸（如 UDP）    
	- procedure-call 和 symmetric mode 中，process 會互相交換資料。每次同步使用一對訊息，計算：    
		- **偏移量 $o_{ij}$​**：兩時鐘的時間差估計        
		- **傳輸延遲 $d_{ij}$​**：兩訊息的總往返時間
	- 有一段計算兩個 servers 間的 clock skew ( $S$ ) 的公式，但我有點看不懂 $$S_{AB}​−\cfrac{d_{AB}}{2}​​≤S≤S_{AB}​+\cfrac{d_{AB}}{2}​​​​$$
		- 若以 $S_{AB}$​ 為 estimated skew，則 $d_{AB}$​ 為其誤差界
		- A 和 B 為兩個 servers
## Event Ordering 與 Logical Clocks（事件排序與邏輯時鐘）
> p.16-23。涵蓋 Lamport 時鐘與 Vector Clock 的概念、演算法與性質
- 基本觀念
	- 在單一電腦中，可以依據**本地實體時鐘**對事件排序。    
	- 但在分散式系統中，無法保證所有節點時鐘完全同步 → 很難使用實體時間排序。    
	- 好消息是有兩個**簡單且明確的事實**可以用於排序：    
	    1. 若兩事件在同一個 process $p_i​$ 發生，則可依照 $p_i​$ 見到的順序排序。 
	    2. 若一個事件是訊息的發送，另一事件是接收該訊息，那麼「發送」發生在「接收」之前。
### Lambert's Logical Clock
- **Happened Before Relation**（發生先後關係）(以 $→$ 做記號)
	- **同個process下**，若事件 e 在 事件 e' 之前，就可記為 $e→e′$
	- $\text{send}(m) \rightarrow \text{receive}(m)$
	- 傳遞性：若 $e \rightarrow e'$ 且 $e' \rightarrow e''$，則 $e \rightarrow e''$
- Logical Clock $L(e)$
	- 符號
		- $L_i(e)$ 是事件 e 在 process i 的 timestamp
		- $L(e)$ 是事件 e 的總 timestamp
	- 更新規則
		- LC1：每當 process $p_i$ 發生事件前，$L_i \leftarrow L_i + 1$
		- LC2a：發送訊息時，附上當下 $L_i$    
		- LC2b：收到訊息 $(m, t_m)$ 時：$$ L_j \leftarrow \max(L_j, t_m) + 1$$
	- 性質：$$若 e→e' ⇒L(e)<L(e')$$
		- 但**反過來不成立**。所以你無法從 LC 得出事件的先後順序
- Global logical clocks (GLC)
	- logical clocks 只是在單個process的中的數值，不同的processes你無法從各別的 LC 來判斷event的先後順序
	- 因此引入 GLC ，混合 LC 和其 process ID : $GLC(e)=(L_i​(e),i)$
	- $$(L_i​(e),i)<(L_j​(f),j)⇔L_i​(e)<L_j​(f)\ 或\ [L_i​(e)=L_j​(f) 且 i<j]$$
- 圖例![[Pasted image 20250603200627.png]]
### Vector Clock
- 事件 e 的 vector clock 記做 : $VC(e)$
- 性質
	- 若 $e\rightarrow e'$，則 $VC(e) < VC(e')$
	- 若 $VC(e) < VC(e')$，則 $e\rightarrow e'$
	- 若無法比較（兩邊都不小於），則 $e\parallel e'$
- 定義
	- $V_i$ 為第 i 個 process 的 Vector Clock，裡面有 N 個 integers 
	- N 代表有多少個 processes
	- $V_i​[j]$：$p_i$​ 目前知道的第 $j$ 個 process 累積的事件數量
- 更新規則
	1. 只要有發生event，不管是內部處理、發送訊息或接收訊息，都先在 $V_i[i]$ 中加1 ( $V_i[i] ← V_i[i] + 1$ )
	2. 內部處理：做完第一步就結束
	3. 發送訊息：做完第一步後，將整個 $V_i$ 一併 message 發送給目標 process
	4. 接收訊息：做完第一步後，將收到的 $V_j$ 與自己的 $V_i$ 做比較，不同欄位個別取 `max的值` 更新數值
- 圖例![[Pasted image 20250603200136.png]]
### 兩個clock的差異
- logical clock 只有單一數值，vector clock 是一個 vector
- logical clock 的絕對數值只在單一process中有效，多個process的數值有可能數值一樣但先後順序不同
- vector clock 能確保多個 process 下，其絕對數值能代表事件真正的先後順序
## Distributed Mutual Exclusion（分散式互斥，簡稱 DME）
> p.24-39。涵蓋多種實作互斥的演算法（中心式、環狀式、Ricart、Maekawa 等）
> p.40-49。涵蓋 Suzuki-Kasami、Raymond 以及其優化版本（如 path compression）
- 基本觀念
	- 在分散式系統中，當多個程序共享資源時，需透過**互斥機制**防止同時存取
	- 一個有效的 DME 演算法應該只依賴**訊息傳遞（message passing）**
	- 那個被共享的區域稱為 Critical Session (CS)
- 假設系統為
	- 非同步（asynchronous）	    
	- 無故障（failure-free）	    
	- 訊息傳送可靠（reliable）
- 基本API
	- enter();     // 嘗試進入 CS
	- resourceAccesses();  // 在 CS 中存取共享資源
	- exit();       // 離開 CS
- DME 三大正確性需求：
	1. **ME1（Safety）**：任何時刻，最多只能有一個 process 在 CS 中。	    
	2. **ME2（Liveness）**：請求進入 CS 最終都會成功（避免 deadlock 與 starvation）。	    
	3. **ME3（HB ordering）**：應依據 request 發生先後順序（→）進入 CS。
### A Central Server DME Algorithm![[Pasted image 20250603201920.png]]
- 使用一個中央伺服器來管理誰可以進入 CS。 
- 操作流程：
	- 進入 CS：需傳送 **request + grant**（共 2 封訊息）   
	- 離開 CS：傳送 **release**（共 1 封訊息）
- 符合 ME1 和 2 ，但 3 沒有
### A Ring-Based DME Algorithm
  ![[Pasted image 20250603201931.png]]
- 各節點組成一個邏輯環（logical ring），透過傳遞「token」來控制 CS 的進入
### The Ricart and Agrawala's DME Algorithm
- 規則簡述：

	1. 每當 process 想進入 CS，就**向所有其他 process 發出請求**
		- 使用 GLC 的格式 $<T_i,\ i>$
	2. 若其他 process 沒有在 CS，會立即回覆。		    
	3. 若自己正在 CS，或有更早（更小 timestamp）的請求 → 不回覆，將對方請求排隊。		    
	4. 當 process 收到來自所有其他節點的回覆（**共 $N-1$ 封**），即可進入 CS。
- 程式實踐
	- process 會有三個 states : `RELEASED`, `WANTED`, `IN-CS`
	- 初始化：`RELEASED`
	- 想進入 CS
		- 設定`WANTED`，並發送進入請求給所有的 process
		- 等待 N-1 個回覆
		- 收完回覆後，設定`IN-CS`，並開始處理CS內容
	- 想離開 CS
		- 設定`RELEASED`
		- 並處理所有在 queue 中的 requests
	- 收到有人想進入 CS
		- 檢查自身的 state
			- `RELEASED` 就馬上回覆
			- `IN-CS` 將 request 放到 queue 中
			- `WANTED` 
				- 自身有比他人更早的 request，收到 queue 中
				- 沒有更早的 request，直接回覆
- 進入 CS 需要的訊息數量：**2(N−1)**    
	- N-1 封 request + N-1 封 reply        
	- 離開 CS 不需額外訊息    
- 三項互斥需求（ME1~ME3）全部滿足
### Maekawa’s Algorithm
- 想法
	- Maekawa 指出：**不需要所有節點同意，只要夠多就好**    
	- 每個節點只需向其**投票集合（voting set）中的節點請求進入 CS**
- 基本觀念
	- 每個節點僅對一個請求投票（grant）。**每人只持有一張票** (`voted`)    
	- 一個節點若獲得足夠票數（例如多數）即可進入 CS。    
	- 傳統多數投票：最少為 K 票
		- K 至少 $1 + \cfrac{N}{2}$​ ，可能為 $\sqrt N$
- 訊息開銷：
	- 請求進入 CS：需送出 $1 + \frac{N}{2} \sim N$ 封 request
	- 需要至少 $1 + \frac{N}{2}$ 封 grant 才能進入    
	- 離開 CS：需發送 K 個 release    
	- 若死結/飢餓發生 → 需額外訊息：revoke, regrant
- 程式碼要看 ...
- Deadlocks in Maekawa's Algorithm
	- 因為一人只能一票，所以有機會造成彼此互相等待的deadlock狀況 (環狀等待)
- 改良後 Maekawa 的範例結構
	- 加入時間戳(timestamp)的概念，越早優先度越高
	- 引入新概念。收到請求時，會將新的請求與舊的請求做比較，會嘗試讓較早的請求勝出
		- 收到請求：新 > 舊 - `FAILED` ，新 < 舊 - `INQUIRED`
			- `FAILED` : 若收到一個較晚的 request，而票已投給較早的 request，則立即回傳 `FAILED`
			- `INQUIRE` : 若某節點（例如 $P_2$​）已投票給 $P_2$​，但又收到較早的請求（例如 $T_1 = 1$​ 的 $P_1$​），則可向 $P_2$​ 發 `INQUIRE`，問他是否願意釋票
		- `YIELD` : 若 P_2​ 收到 `INQUIRE`，發現自己收到過 `FAILED`（知道自己無法勝出），就會發送 `YIELD` → 把票還回去

	- 結論：投票會「流向」時間戳最小者，進而打破環狀等待
### Token-Based DME Algorithms
- 基本觀念
	- Ricart-Agrawala 或 Maekawa 票選法需要大量的訊息傳遞，會太慢
	- 核心：全域只維護一個「Token」（權杖），僅持有該權杖的節點可進入臨界區（Critical Section, CS）
	- 進出CS所需訊息數量：N-1次請求，1次Token傳遞
- #### Suzuki-Kasami 全域 Token 傳遞演算法
	- 單一 Token 全域存在
		- 系統共有 N 個節點，其中只有其中一節點一開始持有 Token，其他節點則在需要進入 CS 時向全域廣播請求
	- **核心要點與正確性**

		- **全域唯一性**：因為整個系統中只存在一個 Token，所以「同一時刻」只能有一個節點進入 CS，可確保互斥性（Mutual Exclusion）。	    
		- **有界等待（No Starvation）**：由於每次退出 CS 時，會將符合同樣請求序號條件的等候節點依 FIFO 先後放進佇列，因此一旦 pj 發出請求，其請求序號會隨著 `RN[j]` 增長，最終必然被 `LN[j] + 1` 滿足，並被加入 `Q`，而佇列頭節點終將被 Token 傳遞，故不會無限期等待。	    
		- **訊息複雜度**：每次進入 CS 時最多產生：		    
		    - N–1 條 request 訊息（向所有其他節點廣播）。		        
		    - 1 條 token 傳遞（把 Token 從某節點送給請求者）。	        
		    - 因此**總共 N 條**訊息，為目前分散式互斥下的最優上界。	        
		- **維護資料結構的成本**：每個節點須維護 RN、LN 向量，各為大小 N；而 Token 本身攜帶 LN 與 Q。因此在節點數 N 很大時，向量更新與 Token 資料搬遷的開銷（帶寬與儲存）會成為瓶頸
- #### Raymond 的最小生成樹（Tree-Based）演算法
	- fdk
### 統整 - 所需message的數量

| 演算法                                 | 所需訊息數量            | 參數說明                                                                    |
| ----------------------------------- | ----------------- | ----------------------------------------------------------------------- |
| **中央化 (Centralized)**               | 3                 | 每次 1 封 Request → 1 封 Reply → 1 封 Release                                |
| **Lamport’s (Timestamp)**           | 3 × (N−1)         | 廣播 Request (N−1) + 收 N−1 個 ACK + 廣播 Release (N−1)                       |
| **Ricart–Agrawala**                 | 2 × (N−1)         | 廣播 Request (N−1) + 收 N−1 個 Reply                                        |
| **Maekawa’s 投票 (Voting)**           | 3 × K             | 向 K 個投票者 Broadcast Request + 收 K 個 Vote + Broadcast Release → K ；K ≈ √N |
| **Token Ring**                      | 最壞 N−1；平均 (N−1)/2 | Token 在環中單向傳遞；最壞需經 N−1 次，平均約 (N−1)/2 次                                  |
| **Suzuki–Kasami (Broadcast Token)** | N                 | 廣播 REQUEST 給 N−1 + 傳送 Token (1)                                         |
| **Raymond’s (Tree-based)**          | 2 × H             | 向上 H 次請求 + 向下 H 次傳回 Token；H 為樹高 (O(log N))                              |
## Leader Election
- 領導者
	- 集中化協調需求：有主責好辦事
	- 容錯與重選機制：當原版的領導失效了，需要能盡快選新領導
		- 透過選舉選出新領導 (Election)
		- Bully Algorithm 和 Ring Algorithm 是兩種選出新領導的演算法
		- 優先權越高(數值越大)，越有機會成為領導者
- Bully Algorithm
	- 流程
		- 舊領導者在時限 T 前未回應，觸發 Election
		- 送出 `election()`
			- 給權限比自己高的node，看看是否會有回應
		- 接收 `election()`
			- 若還為觸發 Election，則回應 Okay，並立刻觸發自己的 Election
		- 宣布領導者
			- 若節點 Z 發現自己向所有更高編號節點發送後都收不到回應，就在內部確定自己當新領導者，然後廣播「`coordinator(Z)`」給所有節點。收到這則訊息後，所有節點都更新當前領導者為 Z
	- 通訊成本
		- 最壞：O($N^2$)，因為每人都觸發election
		- 最佳：O($N$)，因為最高編號偵測到領導者失效，沒有更高的節點需要通知，直接就可以廣播自己當選。
	- 優缺點
		- 優點：簡單
		- 缺點：節點數量多時，可能需要非常多的訊息
- Ring Algorithm
	- 流程
		- 所有的nodes變成環狀，只能發送訊息給下個node
		- 偵測到領導者失效的node，將自己的編號放入 AC (Active List)中，並傳遞 AC
		- 拿到 AC 的 node 就把自己的編號放入 AC 並繼續傳遞 AC
		- 等到 AC 傳回到起始 node 時，停止傳遞 AC ，並在 AC 中找尋最大的編號成為新的領導者，最後廣播給大家知道
	- 通訊成本
		- 傳遞 AC + 廣播：共 2N
		- 若有 K 個節點偵測到失效：則為 2kN
	- 優缺點
		- 優點：簡單
		- 缺點：若環狀結構需要維護
## Deadlock
- 要在 DS 中形成死結，必須同時具備以下四項條件
	- **互斥(Mutual Exclusion)**：資源只有一份，且不能同時由多個程序共用  
	- **保持並等待(Hold and Wait)**：某程序已經持有資源 A，卻又想取得其他資源 B，因此同時「持有 A、等待 B  」
	- **不可剝奪(No Preemption)**：一旦程序取得了某資源，就不能被強行奪走；資源只能由程序自己釋放  
	- **環狀等待(Circular Wait)**：存在一個環形鏈條 P₁→P₂→…→Pₖ→P₁，前者等待後者持有的資源，最終形成閉環
- 處理 Deadlock 的三大策略
	- 檢測與復原 (Detection & Recovery)
		- 放任 Deadlock 發生
	- 預防（Prevention）
		- 打破四項條件中的其中一項，確保絕對不會發生 deadlock
		- 例如：使用 linear ordering (線性排序)
		- 缺點：需要額外資源去維護這些東西
	- 避免（Avoidance）
		- 每次分配資源時，都先查看是否有可能會造成deadlock
		- 缺點：需要不斷查詢「安全狀態（safe state）」的演算法（例如銀行家演算法），在多主機環境中會衍生更高的通訊成本與演算量
- Central Server Deadlock Detection
	- 兩個層級的 WFG (Wait-For Graph)
		- LWFG : 紀錄local的等待圖
		- GWFG : 講所有LWFG整合起來的等待圖
	- 檢查 GWFG 是否有 cycle，有的話就是 deadlock
		- 小問題：中央伺服器若只看單一時刻的 GWFG 而不考慮訊息傳遞的時序，就容易**誤判假死結（false deadlock）**
			- 就明明有資源被釋放了，但此對應的edge沒在GWFG上被斷掉，而造成誤判
- Fully Distributed Deadlock Detection
	- 沒有中央(GWFG)的概念了，而是透過每個node維護其各自的 AWFG (Augmented WFG)來偵測 Deadlock
	- AWFG 建造
		- 先畫出 LWFG
		- 若local的process有連到外面的node，則引入虛擬外部節點( $P_{ex}$ ) 表示所有的外部process，並對其建立 edge 表示連線
	- AWFG 所統整的資訊會互相傳遞，只傳遞包含 $P_{ex}$ 的 cycle
	- 若 cycle 是不包含 $P_{ex}$ 的，直接廣播 deadlock 開始處理