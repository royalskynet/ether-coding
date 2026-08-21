# coding-hermes-skill

> 繁體中文：[README.zh-TW.md](README.zh-TW.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Coding-agent behavior doctrine as an installable skill — three-layer discipline, fixindex lookup, and explicit stop-loss thresholds.

**Skill type:** Hermes Agent skill（純文檔紀律，無 runtime 依賴）

## Install

```bash
npx skills add https://github.com/royalskynet/coding-hermes-skill --skill coding-hermes
```

Installs to `~/.hermes/skills/coding-hermes/`. **Start a new session** for it to be indexed.

## What it does

| Layer | Trigger | Action |
|---|---|---|
| 1. Open-time lookup | coding task start | `fixindex find "<symptom>"` + `session_search`, merge results |
| 2. On-failure re-query | first failure of a command | Four parallel paths: (a) `fixindex find` with failure symptom; (b) **official docs** — tool/package docs, CHANGELOG, migration guide; (c) **community feedback** — GitHub Discussions, Stack Overflow, official Discord/forums, Reddit, search error message verbatim; (d) **`gh search` for prior art** — `gh search issues "<error>" --state closed`, `gh search code "<key API>"`, `gh search repos "<problem>"`, see how others solved it, use existing solutions, don't reinvent the wheel |
| 3. Record-and-block after 3 approaches | 3 different approaches failed | Record with `fixindex fi` (unfixed + diagnosis + next step), stop and ask for help, don't attempt a 4th blindly |

**Stop-loss thresholds (not interchangeable):**
- **Blind attempts ≤ 2** — before re-stating hypotheses and listing ≥3 falsifiable alternatives (protocol / transport / routing / lifecycle / stale-state / permission / dependency)
- **Fix rounds ≤ 3** — after root cause located, each round uses a different approach

## When it triggers / doesn't

**Triggers:** coding, integration, debugging, multi-step planning, or any task with repeated failures needing re-evaluation.

**Does not trigger:** pure Q&A, casual chat, data lookup.

## Optional dependency

[fixindex](https://github.com/royalskynet/fixindex) — bug runbook CLI for symptom→fix lookup.

If not installed, Phase 0's `fixindex find` step falls back to local memory / search tools. All other discipline rules still apply.

## Origin

This skill packages the coding-agent behavior doctrine from [royalskynet/coding-hermes](https://github.com/royalskynet/coding-hermes) (a fork of NousResearch/hermes-agent) into a standalone installable skill. See [HISTORY.md](HISTORY.md) for the full lineage.

## License

MIT. Copyright (c) 2026 royalskynet.
