# Codex Usage Planner — Agent Instructions

## Project purpose

`codex-usage-planner` is a prompt-based toolkit for two related workflows:

1. **Preflight planning** before an AI coding task is executed.
2. **Post-run measurement** after the task is completed.

The project helps users:

- estimate task complexity;
- assess 5-hour and weekly usage risk;
- choose an appropriate model and reasoning level;
- split large tasks into smaller iterations;
- optimize prompts to reduce unnecessary scope and context;
- record actual before/after usage;
- use real history to improve future estimates.

The project does **not** execute the submitted target development task.

---

## Core workflow

```text
TASK
  ↓
prompts/planning/usage-planner.md
  ↓
PLAN / MODEL / REASONING / OPTIMIZED PROMPT
  ↓
USER RUNS THE TARGET TASK
  ↓
BEFORE + AFTER /status
  ↓
prompts/history/usage-recorder.md
  ↓
history/usage-log.md
```

Keep prediction and measurement separate.

- Planner output may be approximate.
- History must remain factual.

---

## Critical rule for target repositories

When a user provides a development task for **planning or estimation**:

**DO NOT execute that target development task.**

Do not:

- modify the target repository;
- implement the requested feature;
- fix the requested bug;
- refactor the target project;
- create commits in the target repository;
- run destructive commands against the target repository.

Instead:

1. analyze the task;
2. estimate scope and complexity;
3. assess usage risk;
4. recommend model and reasoning;
5. split the task when appropriate;
6. optimize the original prompt;
7. provide a concise execution strategy.

This restriction does **not** prevent `usage-recorder.md` from updating this repository's own `history/usage-log.md` when write access is available.

---

## Repository structure

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

### `prompts/planning/usage-planner.md`

Runs before execution.

Transforms:

```text
current usage
+
repository information
+
original coding task
```

into:

```text
complexity
+
5h risk
+
weekly impact
+
recommended model
+
recommended reasoning
+
task split
+
optimized prompt
+
verdict
```

### `prompts/history/usage-recorder.md`

Runs after execution.

Transforms:

```text
before status
+
after status
+
repository
+
task
+
model
+
reasoning
+
result
```

into a factual row in:

```text
history/usage-log.md
```

### `history/usage-log.md`

Contains actual measurements only.

Never treat example values as real observations.

---

## Planning principles

Prefer the least expensive model and reasoning level that can reliably complete the task.

Do not automatically recommend the strongest model or highest reasoning level.

A stronger model should normally be an **escalation path**.

### Usage usually increases with

- large repository exploration;
- many affected files or modules;
- architecture work;
- unfamiliar code;
- cross-module behavior;
- infrastructure or CI/CD changes;
- database migrations;
- external integrations;
- difficult debugging;
- repeated test/fix cycles;
- broad regression runs;
- many tool calls;
- large context;
- long autonomous execution;
- vague instructions such as `fix everything`.

### Usage can often be reduced by

- limiting scope to known files/directories;
- separating analysis from implementation;
- splitting large work into independently testable iterations;
- forbidding unrelated refactoring;
- using focused tests first;
- adding explicit stopping conditions;
- avoiding whole-repository exploration unless required.

---

## Complexity levels

Use:

```text
SMALL
MEDIUM
LARGE
HUGE
```

### SMALL

- isolated change;
- few files;
- little investigation;
- focused tests.

### MEDIUM

- several files;
- one main subsystem;
- some investigation;
- implementation plus tests.

### LARGE

- multiple modules;
- significant refactoring or integration work;
- infrastructure/CI impact;
- several test/fix iterations.

### HUGE

- several subsystems;
- broad architecture work;
- application + infrastructure + tests;
- large repository exploration;
- many autonomous iterations;
- unsuitable for one reliable run.

---

## Reasoning guidance

Prefer the lowest reasoning level likely to solve the task reliably.

### Low / Light

Use for mechanical, repetitive, formatting, documentation, or obvious local changes.

### Medium

Default for normal development, refactoring, debugging, test work, and focused repository analysis.

### High

Reserve for difficult architecture, ambiguous cross-module behavior, hard root-cause analysis, or cases where Medium failed.

Do not use High merely because a task is large. A large but straightforward task may be better as several Medium iterations.

---

## Usage estimation rules

Never present future usage as exact unless supported by enough comparable historical measurements.

When evidence is weak, prefer:

```text
LOW
MEDIUM
HIGH
```

or a clearly labeled approximate range.

Always distinguish:

- current 5-hour usage;
- weekly usage.

If current quota information is missing, task complexity may still be assessed, but exhaustion risk must be marked unknown.

Codex Usage Planner is not an official quota calculator.

---

## Historical data rules

Actual measurements live in:

```text
history/usage-log.md
```

Rules:

1. One model execution = one row.
2. Never invent missing before/after values.
3. Distinguish `used` from `remaining`.
4. Detect quota resets instead of calculating negative cost.
5. Never rewrite historical measurements to improve old predictions.
6. Prefer several comparable measurements before producing a historical range.
7. If repository write access is unavailable, return a ready-to-paste row rather than pretending the history was updated.

When using history for planning, compare:

- repository;
- task type;
- model;
- reasoning;
- complexity;
- affected scope;
- testing scope.

---

## Task decomposition

Recommend splitting when the task mixes several kinds of work, especially:

```text
analysis
+
architecture
+
implementation
+
infrastructure
+
tests
+
regression
```

Each iteration should ideally have:

- one main objective;
- limited scope;
- independent validation;
- explicit completion criteria;
- a stopping condition.

---

## Prompt optimization rules

Preserve:

- functional requirements;
- constraints;
- acceptance criteria;
- required tests;
- safety requirements.

Reduce or constrain:

- whole-repository exploration;
- unrelated refactoring;
- broad cleanup;
- duplicated work;
- vague requests such as `fix everything`;
- unnecessary repeated full regression.

Add when useful:

- relevant directories/files;
- forbidden scope;
- focused tests;
- stopping conditions;
- escalation conditions.

Never silently remove important requirements to make a prompt look cheaper.

---

## Adding new prompts

Use:

```text
one purpose = one prompt file
```

Current implemented prompts:

```text
planning/usage-planner.md  → preflight prediction
history/usage-recorder.md  → post-run measurement
```

Possible future prompts:

```text
planning/model-selector.md
planning/task-splitter.md
optimization/prompt-optimizer.md
optimization/context-optimizer.md
analysis/repo-estimator.md
analysis/preflight-check.md
```

Do not turn `usage-planner.md` into a monolithic collection of unrelated tools.

---

## Language

English is the primary/default documentation language.

Human-facing documentation may have EN and RU versions.

Prompt internals should normally remain in English unless there is a clear reason to localize them.

When maintaining translations:

- keep structure aligned;
- keep technical meaning equivalent;
- avoid materially different rules between languages.

`AGENTS.md` remains English-only.

---

## Editing rules

When modifying this repository:

1. keep prompts focused and concise where possible;
2. avoid duplicated instructions;
3. prefer explicit rules over vague wording;
4. keep examples clearly labeled as examples;
5. do not add unsupported claims about exact quota cost;
6. avoid scattering temporary model assumptions across many files;
7. preserve GitHub-compatible Markdown;
8. update README files when the public workflow changes;
9. update `prompts/README.md` and `prompts/README.ru.md` when prompts are added or removed.

---

## Current development priority

Validate the prompt workflow and collect real measurements before adding unnecessary application architecture.

Prefer:

```text
planner
+
recorder
+
minimal templates
+
real history
```

before:

```text
CLI
backend
database
dashboard
browser extension
VS Code extension
```

---

## Core principle

> Plan first. Measure after. Improve with data.
