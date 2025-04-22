## MultiProcessors
- The Directory Cache Coherence Protocol (PP. 16-17)
	- NUMA的架構，會使用directroy-based的cache coherence架構
	- directory
		- 列出一起共用此區塊資料的CPU編號 (Shared List)
		- Dirty bit
	- 每當Load時都會去確認自己的directory是否有，沒的話去確認別人的directory再Load進來。若有但dirty bit為Invalid，則去找Modified的CPU並把資料讀入。
	- Write時，先公告所有在shared List上的CPU，讓其他人的directory的dirty bit都變成invalid，最後再更新自己的Directory，並設dirty bit為Modified
## DistributedSystem
- Design Issues of a DS
	-  Transparency
		- Provide a single system image to users
	- Scalability:
		- **Size scalability**: number of users and/or processors
		- **Geographical scalability**: distance between nodes
		- **Administrative scalability**: number of administrative domains
	- Fault Tolerance