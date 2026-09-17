<div align="right">

[EN](task-input.md) | **RU**

</div>

# Шаблон задачи

Используй этот минимальный шаблон перед оценкой задачи через **Codex Usage Planner**.

```text
STATUS:

[ВСТАВЬ /status]


REPOSITORY:

https://github.com/danilfg/bank-test-platform

Branch:
main


TASK:

[ВСТАВЬ ИСХОДНЫЙ PROMPT]


CONSTRAINTS:

[НЕОБЯЗАТЕЛЬНО]
```

## Пример

```text
STATUS:

5h remaining: 64%
Weekly remaining: 47%


REPOSITORY:

https://github.com/danilfg/bank-test-platform

Branch:
main


TASK:

Переделать Jenkins так, чтобы каждый ученик видел и мог запускать только свою job.

Обновить связанные тесты и проверить, что существующий учебный сценарий продолжает работать.

Если задача слишком большая для одного запуска, разбить её на небольшие независимые итерации.


CONSTRAINTS:

- Не менять несвязанный банковский функционал.
- Не делать несвязанный рефакторинг.
- Сохранить работоспособность текущего Docker-окружения.
```
