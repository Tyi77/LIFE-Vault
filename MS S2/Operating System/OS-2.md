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
## SJF Scheduling
- 越短越前面
- 分成non-preemptive和preemptive兩種版本