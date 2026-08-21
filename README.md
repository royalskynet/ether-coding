# coding-hermes-skill

> 繁體中文：[README.zh-TW.md](README.zh-TW.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Coding-agent behavior doctrine as an installable skill — pre-work discipline, research-first, glue coding, architecture principles, three-layer verification discipline, two stop-loss axes (retry / scope), fixindex read & write verification, acceptance discipline, and agent collaboration rules. No numeric stop-loss thresholds (see the host-division note below).

**Skill type:** Hermes Agent skill（純文檔紀律，無 runtime 依賴）

## Install

```bash
npx skills add https://github.com/royalskynet/coding-hermes-skill --skill coding-hermes
```

Installs to `~/.hermes/skills/coding-hermes/`. **Start a new session** for it to be indexed.

## What it does

**Pre-work discipline** — state your assumptions, ask when uncertain; write minimally solvable code, no speculative features; change only what's necessary; define acceptance before coding.
**Research-first (10:7)** — spend ~10 units developing to 7 units researching: search proven approaches and read official docs before writing (Layer 2 of the three-layer discipline). List 2–3 candidate approaches with one-line reasons against; on a four-path miss, list the channels and keywords you searched.
**State-audit before archaeology** — on handoff / continuation, first audit the *current* state (VCS status, runbook status). Reports are claims; status is fact. Declaration ≠ effect: commit ≠ persisted; verify sync before claiming done.

| Layer | Trigger | Action |
|---|---|---|
| 1. Open-time lookup | coding task start | `fixindex find \"<symptom>\"` + `session_search`, merge results |
| 2. On-failure re-query | before your own code exceeds glue (new file / abstraction / >50-line logic), or first failure of a command | Four parallel paths: (a) `fixindex find` with failure symptom; (b) **official docs** — tool/package docs, CHANGELOG, migration guide; (c) **community feedback** — GitHub Discussions, Stack Overflow, official Discord/forums, Reddit, search error message verbatim; (d) **`gh search` for prior art** — `gh search issues \"<error>\" --state closed`, `gh search code \"<key API>\"`, `gh search repos \"<problem>\"`, use existing solutions, don't reinvent the wheel |
| 3. Record-and-block after 3 approaches | 3 different approaches failed | Record with `fixindex fi` (unfixed + diagnosis + next step) | stop and ask for help |

**Glue coding** — don't invent, glue: no reinvention; stub first then code; Occam's razor; falsifiable-first (seek counterexamples); official docs first; community fallback.

**Architecture principles** — no backward-compat layers; layered growth from a minimal runnable base; modularization; decisions for the long term.

**Stop-loss: two axes, not interchangeable**
- **Retry axis** — whether to keep trying the same question. Error-cascade warning; stop symbol variants of the same solution; explicit assumptions; deterministic test before exploratory attempts.
- **Scope axis** — whether this problem needs fixing now. Four structural causes feed each other (untracked state, no tests, doc-code drift, silent failure). Judgment: **impact × silent-failure matrix**:

|  | Silent failure | Loud error |
|---|---|---|
| **Large impact** | ① highest priority | ② next |
| **Small impact** | ③ record, don't fix now | ④ ignore |

Three hard lines: advance one layer at a time; stop at the third layer and write a handoff file instead of fixing; open a new document past 5 findings.

**Acceptance discipline** — loose acceptance is silent failure (judge/guard tests must include a verdict sample that turns red); quiz-based acceptance (one question after major changes).

**Agent collaboration rules** — judgment calls only for models, code handles routing/retry; surface conflicts rather than mix; read before writing; tests verify intent; checkpoints; convention beats novelty; fail loud.

## Host-division note

This skill deliberately does **not** define numeric stop-loss thresholds, the counterfactual gate, or the concrete lookup entry point — those belong in your agent's always-on global rules (CLAUDE.md / SOUL.md / system prompt), because they must apply to every turn, not only when this skill is loaded. If your host defines none, the qualitative criteria in each section stand on their own.

## When it triggers / doesn't

**Triggers:** coding, integration, debugging, multi-step planning, or any task with repeated failures needing re-evaluation.

**Does not trigger:** pure Q&A, casual chat, data lookup.

## Optional dependency

[fixindex](https://github.com/royalskynet/fixindex) — bug runbook CLI for symptom→fix lookup.

If not installed, Layer 1's `fixindex find` step falls back to local memory / search tools. All other discipline rules still apply.

## Origin

This skill packages the coding-agent behavior doctrine from [royalskynet/coding-hermes](https://github.com/royalskynet/coding-hermes) (a fork of NousResearch/hermes-agent) into a standalone installable skill. See [HISTORY.md](HISTORY.md) for the full lineage.

## License

MIT. Copyright (c) 2026 royalskynet.