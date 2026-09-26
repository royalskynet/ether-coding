# Layer 2 找輪子與查證 — 完整範本與判準

> 三層行為紀律的 Layer 2：新增相依／陌生 API／外部整合／要寫超過膠水量，或同一解法連續失敗 2 次時使用。一般小改免列候選方案，也不產生研究日誌。

## 來源順序

1. 確認 repo 鎖定／已安裝版本、既有 adapter 與 client。
2. 讀對應版本的官方文件與 migration／release notes。
3. 查 upstream closed issue／discussion，核對已知限制與修復版本。
4. 搜真實程式碼用法與成熟替代方案，驗證文件外的整合細節。

外部整合同時確認 credential、timeout、rate limit、pagination、retry／idempotency、錯誤映射與 live mutation 邊界。未授權不得安裝套件、隱式下載工具、輸出 secret 或呼叫 production 寫操作。

## GitHub 指令範本

```bash
# 只在跨 session 或重複故障時回查舊帳
fixindex find "<上次的錯誤訊息或症狀>"

# (c)+(d) GitHub 上找「已經解掉這題的人」
gh search issues "<錯誤訊息原文>" --state closed --limit 10
gh search issues "<錯誤訊息原文>" --repo <upstream-owner/repo> --limit 10
gh search code "<關鍵 API 或設定鍵>" --limit 10       # 看真實用法,不靠猜
gh search repos "<要解的問題>" --sort stars --limit 10  # 有沒有現成輪子
```

沒有 `gh` 網路路徑時，改用可用的 GitHub connector／瀏覽器；保持同一來源順序，不因工具失敗降低證據標準。

## 判準

**先找人解過，再自己想**。查完依最小解階梯比較 repo 既有實作、stdlib／native、已安裝相依與新套件；只有新套件確實更小、維護與供應鏈風險可接受時才採用。若形成 fixindex 條目，註明關鍵來源 URL。

只有進入擴大調查時才記錄查詢管道與關鍵字；一般小改不產生研究日誌。

## 違反特徵

一步一發現、靠 error message 往回推用法。
