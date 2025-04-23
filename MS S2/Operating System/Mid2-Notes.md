## MultiProcessors
- The Directory Cache Coherence Protocol (PP. 16-17)
	- NUMA的架構，會使用directroy-based的cache coherence架構
	- directory
		- 列出一起共用此區塊資料的CPU編號 (Shared List)
		- Dirty bit
	- 每當Load時都會去確認自己的directory是否有，沒的話去確認別人的directory再Load進來。若有但dirty bit為Invalid，則去找Modified的CPU並把資料讀入。
	- Write時，先公告所有在shared List上的CPU，讓其他人的directory的dirty bit都變成invalid，最後再更新自己的Directory，並設dirty bit為Modified
- MP Scheduling
	- Single Shared Ready List
		- 當有CPU idle時，就去ready list把最高的process引入進來
		- 缺點
			- CPU間會搶process
			- 沒考慮cache affinity的問題，會導致本來有被cache住的資料沒被使用到，會一直cache miss
			- shared ready list 會產生 lock contention的問題
	- Affinity Scheduling
		- Two-level Scheduling
			- 上層：一次性將「一批」相關連的process分派到相對應的CPU中
			- 下層：每個CPU有自己的Ready List，不用再去跟其他CPU搶佔Shared Ready List的使用權(Lock)。等自己的Ready List都空了之後，才會向上層要process或跟其他CPU借process
		- 優點：解決Single Shared Ready List 的缺點
## MultiComputers
- 平衡nodes之間的運算效能的方法 
	- Sender Initiated Load Balancing Algorithm
		- 當自己overload時，就probe出去跟其他node說他需要幫忙，然後under loaded 的 CPU 就會來接此 process
	-  Receiver Initiated Load Balancing Algorithm
		- 當自己 under loaded 時，就 probe 出去跟大家可以把process給他，然後就會有overloaded的CPU把process丟給他
	- 比較兩者
		- 在高負載的狀況下，前者會出錯後者不會
		- 
## DistributedSystem
- Design Issues of a DS
	-  Transparency
		- Provide a single system image to users
	- Scalability:
		- **Size scalability**: number of users and/or processors
		- **Geographical scalability**: distance between nodes
		- **Administrative scalability**: number of administrative domains
	- Fault Tolerance
## MessagePassing
- blocking vs non-blocking call
	- blocking
		- 優點：簡單實踐
		- 缺點：可能會產生communication deadlock，因為如果中間訊息掉包，sender或receiver會被一直卡住
	- non-blocking
		- 優點：處理完訊息，可以繼續處理接下來的任務
		- 缺點：會有額外的overhead來確認訊息是否傳遞或接收成功
## Distributed File System
- AFS vs NFS