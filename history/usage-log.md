# Usage History

Actual Codex usage measurements recorded after completed runs.

Use **remaining percentage** for both `5h` and `Weekly`.

Example:

`5h Before: 70%` means **70% remaining**, not 70% used.

> This file stores facts, not predictions.

---

## Usage Log

| Date | Repository | Task | Model | Reasoning | 5h Before | 5h After | 5h Cost | Week Before | Week After | Week Cost | Result |
|---|---|---|---|---|---:|---:|---:|---:|---:|---:|---|

---

## How to add a record

Preferred workflow:

1. Save `/status` before the coding run.
2. Run one model/reasoning iteration.
3. Save `/status` after the run.
4. Use [`../prompts/history/usage-recorder.md`](../prompts/history/usage-recorder.md).
5. Let the recorder calculate the actual cost and append one row.

If repository write access is unavailable, the recorder returns a ready-to-paste Markdown row.

---

## One run = one row

If the workflow is:

```text
Sol Medium → could not find root cause
Astra Medium → found root cause
Sol Medium → implemented fix
```

record **three rows**, not one combined measurement.

This is required so future estimates can compare models and reasoning levels accurately.

---

## Result values

Use only:

```text
✅ Success
⚠️ Partial
❌ Failed
```

---

## Reset handling

If a quota reset happens between the BEFORE and AFTER statuses, do not calculate a negative cost.

Example:

```text
Before: 8% remaining
After: 96% remaining
```

Record:

```text
5h Cost: UNKNOWN — reset occurred
```

The same rule applies to weekly usage.

---

## Missing data

Never estimate missing actual values.

If either BEFORE or AFTER is unavailable, keep the known values and record the corresponding cost as `UNKNOWN`.

---

## Illustrative example only

The following row demonstrates the format. It is **not** an actual measurement and must not be copied into the Usage Log unless it really occurred.

```markdown
| 2026-09-17 | danilfg/bank-test-platform | Jenkins job isolation | Sol | Medium | 66% | 55% | 11% | 50% | 47% | 3% | ✅ Success |
```

---

## Historical ranges

Only derive ranges after enough comparable real measurements exist.

Compare at least:

- repository;
- task type;
- model;
- reasoning;
- approximate complexity;
- testing/debugging scope.

Do not rewrite old measurements to make previous predictions appear more accurate.

> Estimates may be approximate. History must be factual.
