<div align="right">

**EN** | [RU](task-input.ru.md)

</div>

# Task Input

Use this minimal template before running a task through **Codex Usage Planner**.

```text
STATUS:

[PASTE /status]


REPOSITORY:

https://github.com/danilfg/bank-test-platform

Branch:
main


TASK:

[PASTE YOUR ORIGINAL PROMPT]


CONSTRAINTS:

[OPTIONAL]
```

## Example

```text
STATUS:

5h remaining: 64%
Weekly remaining: 47%


REPOSITORY:

https://github.com/danilfg/bank-test-platform

Branch:
main


TASK:

Refactor Jenkins so each student can only see and run their own job.

Update the related tests and verify that the existing training workflow still works.

If the task is too large for one run, split it into smaller independent iterations.


CONSTRAINTS:

- Do not change unrelated banking functionality.
- Do not perform unrelated refactoring.
- Keep the existing Docker setup working.
```
