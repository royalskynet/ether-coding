# coding-hermes-skill

> English: [README.md](README.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

可安裝的 coding agent 行為紀律 skill —— 動手前紀律、研究優先、膠水思維、架構原則、三層查證紀律、停損兩軸（重試 / 範圍）、fixindex 查寫與寫入驗證、驗收紀律、代理協作紀律。不寫停損次數閾值（見下方宿主分工聲明）。

**技能類型：** Hermes Agent skill（純文檔紀律，無 runtime 依賴）

## 安裝

```bash
npx skills add https://github.com/royalskynet/coding-hermes-skill --skill coding-hermes
```

安裝到 `~/.hermes/skills/coding-hermes/`。**開新 session** 才會進索引生效。

## 功能

**動手前紀律** — 明說假設，不確定就問；寫最小可解問題的代碼、不寫猜測功能；只改必要的、不順手重構無關部分；先定驗收標準再動手。
**研究優先（10:7）** — 開發 10 分、研究 7 分：先搜成熟方案、讀官方文件再寫（見三層紀律 Layer 2）。計畫要列 2–3 個候選方案與各一句不用的理由；四路零命中時，列出查過的管道與關鍵字。
**狀態盤點先於考古** — 接手 / 續作時先盤點**當前實際狀態**（版控狀態、runbook status）。報告是宣稱，狀態是事實；宣告 ≠ 生效：commit ≠ 落盤，完成前必須驗證同步。

| 層 | 觸發 | 動作 |
|---|---|---|
| 1. 動手前查舊帳 | coding 任務開頭 | `fixindex find "<症狀>"` + `session_search`，合併閱讀 |
| 2. 找輪子與查證 | 自己寫的量超過膠水（新檔案 / 新抽象 / >50 行邏輯）之前；或同一道指令首次失敗之後 | 四路並行：(a) `fixindex find` 回查失敗症狀；(b) **官方文檔** — 工具/套件 docs、CHANGELOG、migration guide；(c) **社群反饋** — GitHub Discussions、Stack Overflow、官方 Discord/論壇、Reddit，搜錯誤訊息原文；(d) **`gh search` 找現成輪子** — `gh search issues "<錯誤>" --state closed`、`gh search code "<關鍵 API>"`、`gh search repos "<問題>"`，用現成方案，不重造輪子 |
| 3. 三條路線失敗後記錄並阻斷 | 已試 3 種不同方案仍失敗 | 用 `fixindex fi` 記錄「未修 + 診斷 + 下一步」，停手求助 |

**膠水思維** — 能抄不寫、能連不造、能復用不原創；不發明 → 先搜；只做膠水 → 不造輪子；先結構後代碼；奧卡姆剃刀；可證偽優先（主動找反例）；官方文檔優先；社群回饋備援。

**架構原則** — 不留向下相容；分層成長（最小可運行版本起疊加）；模組化（元件邊界清楚）；為長期做決策。

**停損兩軸（不要混用）**
- **重試軸** — 同一個問題還要不要繼續試。錯誤連鎖警戒；同法連續失敗就停止變體嘗試；明寫假設；決定性測試先於探索性嘗試。
- **範圍軸** — 這個問題該不該現在修。四種結構性成因互相生產彼此（沒有版控、沒有測試、文件與程式碼脫節、靜默失敗）。判準：**影響面 × 是否靜默失敗矩陣**：

|  | 靜默失敗 | 會噴錯 |
|---|---|---|
| **影響面大** | ① 最優先 | ② 次之 |
| **影響面小** | ③ 只記錄，不當場修 | ④ 不用管 |

三條硬性線：一次只推進一層；挖到第二層就停下來寫交接文件、不直接修；新發現超過 5 條就開新文件。使用者說「修不完」時給分類與停損線。

**驗收紀律** — 驗收寬鬆＝靜默失敗（Judge/Guard 類修復時驗收樣本必含一條會紅的判決）；測驗式驗收（重大改動後只問一題、答對即通過）。

**代理協作紀律** — 模型管分類/草稿/摘要、代碼管路由/重試/轉換；模式衝突擇一並標記另一種；加程式前先讀檔案；測試編碼「為何這麼做」；每步完成摘要進度；遵守既有慣例；Fail Loud（不確定 100% 成功就明說，禁默認成功）。

## 宿主分工聲明

本 skill 刻意**不**寫停損次數閾值、反事實閘門、或開工查舊帳的具體入口 —— 那些屬於你的 agent 的**每輪都生效**的全域規則（CLAUDE.md / SOUL.md / system prompt），因為它們必須套用到每一輪，而不只是本 skill 被載入時。若你的宿主什麼都沒定義，本文件各節的質性判準仍可單獨成立。

## 觸發條件

**觸發：** coding、整合、除錯、多步規劃、或連續失敗需要重估的任務。

**不觸發：** 純問卷、聊天、資料查詢。

## 選用依賴

[fixindex](https://github.com/royalskynet/fixindex) — bug runbook CLI，症狀→修法查詢。

未安裝時，Layer 1 的 `fixindex find` 段落改以本機記憶 / 搜建工具代替，skill 其餘紀律照樣適用。

## 附帶斜線指令

[`commands/dispatch.md`](commands/dispatch.md) — `/dispatch`：派工前工單客製化檢查（前情自足、可驗證驗收標準、只含必要資訊、步驟詳細、範圍界定、無頭可執行性）。安裝方式：複製到 `~/.claude/commands/dispatch.md`。

## 來源

本 skill 抽出自 [royalskynet/coding-hermes](https://github.com/royalskynet/coding-hermes)（`NousResearch/hermes-agent` 的 fork）的 coding agent 行為紀律，做成獨立可安裝的 skill。沿革詳請見 [HISTORY.md](HISTORY.md)。

## License

MIT。Copyright (c) 2026 royalskynet。