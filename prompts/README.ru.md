<div align="right">

[EN](README.md) | **RU**

</div>

# Промты

В этой папке находятся переиспользуемые промты для **Codex Usage Planner**.

> Одна задача = один prompt.

Текущий workflow использует два реализованных промта:

```text
PLAN до запуска
↓
RUN coding task
↓
RECORD фактический расход после запуска
```

---

## Реализованные промты

### Planning

#### [`planning/usage-planner.md`](planning/usage-planner.md)

Используется **до** выполнения coding task.

Вход:

```text
STATUS
+
REPOSITORY
+
TASK
+
необязательные ограничения
```

Возвращает:

- сложность задачи;
- оценку scope репозитория;
- риск для 5-часового окна;
- влияние на weekly;
- рекомендуемую модель;
- рекомендуемый reasoning;
- разбивку задачи при необходимости;
- правило эскалации;
- оптимизированный prompt;
- итоговый вердикт.

Типичные вердикты:

```text
✅ RUN
⚠️ SPLIT FIRST
⛔ WAIT FOR RESET
⛔ REDUCE SCOPE
```

---

### History

#### [`history/usage-recorder.md`](history/usage-recorder.md)

Используется **после** выполнения coding task.

Вход:

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

Он:

1. разбирает usage до/после;
2. отличает `used` от `remaining`;
3. определяет reset лимита;
4. считает фактический расход 5h/weekly, когда это возможно;
5. добавляет одну строку в `../history/usage-log.md`, если доступна запись;
6. иначе возвращает готовую Markdown-строку для ручной вставки.

Правило:

> Один запуск модели = одна строка истории.

---

## Какой prompt использовать?

| Задача | Prompt |
|---|---|
| Полная preflight-оценка задачи и лимитов | `planning/usage-planner.md` |
| Записать фактический расход после запуска | `history/usage-recorder.md` |
| Только выбрать модель/reasoning | `planning/model-selector.md` *(планируется)* |
| Разбить большую задачу | `planning/task-splitter.md` *(планируется)* |
| Уменьшить scope/стоимость prompt | `optimization/prompt-optimizer.md` *(планируется)* |
| Уменьшить context репозитория | `optimization/context-optimizer.md` *(планируется)* |
| Только оценить scope репозитория | `analysis/repo-estimator.md` *(планируется)* |
| Решить, безопасен ли один run | `analysis/preflight-check.md` *(планируется)* |

---

## Текущая структура

```text
prompts/
├── README.md
├── README.ru.md
├── planning/
│   └── usage-planner.md
└── history/
    └── usage-recorder.md
```

Планируемая структура:

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

## Правила создания промтов

### 1. Один файл — одна задача

Лучше несколько небольших специализированных промтов, чем один `everything-planner.md`.

### 2. Явный input

Пользователь не должен угадывать формат входных данных.

### 3. Предсказуемый output

Структурированный ответ проще использовать вручную и автоматизировать позже.

### 4. Не выполнять target development task

Промты этого проекта предназначены для планирования, оценки, оптимизации, декомпозиции и записи usage.

### 5. Не создавать ложную точность

Будущий расход заранее неизвестен точно. Пока недостаточно похожей истории, лучше использовать:

```text
LOW
MEDIUM
HIGH
```

или явно обозначенный приблизительный диапазон.

### 6. История должна быть фактической

Реальные измерения хранятся в:

```text
history/usage-log.md
```

Их нельзя придумывать или переписывать задним числом.

### 7. Названия моделей могут меняться

По возможности используй capability-based правила и не размножай временные предположения по множеству файлов.

---

## Как добавить новый prompt

Например, нужно добавить быстрый выбор модели.

Создай:

```text
prompts/planning/model-selector.md
```

После этого обнови:

- этот README;
- `README.md`;
- корневые README, если prompt входит в основной workflow;
- `AGENTS.md`, если меняется поведение репозитория.

---

## Текущий статус

### v0.1

Реализовано:

- [x] `planning/usage-planner.md`
- [x] `history/usage-recorder.md`

Планируется:

- [ ] `planning/model-selector.md`
- [ ] `planning/task-splitter.md`
- [ ] `optimization/prompt-optimizer.md`
- [ ] `optimization/context-optimizer.md`
- [ ] `analysis/repo-estimator.md`
- [ ] `analysis/preflight-check.md`

---

> Сначала спланируй. После — измерь. Улучшай прогнозы данными.
