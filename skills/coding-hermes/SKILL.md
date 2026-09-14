---
name: coding-hermes
version: 2.2.0
author: royalskynet (Ether)
description: "複雜跨檔開發、外部整合或重複失敗時的查證與停損流程。一般 coding、小修、純配置或已有專用 workflow 時不使用。"
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [coding, debugging, fixindex, discipline, stop-loss, glue-coding, architecture, kanban, self-sufficient]
    related_skills: [systematic-debugging, hermes-agent, kanban-worker]
---

# coding-hermes — coding agent 行為紀律

> 來源：royalskynet/coding-hermes 的行為紀律。目的:讓 coding/debug 代理減少反覆與人工介入。

**與宿主全域規則的分工**:本 skill 只放**方法論**;開工查舊帳、反事實閘門、盲試/修正輪次數閾值由宿主全域規則定義,宿主未定義時採本文通用判準。

## When to Use

**複雜跨檔開發、陌生外部整合、連續失敗需重估、問題越查越多需決定哪些不做** → 載入。小修、純配置、已有專用 workflow、純問答、資料查詢不觸發。

## 動手前

- 明說假設;不確定先查(fixindex/官方文檔),仍不確定標 [Assumption] 續做
- 寫最小可解問題的代碼,不寫猜測功能;只改必要,不順手重構;先定驗收再動手
- **按風險研究**:新增相依/陌生 API/外部整合/根因不明的重複失敗,先搜成熟方案並讀官方文檔;小改免
- **服務驗證**:啟動/重啟服務後用 `lsof -i:<port>`/`netstat -tlnp` 確認監聽預期端口再驗收
- **計畫列『根治』與『預防加固』兩段**——根治=找根因修掉;預防加固=hook/測試/guard 擇一落地防復發。缺一不宣告完成

## 狀態盤點先於考古

接手續作或收完成報告時,先盤點**當前實際狀態**(版控/runbook/服務健康),不靠複述當事實。**報告是宣稱,狀態是事實,衝突以狀態為準。**

## 膠水思維

能抄不寫,能連不造,能復用不原創:不發明先搜已驗證方案;只做膠水;先結構後代碼;奧卡姆剃刀;可證偽優先;官方文檔優先;社群回饋備援。

## 架構原則

不留向下相容;分層成長(最小可運行起疊加);模組化(元件邊界清楚);為長期做決策(拒臨時方案)。

## 三層行為紀律(核心)

| 層 | 觸發 | 動作 |
|---|---|---|
| 1. 觸發時查舊帳 | T1 由 claude-mem 注入;新實體/破壞性/枚舉/連兩次意外由 hook 觸發,無命中才手動 | 查修理日誌(`fixindex find "<症狀>"`)與 session 歷史;小改免 |
| 2. 找輪子與查證 | ① 新相依/陌生 API/外部整合/寫超膠水量(>50 行)前;② 同題連敗 2 次 | 官方文檔與 `gh search`(repos/code/issues)並行,有套件就用不續修自製;小改免 |
| 3. 三線失敗後記錄阻斷 | 已試 3 種不同方案仍失敗 | `fixindex fi` 記「未修+診斷+下一步」,停手求助,不盲第 4 條 |

**Layer 2 指令範本/判準/違反特徵全文 → [references/research-playbook.md](references/research-playbook.md)**。判準:先找人解過再自己想;違反特徵:一步一發現、靠 error message 往回推。

## 停損(全文 [references/stop-loss.md](references/stop-loss.md))

兩個軸不要混用:**重試軸**=同題要不要續試;**範圍軸**=該不該現在修。卡住用重試軸,清單變長用範圍軸。具體次數閾值由宿主定義。

**2×2(影響面×是否靜默):靜默失敗優先修,會噴錯的能等。**

**三條硬性線:**一次只推進一層,有依賴不准並行;**第三層就停手寫交接文件,不直接修**;新發現超 5 條就開新文件。

## 完工寫入 fixindex(只在修好 defect 時)

完整指令/禁則/寫入驗證/跳脫地獄 → [references/fixindex-usage.md](references/fixindex-usage.md);維護 agent 系統 → [references/agent-tools.md](references/agent-tools.md)。

修好**一個 defect** 才記(階段完成/任務交付/session 收工不算)。**判準**:寫不出別人搜得到的 `Symptom` → 是進度報告非條目。**寫入驗證**:不得用 `find` 命中當成功;唯一判準是 `fixindex fi` 回傳 JSON(位址+`committed` SHA+`pushed: true`+`git_error: null`)。重大未解診斷可記「not fixed」,一般失敗不寫。

## 驗收紀律(全文 [references/acceptance.md](references/acceptance.md))

- **驗收樣本含一條會紅的判決**(Judge/Guard 類):全 PASS 恐是假警報
- **貼原始輸出**,節錄附產生指令並標「節錄,共 N 行」
- **工具回報也是宣稱**(正負兩向):正面先疑偵測器過期(0599);負面先疑探測姿勢(0575)
- **訊號收集器先看內容**:前 N 筆逐筆看過再接下游(0631)
- **重大改動出**一題**測驗**,答對即過;小修免考

## 代理協作紀律

Judgment Calls Only(模型管分類/草稿/摘要,代碼管路由/重試/轉換);Surface Conflicts(衝突擇一標記);Read Before You Write;Tests Verify Intent(測「為何」非「做了啥」);Checkpoints(每步摘要,失邏輯即停重述);Convention Beats Novelty;Fail Loud(不確定 100% 成功必明說)。

## 長期任務自給自足

檢查點一律用 state.json——檔名/schema/時機見 `~/.claude/shared/state-file.md`(每 iteration 邊界先覆寫再續跑)。

## 從回饋學到的

> 本段由 improver 維護:讀人類回饋一次提一條、開 PR,人工 merge 後才生效。marker 以外視為 CORE。

<!-- LEARNED:BEGIN -->
<!-- LEARNED:END -->

## 指令構造（agent 構造指令當下不觸發檢索）

召回綁在 user prompt,agent 自造指令時零注入。細節/追蹤(fixindex 0645) → [references/command-construction.md](references/command-construction.md):

1. 判斷「有沒有某類輸出」用 `wc -l`/`grep -c` 數全量,不用 `head` 當全貌
2. `mv` 前確認目標目錄是否已存在,`mv` 後用 `find`/`ls` 驗證最終位置與層數
3. push 後比 `git log origin/<branch>..HEAD` 剩餘 commit 數或 `git status -sb` 看 ahead 數,不靠「沒報錯」當成功

## 收工自檢清單（重大改動限定；小修免）

- [ ] 跨 session 或重複故障,已查相關舊帳
- [ ] 陌生整合或連敗 2 次,已依 Layer 2 查證
- [ ] 卡住用重試軸、清單變長用範圍軸,沒混用
- [ ] 完工後有 `fixindex fi` 記錄(除非無 defect)
- [ ] 寫入有貼 `fixindex fi` 回傳,不是拿 `find` 命中充數
- [ ] Judge/Guard 驗收樣本含一條會紅的判決
- [ ] 依健檢/掃描工具回報做事前,有抽驗命中非誤報(含自寫腳本)
- [ ] 新訊號收集器/分類器,已逐筆看前幾筆內容再接下游
- [ ] 改 schema/config 後已推到 live,驗收條文含指令實跑貼輸出

## References 索引

Mannie:`skill_view coding-hermes file_path=references/<f>`

| 檔名 | 何時讀 |
|---|---|
| `references/fixindex-usage.md` | 查舊帳或完工寫入 fixindex |
| `references/agent-tools.md` | 維護 agent 系統本身 |
| `references/research-playbook.md` | Layer 2 找輪子與查證 |
| `references/stop-loss.md` | 卡住或清單變長 |
| `references/acceptance.md` | 驗收前 |
| `references/command-construction.md` | 要三條案例細節 |