<div align="right">

**EN** | [RU](README.ru.md)

</div>

# Codex Usage Planner

<p>
  <img src="https://img.shields.io/badge/Codex-Usage%20Planning-111111?style=flat-square" alt="Codex">
  <img src="https://img.shields.io/badge/ChatGPT-Compatible-111111?style=flat-square" alt="ChatGPT">
  <img src="https://img.shields.io/badge/AI-Coding-111111?style=flat-square" alt="AI Coding">
  <img src="https://img.shields.io/badge/Usage-5h%20%2B%20Weekly-111111?style=flat-square" alt="Usage">
</p>

> Know the cost before you code.

**Codex Usage Planner** is a prompt-based toolkit for planning AI coding tasks before execution and measuring their real usage afterwards.

It helps you answer four practical questions:

- Is this task small enough for the current 5-hour window?
- How much can it affect the weekly usage budget?
- Which model and reasoning level should be used?
- Should the task be split into smaller iterations?

After the task runs, the project can record the real before/after usage in a history table so future estimates can be based on your own measurements.

> Estimates are heuristic. Actual history is factual.

---

## Workflow

```text
Original task
    ↓
Usage Planner
    ↓
Model + reasoning + split + optimized prompt
    ↓
Run the coding task
    ↓
Check /status again
    ↓
Usage Recorder
    ↓
history/usage-log.md
    ↓
Better future estimates
```

The project deliberately separates **prediction** from **measurement**:

- `usage-planner.md` works **before** execution;
- `usage-recorder.md` works **after** execution.

---

## Quick start

### 1. Prepare the task

Use the minimal input template:

[`templates/task-input.md`](templates/task-input.md)

```text
STATUS:
[paste /status]

REPOSITORY:
https://github.com/example/project

Branch:
main

TASK:
[paste the original prompt]

CONSTRAINTS:
[optional]
```

### 2. Plan before execution

Open:

[`prompts/planning/usage-planner.md`](prompts/planning/usage-planner.md)

The planner returns a compact assessment with:

- task size: `SMALL / MEDIUM / LARGE / HUGE`;
- 5-hour risk;
- weekly impact;
- recommended model;
- recommended reasoning;
- task split when needed;
- escalation rule;
- optimized ready-to-copy prompt;
- verdict: `RUN / SPLIT FIRST / WAIT FOR RESET / REDUCE SCOPE`.

### 3. Run the recommended task

Execute the optimized prompt in your coding environment.

For split tasks, record each model execution separately whenever possible.

### 4. Record actual usage

Capture `/status` after the run and use:

[`prompts/history/usage-recorder.md`](prompts/history/usage-recorder.md)

Provide:

```text
BEFORE:
[/status before]

AFTER:
[/status after]

REPOSITORY:
owner/repository

TASK:
short task name

MODEL:
model name

REASONING:
reasoning level

RESULT:
Success / Partial / Failed
```

The recorder calculates the actual cost and appends it to:

[`history/usage-log.md`](history/usage-log.md)

If it cannot edit the repository, it returns a ready-to-paste Markdown row.

---

## Example planner result

```text
TASK: LARGE

SCOPE:
Inspect: ~10–20 files
Modify: ~4–8 files
Systems: Jenkins, auth, Docker, tests

5H:
🟡 RISK
Expected usage: HIGH
Remaining: 43%

WEEK:
🟡 NOTICEABLE
Expected usage: MEDIUM
Remaining: 61%

RECOMMENDED:
Model: Sol
Reasoning: Medium

PLAN:
1. Analyze Jenkins/auth scope → Sol / Medium
2. Implement job isolation → Sol / Medium
3. Add concurrency limits → Terra / Medium
4. Focused tests → Terra / Medium

ESCALATE:
Use a stronger model only if focused investigation cannot resolve the architecture or root cause.

VERDICT:
⚠️ SPLIT FIRST
```

---

## Project structure

```text
codex-usage-planner/
├── README.md
├── README.ru.md
├── AGENTS.md
│
├── prompts/
│   ├── README.md
│   ├── README.ru.md
│   ├── planning/
│   │   └── usage-planner.md
│   └── history/
│       └── usage-recorder.md
│
├── templates/
│   ├── task-input.md
│   └── task-input.ru.md
│
└── history/
    └── usage-log.md
```

---

## Main files

| File | Purpose |
|---|---|
| [`prompts/planning/usage-planner.md`](prompts/planning/usage-planner.md) | Preflight estimate before execution |
| [`prompts/history/usage-recorder.md`](prompts/history/usage-recorder.md) | Record actual before/after usage |
| [`templates/task-input.md`](templates/task-input.md) | Minimal task input template |
| [`history/usage-log.md`](history/usage-log.md) | Factual historical measurements |
| [`AGENTS.md`](AGENTS.md) | Repository rules for coding agents |

See the full prompt index in [`prompts/README.md`](prompts/README.md).

---

## Estimation principles

The planner should prefer the **least expensive model and reasoning level that can reliably complete the task**.

Usage risk usually increases with:

- repository exploration;
- number of affected modules;
- architecture work;
- debugging uncertainty;
- infrastructure and CI/CD changes;
- repeated test/fix cycles;
- large context;
- long autonomous runs.

Usage can often be reduced by:

- limiting scope to relevant files/directories;
- separating analysis from implementation;
- splitting large tasks into independently testable iterations;
- running focused tests first;
- forbidding unrelated refactoring;
- adding explicit stopping conditions.

---

## Model and reasoning strategy

Available model names can change over time, so the planner uses both model names and capability classes.

General strategy:

| Capability | Typical use |
|---|---|
| Lightweight | Mechanical edits, docs, repetitive changes |
| Standard | Clear implementation and focused tests |
| Strong | Main development, refactoring and debugging |
| Advanced | Difficult architecture and root-cause analysis |

The strongest model should usually be an **escalation path**, not the default.

The same principle applies to reasoning:

```text
Low / Light → Medium → High
```

A large but straightforward task is often better handled as several `Medium` iterations than one `High` run.

---

## Historical measurements

`history/usage-log.md` stores actual measurements, not predictions.

Rules:

- one model execution = one history row;
- never invent missing values;
- never rewrite old measurements to improve past predictions;
- detect quota resets instead of calculating negative cost;
- prefer historical ranges only after enough comparable runs exist.

Over time this can make recommendations specific to your real workflow and repositories.

---

## Current status

### v0.1 — Prompt workflow

- [x] Task complexity assessment
- [x] 5-hour usage risk assessment
- [x] Weekly usage impact assessment
- [x] Model recommendation
- [x] Reasoning recommendation
- [x] Task decomposition
- [x] Prompt optimization
- [x] Raw `/status` interpretation inside prompts
- [x] Post-run usage recorder
- [x] Factual usage history
- [x] EN / RU task templates
- [ ] Automatic `/status` capture
- [ ] Automatic repository scope scanner
- [ ] Historical prediction engine
- [ ] CLI
- [ ] VS Code / Codex integration
- [ ] Usage dashboard

---

## Important limitation

Codex Usage Planner is **not an official OpenAI quota calculator** and cannot know the exact cost of a future agent run from the prompt alone.

Before enough historical data exists, it intentionally uses qualitative risk (`LOW / MEDIUM / HIGH`) or clearly marked approximate ranges instead of false precision.

---

## Philosophy

Powerful models are useful. The goal is not to avoid them, but to spend expensive reasoning where it produces real value.

> Plan first. Measure after. Improve with data.
