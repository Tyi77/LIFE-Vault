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
