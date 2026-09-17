# Usage History

Real Codex usage measurements.

Use **remaining percentage** for both `5h` and `Weekly`.

Example:

`5h Before: 70%` means **70% remaining**, not 70% used.

---

## Usage Log

| Date | Repository | Task | Model | Reasoning | 5h Before | 5h After | 5h Cost | Week Before | Week After | Week Cost | Result |
| ---- | ---------- | ---- | ----- | --------- | --------: | -------: | ------: | ----------: | ---------: | --------: | ------ |
|      |            |      |       |           |           |          |         |             |            |           |        |

---

## Example

| Date       | Repository                 | Task                    | Model | Reasoning | 5h Before | 5h After | 5h Cost | Week Before | Week After | Week Cost | Result    |
| ---------- | -------------------------- | ----------------------- | ----- | --------- | --------: | -------: | ------: | ----------: | ---------: | --------: | --------- |
| 2026-09-17 | danilfg/bank-test-platform | Jenkins access analysis | Sol   | Medium    |       72% |      66% |      6% |         51% |        50% |        1% | ✅ Success |
| 2026-09-17 | danilfg/bank-test-platform | Jenkins job isolation   | Sol   | Medium    |       66% |      55% |     11% |         50% |        47% |        3% | ✅ Success |
| 2026-09-17 | danilfg/bank-test-platform | Update focused tests    | Terra | Medium    |       55% |      52% |      3% |         47% |        46% |        1% | ✅ Success |

---

## How to record a task

Before running Codex:

```text
5h Before: 66%
Week Before: 50%
```

After Codex finishes:

```text
5h After: 55%
Week After: 47%
```

Calculate:

```text
5h Cost = 66 - 55 = 11%
Week Cost = 50 - 47 = 3%
```

Then add one row to the table.

---

## Result

Use:

```text
✅ Success
⚠️ Partial
❌ Failed
```

If the task required another model or another iteration, record it as a **separate row**.

Example:

```text
1. Sol Medium → failed to find root cause
2. Astra Medium → found root cause
3. Sol Medium → implementation
```

Do not combine these into one measurement.

---

## Important

Do not change historical values later.

Real measurements are used to improve future estimates.

Over time the planner should be able to learn patterns such as:

```text
bank-test-platform
Jenkins / Sol Medium
Typical cost: 7–12%

Focused tests / Terra Medium
Typical cost: 2–4%
```

Only create ranges like these when enough real measurements exist.
