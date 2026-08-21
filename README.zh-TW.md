# coding-hermes-skill

> English: [README.md](README.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

可安裝的 coding agent 行為紀律 skill —— 三層紀律、fixindex 查寫、明確停損閾值。

**技能類型：** Hermes Agent skill（純文檔紀律，無 runtime 依賴）

## 安裝

```bash
npx skills add https://github.com/royalskynet/coding-hermes-skill --skill coding-hermes
```

安裝到 `~/.hermes/skills/coding-hermes/`。**開新 session** 才會進索引生效。

## 功能

| 層 | 觸發 | 動作 |
|---|---|---|
| 1. 啟動前查 fixindex | coding 任務開頭 | `fixindex find "<症狀>"` + `session_search`，合併閱讀 |
| 2. 首輪失敗後查上游 | 同一道指令第一次失敗 | 四路並行：(a) `fixindex find` 回查失敗症狀；(b) **官方文檔** — 工具/套件 docs、CHANGELOG、migration guide；(c) **社群反饋** — GitHub Discussions、Stack Overflow、官方 Discord/論壇、Reddit，搜錯誤訊息原文；(d) **`gh search` 找現成輪子** — `gh search issues "<錯誤>" --state closed`、`gh search code "<關鍵 API>"`、`gh search repos "<問題>"`，看別人怎麼解，用現成方案，不重造輪子 |
| 3. 三條路線失敗後記錄並阻斷 | 已試 3 種不同方案仍失敗 | 用 `fixindex fi` 記錄「未修 + 診斷 + 下一步」，停手求助，不盲第 4 條 |

**停損閾值（兩者不同義）：**
- **盲試 ≤ 2 次** — 重述假設，列 ≥3 個可證偽替代解釋（protocol / transport / routing / lifecycle / stale-state / 權限 / 依賴）
- **修正輪 ≤ 3 輪** — 已定位根因，每輪換不同修法

## 觸發條件

**觸發：** coding、整合、除錯、多步規劃、或連續失敗需要重估的任務。

**不觸發：** 純問答、閒聊、資料查詢。

## 選用依賴

[fixindex](https://github.com/royalskynet/fixindex) — bug runbook CLI，症狀→修法查詢。

未安裝時，階段 0 的 `fixindex find` 段落改以本機記憶 / 搜尋工具代替，skill 其餘紀律照樣適用。

## 來源

本 skill 抽出自 [royalskynet/coding-hermes](https://github.com/royalskynet/coding-hermes)（`NousResearch/hermes-agent` 的 fork）的 coding agent 行為紀律，做成獨立可安裝的 skill。沿革詳情見 [HISTORY.md](HISTORY.md)。

## License

MIT。Copyright (c) 2026 royalskynet。
