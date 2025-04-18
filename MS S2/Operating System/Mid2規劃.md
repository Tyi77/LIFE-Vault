**規劃複習計劃**

根據使用者的需求，他們希望依據考古題來製定複習計劃，並專注於考試的內容。為此，我應該根據考古題問題將教學內容分為日程安排，每天集中學習一個主題，先介紹相關概念，然後進行練習。我會根據2023年和2024年的考題，將問題分為多個類別（如多處理器、分散系統、訊息傳遞等），並參考幻燈片中的內容來規劃每一天的學習，並重點整理問答。

已思考 11 秒鐘

以下是一份 **「以 2023 與 2024 兩份考古題為主軸」** 的 7 天密集複習藍圖。  
做法是：先把兩張考卷的題目依「核心概念」重組成 6 大模組；每天集中攻下一個模組，直接讀講義＋練同題型，晚上用紙筆把該模組所有相關考古題完整寫一遍。最後一天整合＋背誦。整週完全不需要「先模擬考」，而是「先學 → 立即寫同題」的安全路線。

---

## 模組對照表（題號 ↔ 觀念）

| 模組                                 | 涵蓋題號（2023 / 2024）                                     | 對應講義（檔名＋頁）                                                                                       |
| ---------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| M1. 多處理器架構 & 拓撲                    | 2023 Q1 Q2 Q5 Q9 Q10；2024 Q1 Q3 Q5 Q9 Q10 Q11 Q12 Q14 | Multiprocessor ppt pp.4–16, 21–26 citeturn0file4Multi‑Computer ppt pp.13–20 citeturn0file3 |
| M2. 同步與鎖 (TSL / Spin / Scheduling) | 2023 Q7 Q13；2024 Q1 Q3 Q8 Q18                         | Multiprocessor ppt pp.27–36, 38–41 citeturn0file4                                             |
| M3. 動態負載平衡 & Processor Allocation  | 2023 Q3 Q11；2024 Q2 Q13                               | Multi‑Computer ppt pp.61–66 citeturn0file3                                                    |
| M4. 分散式系統透明性 & 冗餘                  | 2023 Q4 Q15；2024 Q6 Q12 Q21                           | Distributed Systems ppt pp.6–8, 16–17, 68–70 citeturn0file1                                   |
| M5. 訊息傳遞 & Client‑Server 可靠性       | 2023 Q6 Q8 Q12 Q14；2024 Q8 Q16 Q17 Q18                | MessagePassing ppt pp.8–27, 35–45 citeturn0file2                                              |
| M6. Distributed File Systems       | 2023 Q14；2024 Q19                                     | DFS ppt pp.12, 55–64 citeturn0file0                                                           |

---

## 每日行程（建議每天 4–6 hr，視時間彈性微調）

### Day 1 — M1：多處理器架構 & 拓撲

|時段|任務|
|---|---|
|上午|讀 **UMA / NUMA、Master‑Slave / SMP、Crossbar vs Omega 計算**（ppt 範圍見上）。畫表格寫出：架構差異、優缺點、交換器數量公式。|
|下午|按講義例題自行計算：n = 16, 32 時的 Crossbar 與 Omega 交換器數量、直徑。|
|晚上|手寫 2023 Q1 Q2 Q5 Q9 Q10 + 2024 Q1 Q5 Q9 Q10 Q11 Q12 Q14；核對官方解答，錯題寫「為何錯」一句話備註。|

### Day 2 — M2：同步機制與排程

|時段|任務|
|---|---|
|上午|精讀 **TSL→讀前檢查→Back‑off、Spin vs Block 場景、Affinity & Two‑level Scheduling**。|
|下午|寫一本「關鍵流程筆記」：在紙上畫 TSL + Cache 線路／Back‑off 時序。|
|晚上|完整寫 2023 Q7 Q13、2024 Q1 Q3 Q8 Q18；對答案→錯題記錄。|

### Day 3 — M3：動態負載平衡

|時段|任務|
|---|---|
|上午|讀 **Sender‑Initiated / Receiver‑Initiated 流程 & 性能分析**；再看 Deterministic PA 圖與圖割計算。|
|下午|把 2023 圖割題、2024 圖割題重新計算一遍，換不同搬遷方案試試。|
|晚上|寫 2023 Q3 Q11、2024 Q2 Q13；檢查答案。|

### Day 4 — M4：分散式透明性 & 冗餘

|時段|任務|
|---|---|
|上午|背熟 7 種 Transparency 定義；讀 **三類 Redundancy**。|
|下午|列「透明性 → 真實雲端例子」對照表（至少各 1）。|
|晚上|寫 2023 Q4 Q15、2024 Q6 Q12 Q21；比對解答並補充實際例子。|

### Day 5 — M5：訊息傳遞 & 可靠傳輸

|時段|任務|
|---|---|
|上午|讀 **Blocking vs Non‑blocking、Req‑Ack 協定、Exactly‑Once / At‑Least‑Once、四種通知 Receiver 方法**。|
|下午|練習畫時序：RARA → RRA → RR；並在圖旁標 LostReq / LostResp 處理。|
|晚上|寫 2023 Q6 Q8 Q12 Q14、2024 Q8 Q16 Q17 Q18；標記還不熟的失分點。|

### Day 6 — M6：Distributed File Systems

|時段|任務|
|---|---|
|上午|精讀 **Download/Upload vs Remote‑access、NFS v3 與 AFS/HDFS Data Flow**。|
|下午|用 1 頁筆記畫出 Download‑Model 與 Remote‑Access 時序對比＋適用情境。|
|晚上|寫 2023 Q14、2024 Q19；若答錯，再回講義頁面找到關鍵句並抄寫一次。|

### Day 7 — 整合 & 背誦衝刺

|時段|任務|
|---|---|
|上午|把 M1–M6 錯題整理成「一頁 Cheatsheet」（每題只留關鍵字）。|
|下午|閉卷 60 分鐘口頭講解 Cheatsheet 給自己聽，錄音；聽回放找卡殼處立即補缺。|
|晚上|最終默寫：1️⃣ Transparency 七大定義2️⃣ Crossbar & Omega 交換器公式3️⃣ SI‑LB & RI‑LB 步驟4️⃣ TSL 兩種減輕匯流排方法5️⃣ Download vs Remote‑access 特點|

---

### 使用建議

- **先讀講義再寫同題**：每天都讓「概念 → 考題」緊貼，避免學完忘記考點。
    
- **錯題本**：只抄「題號＋關鍵詞＋為何錯」，方便 Day 7 快速複盤。
    
- **30/5 Interval**：30 分鐘專注讀或寫，5 分鐘伸展，保護專注力。
    

按照此計畫，7 天就能把兩份考卷的所有知識點逐題拆解並反覆操練，直接對應老師最可能出的題型。需要更細的每日資料包或想要 Quizlet 卡片，隨時告訴我！