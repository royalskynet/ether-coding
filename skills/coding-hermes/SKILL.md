---
name: coding-hermes
description: "Coding/debug 任務載入:膠水思維、架構原則、三層查證紀律、停損兩軸(重試/範圍)、fixindex 查寫與寫入驗證、代理協作紀律、測驗式驗收。"
version: 2.1.0
author: royalskynet (Ether)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [coding, debugging, fixindex, discipline, stop-loss, glue-coding, architecture, kanban, self-sufficient]
    related_skills: [systematic-debugging, hermes-agent, kanban-worker]
---

# coding-hermes — coding agent 行為紀律

> 來源：royalskynet/coding-hermes(Hermes agent fork)的行為紀律，抽出成獨立可安裝 skill。
> 目的:讓 coding/debug 代理在任務中減少反覆與人工介入。

**與宿主全域規則的分工**:本 skill 只放**方法論**。開工查舊帳的入口、非模型反事實閘門、盲試與修正輪的**具體次數閾值**,由宿主 agent 的全域規則(CLAUDE.md / SOUL.md / system prompt)定義 —— 那些是每輪都生效的前提,不該由按需載入的 skill 重述。宿主未定義時,採用本文各段的通用判準。

## When to Use

正在處理**開發 / 整合 / 除錯 / 規劃多步 coding 任務**、或連續失敗需要重估、或問題越查越多需要決定哪些不做時 → 載入本 skill。純問答、閒聊、資料查詢不觸發。

## 動手前

- 明說假設,不確定就問
- 寫最小可解問題的代碼,不寫猜測功能
- 只改必要的,不順手重構無關部分
- 先定驗收標準再動手
- **研究優先:10 分開發,7 分找資料** —— 先搜成熟方案、讀官方文件再寫(見「三層行為紀律」Layer 2)。計畫要列 2–3 個候選方案與各一句不用的理由;若四路零命中,改列查過的管道與關鍵字。例外免列:一次性腳本、<50 行黏合、純配置改動

## 狀態盤點先於考古

接手跨 session 續作、或收到前一輪完成報告時,第一步盤點**當前實際狀態**(版控狀態、runbook status、服務健康),不靠複述的報告當事實。

**報告是宣稱,狀態是事實;衝突以狀態為準。** 宣告 ≠ 生效:commit ≠ 落盤,完成前必須驗證同步。

## 膠水思維

能抄不寫,能連不造,能復用不原創:

1. **不發明** → 先搜已驗證方案
2. **只做膠水** → 專注連接方式,不造輪子
3. **先結構後代碼** → 接口/架構先清楚
4. **奧卡姆剃刀** → 砍不必要的複雜度
5. **可證偽優先** → 主動找反例驗證,不假設正確然後硬推
6. **官方文檔優先** → 整合/配置前讀懂官方文檔,嚴格遵循官方設計邏輯
7. **社群回饋備援** → 仍未解才參考他人類似問題,再回第 1 項核實

## 架構原則

- **不留向下相容** — 移除過時路徑,不加相容層／備用方案／遷移層
- **分層成長** — 從最小可運行版本起,每次在已可用產品上疊加;絕不為未完成的複雜性交易已成熟的產品
- **模組化** — 元件邊界清楚,關注點分離
- **為長期做決策** — 不接受只對當下有效、日後註定被取代的臨時方案

## 三層行為紀律(核心,長期任務每輪都在跑)

| 層 | 觸發 | 動作 |
|---|---|---|
| 1. 動手前查舊帳 | coding 任務開頭 | 修理日誌(`fixindex find "<症狀>"`)+ 過去 session 歷史,**合併閱讀,不是查無才查另一個**。命中 → 直接讀對應條目 |
| 2. 找輪子與查證 | ① 準備自己寫的量超過膠水(新檔案、新抽象、>50 行邏輯)之前;② 同一道指令首次失敗之後 | 四路並行:(a) 以症狀回查 `fixindex find` → 命中注入再重試;(b) **官方文檔** — 該工具/套件的 docs、CHANGELOG、migration guide,確認接口與版本行為;(c) **社群反饋** — GitHub Discussions、Stack Overflow、官方 Discord/論壇、Reddit,搜「錯誤訊息原文」找踩過同一坑的人;(d) **gh 找輪子與靈感** — `gh search issues`／`code`／`repos`,看別人怎麼解、有沒有現成套件可直接用,不重造輪子 |
| 3. 三條路線失敗後記錄並阻斷 | 已試 3 種不同方案仍失敗 | 用 `fixindex fi` 記錄「未修 + 診斷 + 下一步」,停手求助,不盲第 4 條 |

**Layer 2 指令範本:**

````bash
# (a) 回查自己的舊帳
fixindex find "<上次的錯誤訊息或症狀>"

# (c)+(d) GitHub 上找「已經解掉這題的人」
gh search issues "<錯誤訊息原文>" --state closed --limit 10
gh search issues "<錯誤訊息原文>" --repo <upstream-owner/repo> --limit 10
gh search code "<關鍵 API 或設定鍵>" --limit 10       # 看真實用法,不靠猜
gh search repos "<要解的問題>" --sort stars --limit 10  # 有沒有現成輪子
````

判準:**先找人解過,再自己想**。查完若發現已有現成套件/工具,改用它,不續修自己的實作,並在 fixindex 條目註明來源 URL。

**四路皆零命中,也要寫下查了哪些關鍵字** —— 那是「查過」的證據,不是失敗。零命中才進入停損段的假設重述。

違反特徵:一步一發現、靠 error message 往回推用法。

## 停損

停損有**兩個軸,不要混用**:

- **重試軸** — 同一個問題還要不要繼續試
- **範圍軸** — 這個問題該不該現在修

卡住時用重試軸;清單越查越長時用範圍軸。

> 具體次數閾值(未定位根因的盲試上限、已定位根因的修正輪上限)由宿主全域規則定義。本段只給兩軸的判準與動作。

### 範圍停損:修不完的時候

發現越挖越多**不是運氣差,是結構性的**。四種成因會互相生產彼此:
沒版本控管 → 出事才發現沒還原點;沒測試 → 改對改錯都不知道;
文件與程式碼脫節 → 錯誤陳述被當事實重抄;靜默失敗 → 壞了沒人知道,累積到連鎖爆發才一次浮現。

**判準:影響面 × 是否靜默失敗**

|  | 靜默失敗 | 會噴錯 |
|---|---|---|
| **影響面大** | ① 最優先 | ② 次之 |
| **影響面小** | ③ 只記錄,不當場修 | ④ 不用管 |

一句話:**會靜默失敗的先修,會噴錯的可以等** —— 噴錯的東西會自己找上門。

**三條硬性線:**

- 一次只推進一層,有依賴的不准並行
- **挖到第三層就停下來寫交接文件,不要直接修** —— 第三層通常已超出單一 session 能安全處理的範圍
- 新發現超過 5 條就開新文件,不要無限膨脹當前這份

**使用者說「修不完」時**:那是對的觀察,不要否認也不要安慰。
給分類與停損線,說清楚「③④ 是記錄不是待辦,真正必做的只有這幾條」——
比承諾「我會一個一個修好」有用,後者是在承諾做不到的事。

判準的價值不在少做事,在於**讓「不做」變成可以說出口的決定**。

### 重試停損

**錯誤連鎖警戒**:錯誤架構 → 功能異常 → 誤判「工具/模型太弱」→ 反覆換方案 → 燒時間。
第一次失敗就回頭驗「我的做法符合已驗證的正確方式嗎」,不要改到第三個變體。

**同一解法連續失敗**:停止變體嘗試,重述假設與反例,不原樣重送指令。

- 動手前明寫假設:「我假設 X 能 work,因為 Y。如果 Z 發生就停」
- 先做**決定性測試**(一次驗證或推翻假設),再做探索性嘗試
- 停手後回到 Layer 2 跑找輪子四路,這是該層的觸發時機 ②

## 完工寫入 fixindex(只在修好 defect 時)

> 完整指令細節（`fixindex find` / `fixindex fi` defect 與 insight 格式 / 禁則清單 / 環境變數）見 [references/fixindex-usage.md](references/fixindex-usage.md)。
>
> 維護 agent 系統本身時（改行為契約檔／查系統健康／動自我改進管線）見 [references/agent-tools.md](references/agent-tools.md)——工具在 `~/dev/agent-tools`，可派 worker。

修好**一個 defect** 才記錄(階段完成、任務交付、session 收工並不算;phase 工作流最容易把 runbook 寫成日記)。

**判準**:寫不出別人會拿來搜尋的 `Symptom` → 這是進度報告,不是條目。

補記入口(fixindex CLI;勿手動 append `## §N` 或改 frontmatter):
```bash
printf 'SYMPTOM: ...\nROOT: ...\nFIX: ...\nVERIFY: <可重跑指令 + 期望結果>' | fixindex fi
```
insight(已固化的設計決策,非 defect):
```bash
printf 'CONTEXT: ...\nINSIGHT: ...\nIMPLICATION: ...\nREVISIT-WHEN: ...\nQUERIES: q1, q2' | fixindex fi
```
新域:`fixindex new <slug>`;frontmatter 損毀 → `fixindex doctor --fix`。

**禁**:date 進檔名、`## §N (date)` 修正片段(直接改原文)、Verify 用一次性讀數(必須可重跑+期望)、Fix 寫成「Phase 3 修好」、metrics/PID 塞進 `symptoms:`、一條記塞多個 defect、secrets(含截斷 key 前綴)。

**診斷未實作也寫**:標「not fixed」+ 下一步 — 診斷本身是資產。

### 寫入驗證:search 命中 ≠ 寫入成功

`fixindex fi` 寫完後**不得**用 `fixindex find "<剛寫的症狀>"` 當成功判準。索引是語意搜尋,幾個月前的相近舊帳一樣會命中高分 —— 寫入根本失敗時也照樣「查得到」,看起來像成功。

**唯一判準是 `fixindex fi` 自己回傳的 JSON**:

```json
{"appended": "0322-<slug>.md", "section": 2, "committed": "05c8954", "pushed": true, "git_error": null}
```

- `appended` + `section` → 條目的精確位址 `NNNN#S`,回報時貼這個,不要只寫編號
- `committed` 有 SHA、`pushed: true`、`git_error: null` → 才算落地
- 指令非零結束碼、或 harness 回 `Exec failed` → **就是沒寫進去**,不得回報成功

**複查要對得上位址**:`fixindex find` 之後,確認命中的是 `appended`/`section` 指的那一條(比對 title 與 tags),不是語意相近的別條。對不上就是沒寫成功。

**跳脫地獄的解**:`printf` 內嵌 `\|`、`%`、巢狀引號極易讓整條指令掛掉。內容超過三行就先寫檔再 pipe:

```bash
cat /tmp/fi-entry.txt | fixindex fi
```

## 驗收紀律

**驗收寬鬆＝靜默失敗**:Judge/Guard/referee 類功能修復時,驗收樣本必須含**一條會紅的判決樣本**(明確違規 → 必須 block/rewrite;正常 → allow),否則全 PASS 可能是 fail-open 洗白 —— 機制沒判,全靠放行。

偵測:樣本 verdict 全部落在安全側(allow / guard_error)而無任何 block/rewrite ＝ 警訊。

**貼原始輸出,不貼過濾後的輸出**: 驗收回報裡的指令輸出必須是那條指令的原樣結果。想聚焦就把過濾條件寫進指令本身(`git status --porcelain -- <path>`), 不要跑寬指令再手挑幾行貼出來 —— 讀的人會把它當全貌。輸出很長就標明「節錄,共 N 行」並附上產生節錄的指令。

**工具的回報也是宣稱**:健檢/lint/audit/探測工具的輸出跟人的完成報告同級 —— 是宣稱,不是事實。兩個方向都要先懷疑量測工具:

- **正面回報**(「抓到 N 個問題」)：工具穩定吐同一批不會消失的 issue 時,第一個假設是**偵測器過期**,不是待修清單很長。查法:`git log -S'<檢查裡的關鍵字串>'` 找引入 commit —— 若它跟某個已完成的修法同批進來,那個修法就是它的退役條件。標 `report only`/`warning only` 的檢查最危險,沒人會去修,誤報永久累積直到整份輸出沒人看。全文 fixindex `0599`。
- **負面探測**(連不上/解析失敗/401/空回應)：可能是自己的探測姿勢,不是對象壞了。單一負面訊號只能開啟調查,不能結案。全文 fixindex `0575`。
- 靠名字/長度/關鍵字比對的檢查一定會誤傷真資料,再加一道便宜的內容閘(行數、section 數、是否空殼)。**自己臨時寫的掃描腳本同樣適用** —— 抽驗幾筆命中再相信它的總數。

**訊號收集器上線後先看它收到什麼,不是看它有沒有在收**:「有資料進來」最常被當成運作正常,但**誤報率 100% 的收集器比沒有收集器更糟** —— 下游會把噪音當證據,而且噪音愈多看起來愈像訊號充足。

- **前 N 筆逐筆人工看過內容再接下游**。任何自動分類器都適用,不只回饋收集。
- 事件來源是**共用管道**時(hook 的 UserPromptSubmit、匯流的 log、broadcast topic),先問「除了我要的,還有誰會流經這裡」—— harness 注入的任務通知、系統提醒、slash command 的 stdout 都長得像使用者發言。
- **關鍵詞比對要限定位置**。糾正與指令會在開頭出現;同一個詞埋在長文深處多半是敘述用語(「這**不是** harness 失敗」)。全文比對必然把長篇報告收成糾正。
- 判準:抽樣的摘要若一筆都不含觸發詞,代表觸發位置遠離開頭,比對範圍就是錯的。全文 fixindex `0631`。

**測驗式驗收(重大改動限定)**:重大改動(人格 prompt、交易系統、fallback 順序、排程、不可逆操作)完成後出**一題**測驗,確認使用者懂改了什麼及為何:

- 只問一題,答對即通過
- 沒答對 → 白話說明改動,再確認修哪裡或人工放行(不強制修)
- 小修小補(typo、小 bug、格式)免考

## 代理協作紀律

- **Judgment Calls Only** — 模型管分類/草稿/摘要,代碼管路由/重試/轉換
- **Surface Conflicts** — 兩種模式衝突時擇一並標記另一種,不混合
- **Read Before You Write** — 加代碼前必讀導出項/調用者/工具庫,防重複
- **Tests Verify Intent** — 測試編碼「為何這麼做」,不是「做了什麼」;因邏輯改變而壞的測試無效
- **Checkpoints** — 每步完成摘要進度;失去邏輯追蹤立即停止重述
- **Convention Beats Novelty** — 自認寫法更好也遵從既有命名與架構慣例
- **Fail Loud** — 不確定 100% 成功(跳過記錄/測試/驗證)必須明說,禁默認成功

## 長期任務自給自足(agent / subagent 在跑時)

> **長期任務自給自足**：長任務檢查點一律用 state.json——檔名、schema、時機見 `~/.claude/shared/state-file.md`（每 iteration 邊界先覆寫再續跑）。

## 從回饋學到的

> 本段由 improver 維護：它讀人類回饋（session 內糾正、PR review comment、
> `fixindex mark` 的斷言），一次只提一條、開 PR，**人工 merge 後才生效**。
> marker 以外的內容視為 CORE，improver 不得改動。

<!-- LEARNED:BEGIN -->
<!-- LEARNED:END -->

## 指令構造（agent 構造指令的當下不觸發檢索）

`context-router`／claude-mem 的召回綁在 **user prompt** 上；**agent 自己構造指令的那一刻沒有任何注入**。以下三條是重踩過的「指令構造型」教訓 —— 靠語意檢索救不回來，必須寫成常駐行為：

1. **`head` 截斷輸出判斷內容會誤判**（fixindex 0547#5）：`git status --porcelain | head` 剛好被 10 行改名填滿，未追蹤檔的證據被切掉，據此下錯結論。→ 判斷「有沒有某類輸出」時用 `wc -l`／`grep -c` 數全量，不用 `head` 當全貌；要節錄就明寫「節錄，共 N 行」。
2. **`mv` 語意隨目標存在與否而變**（fixindex 0629#2）：目標不存在＝改名、目標存在＝搬進去，同條指令兩種結果（實測把狀態目錄包成 93 層巢狀）。→ 移動前先確認目標目錄是否已存在，且 `mv` 後用 `find`／`ls` 驗證最終位置與層數，不預設語意。
3. **`push` 錯 ref／無效 ref 會靜默 no-op**（fixindex 0636）：`git push` 印「Everything up-to-date」但本地 10 個 commit 沒上去（ref 指錯），退出碼仍是 0。→ push 後比對 `git log origin/<branch>..HEAD` 剩餘 commit 數，或 `git status -sb` 看 ahead 數，不靠「沒報錯」當成功。

> 這三條是「高頻且後果大」才被選入；全部塞進去會讓 skill 膨脹，違反 progressive disclosure。行為改善**未經實驗驗證**——若再踩到三種之一，代表本段固化無效，應改 lint 或收工自檢（追蹤：fixindex 0645）。

## 收工自檢清單

- [ ] 動手前有查舊帳(修理日誌 + session 歷史,合併閱讀)
- [ ] 自己寫超過膠水量之前,或首輪失敗之後,有跑 Layer 2 四路找輪子;零命中有留關鍵字證據
- [ ] 卡住用重試軸、清單變長用範圍軸,沒混用
- [ ] 完工後有 `fixindex fi` 記錄(除非無 defect 可記)
- [ ] 寫入有貼 `fixindex fi` 回傳的 `appended`/`section`/`committed`/`pushed`,不是拿 `find` 命中充數
- [ ] Judge/Guard 類改動的驗收樣本含一條會紅的判決
- [ ] 依健檢/掃描工具的回報做事之前,有抽驗幾筆命中確認不是誤報(含自己剛寫的腳本)
- [ ] 新上線的訊號收集器/分類器,有逐筆看過前幾筆**內容**再接下游,不是只確認「有資料」
- [ ] 改 schema/config 後已推到 live,驗收條文含指令已實跑貼輸出
