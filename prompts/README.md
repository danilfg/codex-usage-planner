<div align="right">

**EN** | [RU](README.ru.md)

</div>

# Prompts

This directory contains reusable prompts for **Codex Usage Planner**.

Each prompt should solve one clear planning or optimization problem.

The goal is to keep prompts focused, reusable, and easy to combine without turning one file into a large monolithic instruction set.

> One purpose = one prompt.

---

## Available prompts

### Planning

#### [`planning/usage-planner.md`](planning/usage-planner.md)

The main prompt of the project.

Use it before sending a development task to Codex or another AI coding agent.

It analyzes:

* task complexity;
* repository scope;
* expected 5-hour usage risk;
* expected weekly usage impact;
* recommended model;
* recommended reasoning level;
* whether the task should be split;
* where escalation to a stronger model is justified;
* how the original task can be rewritten to reduce unnecessary usage.

Input:

```text
STATUS
+
REPOSITORY
+
TASK
```

Example:

```text
STATUS:

5h remaining: 63%
Weekly remaining: 48%

REPOSITORY:

https://github.com/example/project

TASK:

Refactor Jenkins permissions so every student can only see their own job.
Update tests and verify that existing scenarios still work.
```

Typical output:

```text
TASK: LARGE

5H:
🟡 RISK

WEEK:
🟡 NOTICEABLE

RECOMMENDED:
Sol / Medium

PLAN:
1. Analyze scope
2. Implement core change
3. Update tests
4. Cleanup

VERDICT:
⚠️ SPLIT FIRST
```

---

# Planned prompts

The following prompts are planned for future versions.

---

## Planning

### `planning/model-selector.md`

A lightweight prompt for choosing only:

* model;
* reasoning level;
* escalation strategy.

Use it when a full usage analysis is unnecessary.

Example input:

```text
Fix a flaky Playwright test caused by asynchronous rendering.
```

Possible result:

```text
Model: Sol
Reasoning: Medium

Escalate:
Sol High only if the root cause remains unclear.
```

---

### `planning/task-splitter.md`

Splits a large development request into smaller, independently executable iterations.

Useful when the original task combines:

* analysis;
* implementation;
* infrastructure;
* tests;
* migrations;
* cleanup;
* documentation.

Example:

```text
Refactor authentication, Jenkins permissions,
Docker resources and CI tests.
```

Possible result:

```text
1. Analyze authentication and Jenkins flow
2. Implement permission changes
3. Update Docker/resource limits
4. Update focused tests
5. Run regression
```

---

## Optimization

Future directory:

```text
prompts/optimization/
```

### `optimization/prompt-optimizer.md`

Rewrites a development prompt to reduce unnecessary AI usage.

It should:

* preserve requirements;
* reduce vague scope;
* identify relevant directories;
* forbid unrelated refactoring;
* specify focused tests;
* add stopping conditions.

Example:

```text
Analyze the whole project and fix everything related to Jenkins.
```

may become:

```text
Limit this task to Jenkins authorization and job visibility.

Inspect only directly related configuration, authorization code,
job creation logic and focused tests.

Do not refactor unrelated services.

Stop after focused tests pass.
```

---

### `optimization/context-optimizer.md`

Determines which files or directories actually need to be included in context.

Goal:

```text
entire repository
```

→

```text
deploy/jenkins/
docker-compose.yml
auth/
tests/jenkins/
```

This can reduce unnecessary repository exploration.

---

## Analysis

Future directory:

```text
prompts/analysis/
```

### `analysis/repo-estimator.md`

Estimates how much of a repository a specific task is likely to touch.

Expected output may include:

```text
Likely inspected:
10–20 files

Likely modified:
4–8 files

Subsystems:
auth, Jenkins, Docker, tests

Complexity:
LARGE
```

This prompt should estimate scope without implementing the task.

---

### `analysis/preflight-check.md`

Answers one main question:

> Should this task be executed as one agent run?

Possible result:

```text
⚠️ SPLIT FIRST

Reason:
The task mixes application code, infrastructure and regression testing.

Recommended:
4 independent iterations.
```

---

# Prompt categories

The planned structure is:

```text
prompts/
├── README.md
├── README.ru.md
│
├── planning/
│   ├── usage-planner.md
│   ├── model-selector.md
│   └── task-splitter.md
│
├── optimization/
│   ├── prompt-optimizer.md
│   └── context-optimizer.md
│
└── analysis/
    ├── repo-estimator.md
    └── preflight-check.md
```

---

# Which prompt should I use?

| Need                               | Prompt                              |
| ---------------------------------- | ----------------------------------- |
| Full task + usage assessment       | `planning/usage-planner.md`         |
| Choose model and reasoning only    | `planning/model-selector.md`        |
| Split a large task                 | `planning/task-splitter.md`         |
| Reduce prompt cost/scope           | `optimization/prompt-optimizer.md`  |
| Reduce repository context          | `optimization/context-optimizer.md` |
| Estimate affected repository scope | `analysis/repo-estimator.md`        |
| Decide whether one run is safe     | `analysis/preflight-check.md`       |

Only `usage-planner.md` is currently implemented.

---

# Prompt design rules

When adding a new prompt:

## 1. One purpose per file

Avoid prompts that try to solve every possible problem.

Prefer:

```text
model-selector.md
task-splitter.md
prompt-optimizer.md
```

instead of:

```text
everything-planner.md
```

---

## 2. Keep input explicit

Every prompt should clearly describe the expected input.

Example:

```text
TASK:

REPOSITORY:

STATUS:
```

Do not require the user to guess the format.

---

## 3. Keep output predictable

Prefer structured output.

For example:

```text
MODEL:
Sol

REASONING:
Medium

VERDICT:
✅ RUN
```

Predictable output makes prompts easier to use manually and easier to automate later.

---

## 4. Do not execute target tasks

Prompts in this repository are primarily intended for:

* planning;
* estimation;
* optimization;
* decomposition.

Unless a prompt explicitly says otherwise, it should **not implement the user's target development task**.

---

## 5. Avoid unsupported precision

Do not invent exact quota costs when there is not enough evidence.

Prefer:

```text
LOW
MEDIUM
HIGH
```

or historically supported ranges.

---

## 6. Prefer real usage data

When available, estimates should use:

```text
history/usage-log.md
```

Real historical measurements are more valuable than generic assumptions.

---

## 7. Model names may change

Avoid spreading temporary model-specific assumptions across many prompt files.

Where possible:

* use capability-based rules;
* keep model-specific guidance centralized;
* update prompts when the available model lineup changes.

---

# Adding a new prompt

Example:

You want to add a prompt that only selects the best model.

Create:

```text
prompts/planning/model-selector.md
```

Then update this README:

```text
Available prompts
→ Planning
→ model-selector.md
```

If it becomes important enough for users, also add it to the root `README.md`.

---

# Current status

## v0.1

Implemented:

* [x] `planning/usage-planner.md`

Planned:

* [ ] `planning/model-selector.md`
* [ ] `planning/task-splitter.md`
* [ ] `optimization/prompt-optimizer.md`
* [ ] `optimization/context-optimizer.md`
* [ ] `analysis/repo-estimator.md`
* [ ] `analysis/preflight-check.md`

---

> Plan first. Spend reasoning where it matters.
