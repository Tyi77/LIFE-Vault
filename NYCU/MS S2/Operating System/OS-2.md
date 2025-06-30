## Dispatcher
- 真實的去執行scheduler所要的context switch
- Dispatch latency
## Scheduling Criteria
- CPU utilization: CPU被用了多少
- Throughput: 每單元時間所能執行的thread數量
- Turnaround time: 一個thread所需執行的時間
- Waiting time: 在 ready queue 中等待的時長
- Response time: 從接收任務到回傳回應中間的時間
- Time slice: 被中斷前的最長執行時間
## FCFS Scheduling
- average waiting time: thread執行起點-thread到達時間，取平均。
- Convoy effect : A short process may be slow down by a long process
	- 所以越短的在越前面越好
## SJF/SRTF Scheduling
- 越短越前面
- 分成non-preemptive和preemptive兩種版本
	- preemptive 又叫 SRTF (Shortest Remaining Time First)
![[Pasted image 20250311222448.png]]
## RR Scheduler
- 每個執行緒輪流執行，每次執行只有q時間長，超過就切掉換下一個執行緒
- q太長: 變成FCFS
- q太短: context switch 的 overhead 會太多
## Priority Scheduling
- 如果優先度是定義在時間長的話，那SFJ就是其中一種
- 問題：Starvation: 低優先度一直無法執行
	- 解法：Aging: 照著時間長啼聲優先度
## Multilevel Queues
- foreground(RR), background(FCFS)
- Fixed Priority: 所以還是有可能造成starvation
- CPU時間：80%foreground, 20%background
## Real-Time Systems
- hard real-time and soft real-time
- 通常使用 priority schedulers
- Earliest Deadline First (EDF) vs Least Slack First (LSF)
## Logical vs Physical Address Space
- MMU scheme