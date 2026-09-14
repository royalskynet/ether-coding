# Layer 2 找輪子與查證 — 完整範本與判準

> 三層行為紀律的 Layer 2：新增相依／陌生 API／外部整合／要寫超過膠水量，或同一問題連續失敗 2 次時使用。一般小改免列候選方案，也不產生研究日誌。

## GitHub 指令範本（四路並行）

```bash
# (a) 回查自己的舊帳
fixindex find "<上次的錯誤訊息或症狀>"

# (c)+(d) GitHub 上找「已經解掉這題的人」
gh search issues "<錯誤訊息原文>" --state closed --limit 10
gh search issues "<錯誤訊息原文>" --repo <upstream-owner/repo> --limit 10
gh search code "<關鍵 API 或設定鍵>" --limit 10       # 看真實用法,不靠猜
gh search repos "<要解的問題>" --sort stars --limit 10  # 有沒有現成輪子
```

## 判準

**先找人解過,再自己想**。查完若發現已有現成套件/工具,改用它,不續修自己的實作,並在 fixindex 條目註明來源 URL。

只有進入擴大調查時才記錄查詢管道與關鍵字；一般小改不產生研究日誌。

## 違反特徵

一步一發現、靠 error message 往回推用法。