## MultiProcessors
- The Directory Cache Coherence Protocol (PP. 16-17)
	- NUMA的架構，會使用directroy-based的cache coherence架構
	- directory
		- 列出一起共用此區塊資料的CPU編號 (Shared List)
		- Dirty bit
	- 每當Load時都會去確認自己的directory是否有，沒的話去確認別人的directory再Load進來。若dirty bit為Invalid，則去找Modified的dirty bit
	- Write時，先公告所有在shared List上的CPU，讓其他人的directory的dirty bit都變成invalid，最後再更新自己的Directory，並設dirty bit為Modified