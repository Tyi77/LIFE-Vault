## 📌 分散式同步的核心議題包括：

1. **時鐘同步與事件排序（Clock synchronization & Event ordering）**  
    ➤ 為了讓事件能以一致順序處理，各節點必須對「時間」有共識   
2. **互斥（Mutual exclusion）**  
    ➤ 當多個節點想要存取共享資源時，需確保同一時間只有一個節點能使用該資源   
3. **死結偵測與處理（Deadlock detection and handling）**  
    ➤ 若程序間形成循環等待資源的情形，需偵測並解除死結   
4. **領導者選舉（Leader Election）**  
    ➤ 當需要集中協調的任務出現時，需從節點中選出一位「領導者」。
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
	- 沒有 UTC 接收裝置。    
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
	- 