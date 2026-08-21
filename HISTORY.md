# HISTORY

## 沿革

### Origin

This doctrine originated in `royalskynet/coding-hermes`, a fork of `NousResearch/hermes-agent`.

最初位置：`skills/software-development/coding-hermes/SKILL.md`，由 commit `4a679a3296367f69ec7dca150a904a1638178b19`（2026-08-20T17:31:13Z，author royalskynet）引入，走 PR [#6](https://github.com/royalskynet/coding-hermes/pull/6)，分支 `feat/coding-hermes-skill`。

### Extraction

The doctrine was extracted into this standalone skill because:

1. The content is decoupled from the hermes-agent runtime (doc-only discipline).
2. Bundled inside a 9000+ file fork, it cannot be independently installed.
3. The fork's CI is independently red (drift vs upstream: ruff enforcement, Windows footguns, test slices 1,3,4 on `main`), so docs-only PRs get caught in the crossfire.

### mdispatch removal

Early versions of this doctrine depended on `royalskynet/mdispatch` (plan.md dispatch CLI with falsifiability lint). That repo no longer exists (GitHub API returns 404). The dependency entry and `curl | sudo tee` install line were removed in coding-hermes's `chore/drop-mdispatch-dead-dep` PR. Dispatch flow is now handled by each harness's own kanban mechanism.

### Preserved dependency

`royalskynet/fixindex` remains in maintenance (last update 2026-08-18) and is the optional dependency of this skill.

---

## 沿革

### 來源

本紀律誕生於 `royalskynet/coding-hermes`，該 repo 是 `NousResearch/hermes-agent` 的 fork。

原始位置：`skills/software-development/coding-hermes/SKILL.md`，由 commit `4a679a3296367f69ec7dca150a904a1638178b19`（2026-08-20T17:31:13Z，author royalskynet）引入，走 PR [#6](https://github.com/royalskynet/coding-hermes/pull/6)，分支 `feat/coding-hermes-skill`。

### 抽出原因

紀律內容與 hermes-agent runtime 無耦合（純文檔紀律），綁在 9000+ 檔的 fork 內無法被單獨安裝；且該 fork 的 CI 相對 upstream 已漂移（ruff / Windows footguns / test slices 1,3,4 在 `main` 即為紅），docs-only PR 也被連坐。

### mdispatch 廢除

早期紀律曾依賴 `royalskynet/mdispatch`（plan.md 派工 CLI，含 falsifiability lint）。該 repo 已不存在（GitHub API 404），相關 README 條目與 `curl | sudo tee` 安裝指令於本次一併移除（見 coding-hermes 的 `chore/drop-mdispatch-dead-dep` PR）。派工流程改由各 harness 自己的工單檢查機制承接。

### 保留依賴

`royalskynet/fixindex` 仍在維護（最後更新 2026-08-18），為本 skill 的選用依賴。
