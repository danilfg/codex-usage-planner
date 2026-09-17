<div align="right">

**EN** | [RU](README.ru.md)

</div>

# Codex Usage Planner

> Know the cost before you code.

**Codex Usage Planner** helps estimate the complexity and expected AI usage of a coding task before you send it to Codex.

It helps answer practical questions:

* Which model should I use?
* Which reasoning level is enough?
* Is my remaining 5-hour usage likely to be enough?
* How much can this task affect my weekly usage?
* Should I split the task into smaller iterations?
* Is a more capable model actually necessary?
* How can I rewrite the prompt to reduce unnecessary usage?

The main idea is simple:

> Use the least expensive model and reasoning level that can reliably complete the task.

---

## Why this project exists

Large AI coding tasks can consume much more usage than expected.

For example, a prompt like:

```text
Refactor Jenkins permissions, Docker configuration and CI/CD.
Update tests and fix all discovered problems.
```

may cause the coding agent to:

1. inspect a large part of the repository;
2. analyze architecture;
3. modify several subsystems;
4. run tests;
5. investigate failures;
6. make additional changes;
7. rerun tests;
8. repeat the process several times.

A single broad task may therefore consume a significant part of a 5-hour or weekly usage limit.

Codex Usage Planner performs a **preflight assessment before execution**.

It does not execute your development task.

It tells you how to run it more efficiently.

---

## What the planner evaluates

The planner considers factors such as:

* repository size;
* expected number of files involved;
* number of subsystems affected;
* amount of context required;
* architecture complexity;
* debugging complexity;
* test execution;
* CI/CD and infrastructure changes;
* repeated test/fix cycles;
* external tools;
* expected number of agent iterations;
* selected model;
* reasoning level.

It then recommends:

```text
Task complexity
      ↓
5-hour usage risk
      ↓
Weekly usage impact
      ↓
Recommended model
      ↓
Recommended reasoning
      ↓
Task decomposition
      ↓
Optimized prompt
```

---

## Example

### Input

```text
5h remaining: 68%
Weekly remaining: 41%

Repository:
https://github.com/example/project

Task:

Refactor Jenkins so every student can only see their own job.

Requirements:
- Jenkins is available only to authorized users;
- maximum 3 builds can run simultaneously;
- server resources are limited;
- existing training scenarios must continue working;
- update and run tests.
```

### Example result

```text
TASK: LARGE

5H:
🟡 RISK
Expected usage: HIGH

WEEK:
🟡 NOTICEABLE
Expected usage: MEDIUM

RECOMMENDED:
Model: Sol
Reasoning: Medium

WHY:
The task affects permissions, infrastructure and tests.
It should not be executed as one large agent run.

SPLIT:

1. Analyze Jenkins permissions → Sol Medium
2. Implement job isolation → Sol Medium
3. Add concurrency limits → Terra Medium
4. Update tests → Terra Medium
5. Documentation / cleanup → Luna

ESCALATION:
Use Astra only if Sol cannot identify the root cause of a complex issue.

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
│   └── planning/
│       └── usage-planner.md
│
├── templates/
│   └── task-input.md
│
├── examples/
│
└── history/
    └── usage-log.md
```

---

## Files

### `README.md`

English documentation.

This is the default README displayed by GitHub.

### `README.ru.md`

Russian documentation.

### `AGENTS.md`

Instructions for Codex when working inside this repository.

It explains that submitted development tasks must be **analyzed, not executed**.

### `prompts/planning/usage-planner.md`

The main planner prompt.

This is the primary file you use to evaluate a coding task.

### `prompts/README.md`

Index and description of available prompts.

### `templates/task-input.md`

Reusable input template for new assessments.

### `examples/`

Real examples of planner usage.

### `history/usage-log.md`

Actual usage measurements collected after running tasks.

---

## How to use

### 1. Check your current usage

Copy your current usage information.

For example:

```text
5h remaining: 68%
5h reset: 2h 40m

Weekly remaining: 41%
Weekly reset: 3d 12h
```

If available in your Codex environment, you can also paste the complete output of:

```text
/status
```

---

### 2. Open the main prompt

```text
prompts/planning/usage-planner.md
```

---

### 3. Add your data

Fill in:

```text
STATUS:

5h remaining:
5h reset:

Weekly remaining:
Weekly reset:

REPOSITORY:

URL:
Branch:

TASK:

[Paste your original Codex prompt here]
```

Do not optimize the prompt yourself first.

Paste the task exactly as you originally intended to send it.

The planner should identify unnecessary scope and optimize it for you.

---

## Recommended workflow

Instead of:

```text
Huge task
   ↓
Strongest model
   ↓
High reasoning
   ↓
Entire repository
   ↓
Many iterations
   ↓
Usage exhausted
```

prefer:

```text
Original task
      ↓
Codex Usage Planner
      ↓
Scope assessment
      ↓
Task decomposition
      ↓
Appropriate model
      ↓
Appropriate reasoning
      ↓
Focused execution
```

---

## Model strategy

The exact available models may change over time, so model recommendations should follow capabilities rather than blindly relying on names.

A typical strategy is:

| Model class | Typical use                                            |
| ----------- | ------------------------------------------------------ |
| Lightweight | Mechanical edits, docs, simple repetitive changes      |
| Standard    | Clear implementation tasks and focused testing         |
| Strong      | Main development, refactoring and debugging            |
| Advanced    | Difficult architecture and complex root-cause analysis |

The strongest model should normally be an **escalation path**, not the default choice.

---

## Reasoning strategy

Use the lowest reasoning level that is likely to solve the task reliably.

```text
Low
 ↓
Medium
 ↓
High
```

Typical usage:

### Low

Good for:

* mechanical edits;
* formatting;
* simple documentation;
* obvious localized changes.

### Medium

Good for:

* normal development;
* refactoring;
* debugging;
* test implementation;
* focused repository analysis.

### High

Use when necessary for:

* difficult architecture;
* complex cross-module reasoning;
* difficult root-cause analysis;
* problems that Medium reasoning failed to solve.

---

## Task splitting

A task should usually be split when it mixes several kinds of work.

For example:

```text
Analyze architecture
+
Change authentication
+
Modify infrastructure
+
Update CI/CD
+
Write tests
+
Run regression
```

is usually better transformed into:

```text
1. Analysis
2. Authentication
3. Infrastructure
4. Tests
5. Regression
```

Each iteration should ideally:

* have one primary objective;
* have a limited scope;
* be independently testable;
* have a clear stopping condition.

---

## Prompt optimization

The planner can rewrite a broad prompt such as:

```text
Analyze the project and refactor Jenkins.
Fix everything related to permissions and tests.
```

into something more focused:

```text
Work only with Jenkins authorization and job visibility.

First inspect:
- deploy/jenkins/
- docker-compose.yml
- related authentication configuration
- Jenkins-specific tests

Goal:
Each authorized student must only see their own job.

Do not:
- refactor unrelated application code;
- modify unrelated services;
- inspect the entire repository unless necessary.

After implementation:
1. run Jenkins-related tests;
2. fix failures caused by this change;
3. stop after those tests pass.

Do not perform unrelated cleanup.
```

A smaller scope usually means less unnecessary context and fewer agent iterations.

---

## Usage history

After completing a task, record the real usage in:

```text
history/usage-log.md
```

Example:

```text
| Date | Repository | Task | Model | Reasoning | 5h Before | 5h After | Cost |
|---|---|---|---|---|---:|---:|---:|
| 2026-09-17 | example/project | Jenkins analysis | Sol | Medium | 72% | 66% | 6% |
```

This is important because real usage depends heavily on the actual workflow.

After enough real tasks, the planner can eventually estimate costs based on historical data such as:

```text
Large Django refactor + Medium reasoning
Typical historical cost: 8–13%

Focused test update
Typical historical cost: 2–4%

Complex architecture investigation
Typical historical cost: 15–25%
```

These values should come from actual measurements, not invented assumptions.

---

## Current version

### v0.1 — Prompt-based planner

* [x] Task complexity assessment
* [x] 5-hour usage risk
* [x] Weekly usage risk
* [x] Model recommendation
* [x] Reasoning recommendation
* [x] Task decomposition
* [x] Prompt optimization
* [x] Manual usage history
* [ ] Automatic `/status` parsing
* [ ] Automatic repository scope analysis
* [ ] Historical usage prediction
* [ ] CLI
* [ ] VS Code integration
* [ ] Codex integration
* [ ] Usage dashboard

---

## Future CLI

A future version could work like this:

```bash
codex-usage-plan "Refactor Jenkins permissions"
```

and return:

```text
🟡 LARGE

Recommended:
Sol / Medium

5h impact:
Medium

Weekly impact:
Low–Medium

Split:
3 iterations

Advanced model:
Not required initially
```

---

## Philosophy

Codex Usage Planner is not about avoiding powerful models.

It is about using expensive reasoning **where it actually provides value**.

> Plan first. Spend reasoning where it matters.
