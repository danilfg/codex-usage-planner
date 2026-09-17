<div align="right">

**EN** | [RU](README.ru.md)

</div>

# Prompts

This directory contains reusable prompts for **Codex Usage Planner**.

> One purpose = one prompt.

The current workflow uses two implemented prompts:

```text
PLAN before execution
↓
RUN the coding task
↓
RECORD actual usage after execution
```

---

## Implemented prompts

### Planning

#### [`planning/usage-planner.md`](planning/usage-planner.md)

Use **before** executing a coding task.

Input:

```text
STATUS
+
REPOSITORY
+
TASK
+
optional constraints
```

It returns:

- task complexity;
- repository scope estimate;
- 5-hour usage risk;
- weekly usage impact;
- recommended model;
- recommended reasoning;
- task decomposition when needed;
- escalation rule;
- optimized prompt;
- execution verdict.

Typical verdicts:

```text
✅ RUN
⚠️ SPLIT FIRST
⛔ WAIT FOR RESET
⛔ REDUCE SCOPE
```

---

### History

#### [`history/usage-recorder.md`](history/usage-recorder.md)

Use **after** executing a coding task.

Input:

```text
BEFORE /status
+
AFTER /status
+
REPOSITORY
+
TASK
+
MODEL
+
REASONING
+
RESULT
```

It:

1. parses before/after usage;
2. distinguishes `used` from `remaining`;
3. detects quota resets;
4. calculates factual 5h/weekly cost when possible;
5. appends one history row to `../history/usage-log.md` when write access is available;
6. otherwise returns a ready-to-paste Markdown row.

Rule:

> One model execution = one history row.

---

## Which prompt should I use?

| Need | Prompt |
|---|---|
| Full preflight task + usage assessment | `planning/usage-planner.md` |
| Record actual cost after a run | `history/usage-recorder.md` |
| Choose model/reasoning only | `planning/model-selector.md` *(planned)* |
| Split a large task | `planning/task-splitter.md` *(planned)* |
| Reduce prompt scope/cost | `optimization/prompt-optimizer.md` *(planned)* |
| Reduce repository context | `optimization/context-optimizer.md` *(planned)* |
| Estimate repository scope only | `analysis/repo-estimator.md` *(planned)* |
| Decide whether one run is safe | `analysis/preflight-check.md` *(planned)* |

---

## Current structure

```text
prompts/
├── README.md
├── README.ru.md
├── planning/
│   └── usage-planner.md
└── history/
    └── usage-recorder.md
```

Planned structure:

```text
prompts/
├── planning/
│   ├── usage-planner.md
│   ├── model-selector.md
│   └── task-splitter.md
├── history/
│   └── usage-recorder.md
├── optimization/
│   ├── prompt-optimizer.md
│   └── context-optimizer.md
└── analysis/
    ├── repo-estimator.md
    └── preflight-check.md
```

---

## Prompt design rules

### 1. One purpose per file

Prefer focused prompts over a monolithic `everything-planner.md`.

### 2. Keep input explicit

Users should never need to guess the required format.

### 3. Keep output predictable

Structured output is easier to use manually and automate later.

### 4. Do not execute the target development task

These prompts are for planning, estimation, optimization, decomposition, and usage recording.

### 5. Avoid false precision

Future usage is uncertain. Without enough comparable history, prefer:

```text
LOW
MEDIUM
HIGH
```

or clearly labeled approximate ranges.

### 6. Keep history factual

Actual measurements belong in:

```text
history/usage-log.md
```

Do not invent or rewrite them.

### 7. Model names may change

Prefer capability-based rules where possible and avoid spreading temporary assumptions across many files.

---

## Adding a new prompt

Example: add a lightweight model selector.

Create:

```text
prompts/planning/model-selector.md
```

Then update:

- this README;
- `README.ru.md`;
- root README files if the prompt is part of the main workflow;
- `AGENTS.md` when repository behavior changes.

---

## Current status

### v0.1

Implemented:

- [x] `planning/usage-planner.md`
- [x] `history/usage-recorder.md`

Planned:

- [ ] `planning/model-selector.md`
- [ ] `planning/task-splitter.md`
- [ ] `optimization/prompt-optimizer.md`
- [ ] `optimization/context-optimizer.md`
- [ ] `analysis/repo-estimator.md`
- [ ] `analysis/preflight-check.md`

---

> Plan first. Measure after. Improve with data.
