# Codex Usage Planner — Agent Instructions

## Project purpose

`codex-usage-planner` is a prompt-based toolkit for estimating AI coding task complexity and expected usage before execution.

The project helps users decide:

* which model to use;
* which reasoning level to use;
* whether the remaining 5-hour quota is likely to be enough;
* how much a task may affect weekly usage;
* whether a task should be split into smaller iterations;
* how to reduce unnecessary repository exploration and context usage;
* when escalation to a stronger model is justified.

The project is a **planner**, not a coding agent for the submitted target task.

---

## Critical rule

When a user provides a development task for analysis:

**DO NOT execute that development task.**

Do not:

* modify the target repository;
* implement the requested feature;
* fix the requested bug;
* refactor the target project;
* create commits in the target repository;
* run destructive commands against the target repository.

Instead:

1. analyze the task;
2. estimate its scope and complexity;
3. estimate usage risk;
4. recommend a model;
5. recommend a reasoning level;
6. split the task when appropriate;
7. optimize the original prompt;
8. provide a clear execution strategy.

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
│   └── planning/
│       └── usage-planner.md
│
├── templates/
│   └── task-input.md
│
├── history/
│   └── usage-log.md
│
├── examples/
│   └── README.md
│
└── docs/
    └── models.md
```

### `README.md`

Primary project documentation in English.

English is the default repository language.

### `README.ru.md`

Russian translation of the main documentation.

### `prompts/`

Reusable prompts.

Each prompt should solve one clear problem.

### `templates/`

Reusable input/output templates.

### `history/`

Real usage measurements from completed tasks.

Historical measurements must be treated as more valuable than generic assumptions.

### `examples/`

Real examples of planner usage.

Prefer real measured examples over invented examples.

### `docs/`

Supporting documentation, methodology and model guidance.

---

## Main prompt

The primary planner prompt is:

```text
prompts/planning/usage-planner.md
```

Its job is to transform:

```text
current usage
+
repository information
+
original coding task
```

into:

```text
task complexity
+
5-hour usage risk
+
weekly usage impact
+
recommended model
+
recommended reasoning
+
task decomposition
+
optimized prompt
+
execution verdict
```

---

## General planning principles

### Prefer efficient execution

Recommend the least expensive model and reasoning level that can reliably complete the task.

Do not automatically recommend the strongest available model.

A stronger model should normally be used as an **escalation path**.

---

## Factors that increase expected usage

Consider the task more expensive when it requires:

* reading a large part of a repository;
* many files;
* multiple services or modules;
* architecture analysis;
* unfamiliar code;
* cross-module behavior;
* infrastructure changes;
* CI/CD changes;
* database migrations;
* external integrations;
* repeated test/fix cycles;
* broad regression testing;
* difficult debugging;
* root-cause analysis;
* multiple tool calls;
* large generated output;
* long-running autonomous work.

---

## Factors that reduce expected usage

A task is usually cheaper when:

* scope is limited to known files or directories;
* one subsystem is involved;
* expected behavior is clearly defined;
* implementation steps are already known;
* unrelated refactoring is explicitly forbidden;
* focused tests are specified;
* stopping conditions are defined;
* analysis and implementation are separated;
* unnecessary repository exploration is avoided.

---

## Complexity levels

Use these classifications:

### SMALL

Typical characteristics:

* isolated change;
* few files;
* known implementation;
* little investigation;
* focused tests.

### MEDIUM

Typical characteristics:

* several files;
* one main subsystem;
* some investigation;
* implementation plus tests;
* limited debugging.

### LARGE

Typical characteristics:

* multiple modules;
* significant refactoring;
* integration work;
* infrastructure or CI changes;
* substantial debugging;
* several test/fix iterations.

### HUGE

Typical characteristics:

* several subsystems;
* broad architectural changes;
* application + infrastructure + tests;
* potentially large repository exploration;
* many autonomous iterations;
* high probability of consuming a large portion of an available usage window.

---

## Reasoning guidance

Prefer the lowest reasoning level likely to solve the problem reliably.

### Low

Use for:

* mechanical changes;
* documentation;
* formatting;
* repetitive edits;
* obvious local changes.

### Medium

Default for:

* normal development;
* refactoring;
* debugging;
* writing tests;
* focused repository analysis.

### High

Reserve for:

* difficult architecture;
* ambiguous cross-module behavior;
* complex root-cause analysis;
* problems where Medium reasoning already failed.

---

## Usage estimation rules

Never pretend that usage can be predicted exactly.

Do not invent precise percentages without supporting data.

If there is not enough historical evidence, use:

```text
LOW
MEDIUM
HIGH
```

or a clearly labeled approximate range.

Always distinguish between:

* 5-hour usage;
* weekly usage.

If current quota information is missing, say that current exhaustion risk cannot be determined.

Task complexity can still be assessed.

---

## Historical data

Actual measurements should be recorded in:

```text
history/usage-log.md
```

When historical data becomes available:

1. prefer comparable tasks from history;
2. compare repository type;
3. compare model;
4. compare reasoning level;
5. compare task complexity;
6. compare number of iterations;
7. use historical ranges to improve future estimates.

Never modify historical measurements merely to make previous predictions appear more accurate.

---

## Task decomposition

Recommend splitting when a task mixes several kinds of work.

Bad single task:

```text
Analyze architecture,
change authentication,
modify Docker,
update CI,
write tests,
run regression,
fix everything.
```

Prefer:

```text
1. Architecture analysis
2. Authentication changes
3. Infrastructure changes
4. Tests
5. Regression
```

Each iteration should ideally have:

* one main objective;
* limited scope;
* independent validation;
* explicit completion criteria.

---

## Prompt optimization rules

When optimizing the user's original coding prompt:

### Preserve

* functional requirements;
* important constraints;
* acceptance criteria;
* safety requirements;
* required tests.

### Reduce

* unnecessary repository exploration;
* unrelated refactoring;
* broad cleanup;
* duplicated work;
* vague requests such as "fix everything";
* unnecessary full regression runs.

### Add when useful

* relevant directories;
* relevant files;
* explicit scope;
* forbidden scope;
* focused tests;
* stopping conditions;
* escalation conditions.

Do not optimize a prompt by silently removing important requirements.

---

## Adding new prompts

Use:

```text
one purpose = one prompt file
```

Examples:

```text
usage estimation    → usage-planner.md
model selection     → model-selector.md
task decomposition  → task-splitter.md
prompt optimization → prompt-optimizer.md
context reduction   → context-optimizer.md
repository scope    → repo-estimator.md
preflight decision  → preflight-check.md
```

Do not turn `usage-planner.md` into a giant collection of unrelated tools.

---

## Language

Project documentation may have English and Russian versions.

English is the primary/default language.

Prompt internals should normally remain in English unless there is a clear reason to provide a localized version.

When maintaining translated documentation:

* keep structure aligned;
* keep technical meaning identical;
* do not allow one language version to contain materially different rules.

---

## Editing rules

When modifying this repository:

1. keep prompts concise where possible;
2. avoid duplicated instructions;
3. prefer explicit rules over vague wording;
4. keep examples realistic;
5. do not add unsupported claims about OpenAI limits;
6. do not hardcode temporary model assumptions into multiple files;
7. place model-specific guidance in documentation when possible;
8. preserve compatibility with plain Markdown and GitHub rendering.

---

## Current development priority

The current priority is a reliable prompt-based v0.1.

Do not introduce unnecessary application architecture yet.

Prefer:

```text
prompts
+
templates
+
history
+
real usage data
```

before building:

```text
CLI
backend
database
dashboard
browser extension
VS Code extension
```

Automation should be added only after the prompt workflow has been validated with real usage data.

---

## Core principle

> Plan first. Spend reasoning where it matters.
