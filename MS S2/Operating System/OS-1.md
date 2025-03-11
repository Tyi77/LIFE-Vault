## 2nd video
- Boot loader
	- chain loader
		- BIOS(basic input/output system)
		- UEFI(unified extensible firmware interface)
- Upon loading the OS, the boot loader transfers control to the OS
# Read
## Policy vs Mechanism
- Mechanism 說明如何實踐
- Policy 說明實踐中需要注意的事項，或達成的目標
- 好的OS應該要把這兩個東西分開撰寫
## Boot Loader
- Boot Loader 執行完後，才會接手給OS
- Cascaded Boot Sequence: Chain Loading
	- BIOS
	- UEFI
## System Call
- trap = system interrupt
## Timer Interrupt
- 定期的interrupt，目的是給OS操控權以更好的管理process
## Context Switch
- 停下一個process並記住執行的所有內容，並切換使用權給另一個process
##  Devices
- Three types: Character/Block/Network
- 確認裝置可以溝通
	- Polling: 定期clock interrupt時確認
	- Interrupts: device 自己發出，會有context switch 的 cost
## Moving Data to/from Devices
- Programmed I/O (PIO) vs Direct Memory Access (DMA)
## Memory Map
![[Pasted image 20250311190634.png]]
- Text, Data(static, global), Bss(uninitialized static), Heap(dynamically), Stack(subroutine call stack).
- data 跟 bss 的差別只在是否有初始化
- local variable 看是否有動態配置，有就是heap，沒就是stack
## Process States
![[Pasted image 20250311200803.png]]
## Scheduler and PCB
- preemptive multitasking system: 可搶佔，running <-> ready
- PCB: 記住一個process中的所有資訊
	- Process ID
	- Machine state
	- process state
	- memory map
	- ...
## Process Management in POSIX
- Fork(create), Execve(load), Exit(terminate), Wait(), Signal(), Kill()
- Parent & Child processes
![[Pasted image 20250311202112.png]]
## Thread
- A thread-aware OS keeps track of processes via a list of PCBs.
- Each PCB keeps a list of ==thread control blocks (TCBs)== for that process.
- thread共有和各自的東西
	- 共有：code, data, bss, files區塊
	- 各自：registers, stack區塊

| **類型**        | **Kernel-Level Thread（內核層執行緒）** | **User-Level Thread（使用者層執行緒）** |
| ------------- | ------------------------------- | ------------------------------ |
| **管理方式**      | 由 OS 內核管理                       | 由執行緒函式庫管理                      |
| **執行緒切換成本**   | 高（需內核介入）                        | 低（不需內核介入）                      |
| **多核 CPU 支援** | 是，可真正並行                         | 否，無法利用多核 CPU                   |
| **執行緒阻塞影響**   | **單一執行緒阻塞不影響其他執行緒**             | **單一執行緒阻塞會影響整個 Process**       |
| **應用場景**      | 現代作業系統的標準執行緒管理                  | 嵌入式系統、需要低開銷的應用程式               |
| **比例**        | 1:1                             | M:1                            |
**🔹 Kernel-Level Thread 適合現代作業系統，因為它支援多核 CPU 和並行運行。**  
**🔹 User-Level Thread 適合輕量級應用，但無法充分利用多核心 CPU。**

🚀 **現代作業系統（如 Linux）大多使用 Kernel-Level Thread，並支援 M:N 模型來整合 User-Level Thread 的優勢。**
## Concurrence
- race condition
	- Cooperating threads may access shared data simultaneously.
	- 造成race condition的程式區段叫做 critical sections(CS)
	- 把CS lock 住的話，就可以避免race condition
		- acquire(), release()
- Spin Lock & priority inversion
	- spin lock: thread一直等待CS區段執行完畢，會占用CPU資源
	- priority inversion: 低優先度thread先持有鎖，高優先度thread碰到此區段時會進入spinning狀態以等待鎖的釋放，但spinning會占用CPU資源，因此反而導致低優先度thread無法執行完畢釋放鎖。
	- Priority Inheritance: 碰到priority inversion時，提高低優先度thread的優先度跟高優先度thread一樣，以讓此thread優先執行完畢並釋放鎖。
- Semaphores
	- wait(), signal()
	- mutex 就是 binary semaphores，唯有一個thread可以執行CS
	- semaphores本身是可以操控可以執行CS的thread數量
	- 防止busy waiting
		- 當資源不可用時，讓執行緒進入「等待隊列」並進入睡眠狀態（Block）。
		- 當資源變為可用時，喚醒（Wake up）等待的執行緒。