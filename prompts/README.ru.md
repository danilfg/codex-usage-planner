<div align="right">

[EN](README.md) | **RU**

</div>

# Промты

В этой папке хранятся переиспользуемые промты для **Codex Usage Planner**.

Каждый prompt должен решать одну конкретную задачу планирования или оптимизации.

Цель — сделать промты небольшими, понятными и совместимыми друг с другом, а не превращать один файл в огромную универсальную инструкцию.

> Одна задача = один prompt.

---

## Доступные промты

### Planning

#### [`planning/usage-planner.md`](planning/usage-planner.md)

Главный prompt проекта.

Используется перед тем, как отправлять задачу в Codex или другой AI coding agent.

Он оценивает:

* сложность задачи;
* предполагаемый scope репозитория;
* риск для 5-часового лимита;
* влияние на недельный лимит;
* рекомендуемую модель;
* рекомендуемый reasoning;
* нужно ли разбивать задачу;
* когда имеет смысл перейти на более мощную модель;
* как переписать исходный prompt, чтобы уменьшить лишний расход.

Вход:

```text
STATUS
+
REPOSITORY
+
TASK
```

Пример:

```text
STATUS:

5h remaining: 63%
Weekly remaining: 48%

REPOSITORY:

https://github.com/example/project

TASK:

Переделать Jenkins permissions так, чтобы каждый ученик
видел только свою job.

Обновить тесты и убедиться, что существующие сценарии работают.
```

Типичный результат:

```text
TASK: LARGE

5H:
🟡 RISK

WEEK:
🟡 NOTICEABLE

RECOMMENDED:
Sol / Medium

PLAN:
1. Проанализировать scope
2. Реализовать основное изменение
3. Обновить тесты
4. Cleanup

VERDICT:
⚠️ SPLIT FIRST
```

---

# Планируемые промты

Следующие промты можно добавить в будущих версиях.

---

## Planning

### `planning/model-selector.md`

Короткий prompt только для выбора:

* модели;
* reasoning;
* правила эскалации.

Используется, когда полный анализ лимитов не нужен.

Пример входа:

```text
Исправить flaky Playwright test,
связанный с асинхронным рендерингом.
```

Возможный результат:

```text
Model: Sol
Reasoning: Medium

Escalate:
Sol High только если root cause останется неясным.
```

---

### `planning/task-splitter.md`

Разбивает большую задачу на небольшие независимые итерации.

Полезен, когда исходный запрос одновременно включает:

* анализ;
* реализацию;
* инфраструктуру;
* тесты;
* миграции;
* cleanup;
* документацию.

Например:

```text
Переделать authentication, Jenkins permissions,
Docker resources и CI tests.
```

может превратиться в:

```text
1. Проанализировать authentication и Jenkins flow
2. Реализовать permissions
3. Изменить Docker/resource limits
4. Обновить focused tests
5. Выполнить regression
```

---

## Optimization

Будущая папка:

```text
prompts/optimization/
```

### `optimization/prompt-optimizer.md`

Переписывает исходный development prompt так, чтобы уменьшить лишний расход AI.

Он должен:

* сохранять требования;
* уменьшать неопределённый scope;
* указывать релевантные директории;
* запрещать unrelated refactoring;
* задавать focused tests;
* добавлять stopping conditions.

Например:

```text
Посмотри весь проект и исправь всё,
что связано с Jenkins.
```

может превратиться в:

```text
Ограничь задачу Jenkins authorization и job visibility.

Исследуй только связанные конфигурации,
authorization code, job creation logic и focused tests.

Не рефактори несвязанные сервисы.

Остановись после прохождения focused tests.
```

---

### `optimization/context-optimizer.md`

Определяет, какие файлы и директории действительно нужны модели в контексте.

Цель:

```text
весь репозиторий
```

превратить, например, в:

```text
deploy/jenkins/
docker-compose.yml
auth/
tests/jenkins/
```

Это помогает уменьшить ненужное исследование проекта.

---

## Analysis

Будущая папка:

```text
prompts/analysis/
```

### `analysis/repo-estimator.md`

Оценивает, какую часть репозитория, скорее всего, затронет конкретная задача.

Пример результата:

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

Этот prompt должен только оценивать scope, а не выполнять саму задачу.

---

### `analysis/preflight-check.md`

Отвечает на один основной вопрос:

> Стоит ли выполнять эту задачу одним агентным запуском?

Например:

```text
⚠️ SPLIT FIRST

Причина:
Задача одновременно затрагивает application code,
инфраструктуру и regression testing.

Рекомендуется:
4 независимые итерации.
```

---

# Категории промтов

Планируемая структура:

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

# Какой prompt использовать?

| Задача                                   | Prompt                              |
| ---------------------------------------- | ----------------------------------- |
| Полная оценка задачи и лимитов           | `planning/usage-planner.md`         |
| Только выбрать модель и reasoning        | `planning/model-selector.md`        |
| Разбить большую задачу                   | `planning/task-splitter.md`         |
| Уменьшить стоимость/scope prompt         | `optimization/prompt-optimizer.md`  |
| Уменьшить контекст репозитория           | `optimization/context-optimizer.md` |
| Оценить scope репозитория                | `analysis/repo-estimator.md`        |
| Решить, можно ли запускать всё одним run | `analysis/preflight-check.md`       |

Сейчас реализован только `usage-planner.md`.

---

# Правила создания новых промтов

## 1. Один файл — одна задача

Не стоит делать prompt, который пытается решить вообще всё.

Лучше:

```text
model-selector.md
task-splitter.md
prompt-optimizer.md
```

чем:

```text
everything-planner.md
```

---

## 2. Явно описывать вход

Каждый prompt должен объяснять, что пользователь должен передать.

Например:

```text
TASK:

REPOSITORY:

STATUS:
```

Пользователь не должен догадываться о формате.

---

## 3. Делать предсказуемый output

Лучше использовать структурированный ответ.

Например:

```text
MODEL:
Sol

REASONING:
Medium

VERDICT:
✅ RUN
```

Такой формат удобен и человеку, и будущему CLI/API.

---

## 4. Не выполнять целевую задачу

Промты этого репозитория в первую очередь предназначены для:

* планирования;
* оценки;
* оптимизации;
* декомпозиции.

Если конкретный prompt явно не говорит обратного, он **не должен выполнять development task пользователя**.

---

## 5. Не придумывать точные цифры

Если данных недостаточно, нельзя выдумывать точную стоимость задачи.

Лучше:

```text
LOW
MEDIUM
HIGH
```

или диапазон, подтверждённый историческими данными.

---

## 6. Использовать реальные измерения

Если доступна история:

```text
history/usage-log.md
```

нужно предпочитать реальные измерения общим предположениям.

---

## 7. Названия моделей могут меняться

Не стоит размножать временные предположения о конкретных моделях по десяткам файлов.

По возможности:

* использовать capability-based правила;
* хранить model-specific информацию централизованно;
* обновлять промты при изменении доступных моделей.

---

# Как добавить новый prompt

Например, нужно добавить prompt только для выбора модели.

Создай:

```text
prompts/planning/model-selector.md
```

После этого обнови этот README:

```text
Доступные промты
→ Planning
→ model-selector.md
```

Если prompt становится важной частью проекта, добавь его также в корневой `README.md`.

---

# Текущий статус

## v0.1

Реализовано:

* [x] `planning/usage-planner.md`

Планируется:

* [ ] `planning/model-selector.md`
* [ ] `planning/task-splitter.md`
* [ ] `optimization/prompt-optimizer.md`
* [ ] `optimization/context-optimizer.md`
* [ ] `analysis/repo-estimator.md`
* [ ] `analysis/preflight-check.md`

---

> Сначала спланируй. Трать reasoning там, где он действительно нужен.
