<div align="right">

[EN](README.md) | **RU**

</div>

# Codex Usage Planner

<p>
  <img src="https://img.shields.io/badge/Codex-Usage%20Planning-111111?style=flat-square" alt="Codex">
  <img src="https://img.shields.io/badge/ChatGPT-Compatible-111111?style=flat-square" alt="ChatGPT">
  <img src="https://img.shields.io/badge/AI-Coding-111111?style=flat-square" alt="AI Coding">
  <img src="https://img.shields.io/badge/Usage-5h%20%2B%20Weekly-111111?style=flat-square" alt="Usage">
</p>

> Узнай стоимость задачи до того, как потратишь лимит.

**Codex Usage Planner** — набор промтов для планирования AI-задач до запуска и фиксации их реального расхода после выполнения.

Он помогает быстро ответить на четыре практических вопроса:

- Хватит ли текущего 5-часового окна?
- Насколько задача затронет недельный лимит?
- Какую модель и reasoning выбрать?
- Нужно ли разбить задачу на небольшие итерации?

После выполнения задачи проект может записать фактический расход `до / после` в историю, чтобы следующие оценки постепенно опирались на реальные измерения.

> Прогнозы — приблизительные. История — фактическая.

---

## Workflow

```text
Исходная задача
    ↓
Usage Planner
    ↓
Модель + reasoning + разбивка + оптимизированный prompt
    ↓
Запуск задачи
    ↓
Повторный /status
    ↓
Usage Recorder
    ↓
history/usage-log.md
    ↓
Более точные будущие оценки
```

Проект специально разделяет **прогноз** и **измерение**:

- `usage-planner.md` используется **до** запуска;
- `usage-recorder.md` используется **после** запуска.

---

## Быстрый старт

### 1. Подготовь задачу

Используй минимальный шаблон:

[`templates/task-input.ru.md`](templates/task-input.ru.md)

```text
STATUS:
[вставь /status]

REPOSITORY:
https://github.com/example/project

Branch:
main

TASK:
[вставь исходный prompt]

CONSTRAINTS:
[необязательно]
```

### 2. Оцени задачу до запуска

Открой:

[`prompts/planning/usage-planner.md`](prompts/planning/usage-planner.md)

Planner возвращает короткую оценку:

- размер задачи: `SMALL / MEDIUM / LARGE / HUGE`;
- риск для 5h;
- влияние на weekly;
- рекомендуемую модель;
- рекомендуемый reasoning;
- разбивку при необходимости;
- правило эскалации;
- готовый оптимизированный prompt;
- вердикт: `RUN / SPLIT FIRST / WAIT FOR RESET / REDUCE SCOPE`.

### 3. Выполни рекомендуемую итерацию

Запусти оптимизированный prompt в своём coding environment.

Если задача разбита, по возможности учитывай каждый запуск модели отдельно.

### 4. Запиши реальный расход

После выполнения снова получи `/status` и используй:

[`prompts/history/usage-recorder.md`](prompts/history/usage-recorder.md)

Передай:

```text
BEFORE:
[/status до]

AFTER:
[/status после]

REPOSITORY:
owner/repository

TASK:
короткое название задачи

MODEL:
название модели

REASONING:
уровень reasoning

RESULT:
Success / Partial / Failed
```

Recorder вычисляет фактический расход и добавляет строку в:

[`history/usage-log.md`](history/usage-log.md)

Если у него нет доступа на запись, он возвращает готовую Markdown-строку для ручной вставки.

---

## Пример результата Planner

```text
TASK: LARGE

SCOPE:
Inspect: ~10–20 files
Modify: ~4–8 files
Systems: Jenkins, auth, Docker, tests

5H:
🟡 RISK
Expected usage: HIGH
Remaining: 43%

WEEK:
🟡 NOTICEABLE
Expected usage: MEDIUM
Remaining: 61%

RECOMMENDED:
Model: Sol
Reasoning: Medium

PLAN:
1. Analyze Jenkins/auth scope → Sol / Medium
2. Implement job isolation → Sol / Medium
3. Add concurrency limits → Terra / Medium
4. Focused tests → Terra / Medium

ESCALATE:
Использовать более мощную модель только если focused investigation не позволяет безопасно определить архитектуру или root cause.

VERDICT:
⚠️ SPLIT FIRST
```

---

## Структура проекта

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

---

## Основные файлы

| Файл | Назначение |
|---|---|
| [`prompts/planning/usage-planner.md`](prompts/planning/usage-planner.md) | Предварительная оценка до запуска |
| [`prompts/history/usage-recorder.md`](prompts/history/usage-recorder.md) | Запись фактического расхода после запуска |
| [`templates/task-input.ru.md`](templates/task-input.ru.md) | Минимальный RU-шаблон задачи |
| [`history/usage-log.md`](history/usage-log.md) | Фактическая история измерений |
| [`AGENTS.md`](AGENTS.md) | Правила репозитория для coding agents |

Полный индекс промтов: [`prompts/README.ru.md`](prompts/README.ru.md).

---

## Как Planner оценивает задачу

Planner должен выбирать **самую дешёвую модель и reasoning, которых достаточно для надёжного выполнения задачи**.

Расход обычно увеличивают:

- исследование большого репозитория;
- большое количество затрагиваемых модулей;
- архитектурные изменения;
- неопределённый debugging;
- инфраструктура и CI/CD;
- повторные циклы `изменение → тест → исправление`;
- большой context;
- длинные автономные запуски.

Расход часто можно уменьшить:

- ограничив scope конкретными файлами и директориями;
- разделив анализ и реализацию;
- разбив большую задачу на независимые итерации;
- сначала запуская focused tests;
- запретив unrelated refactoring;
- добавив чёткие stopping conditions.

---

## Модели и reasoning

Названия доступных моделей могут меняться, поэтому Planner должен учитывать и конкретные модели, и их класс возможностей.

Общая логика:

| Класс | Типичное применение |
|---|---|
| Lightweight | Механические изменения, docs, повторяющиеся задачи |
| Standard | Понятная реализация и focused tests |
| Strong | Основная разработка, refactoring и debugging |
| Advanced | Сложная архитектура и root-cause analysis |

Самая мощная модель обычно должна быть **эскалацией**, а не вариантом по умолчанию.

То же относится к reasoning:

```text
Low / Light → Medium → High
```

Большую, но понятную задачу часто выгоднее выполнить несколькими `Medium`-итерациями, чем одним `High`-запуском.

---

## Фактическая история

`history/usage-log.md` хранит измерения, а не прогнозы.

Правила:

- один запуск модели = одна строка;
- не придумывать отсутствующие значения;
- не менять старые измерения ради более красивых прошлых прогнозов;
- учитывать reset лимита и не считать отрицательный расход;
- использовать исторические диапазоны только после накопления достаточного количества похожих запусков.

Со временем это позволит делать рекомендации под реальные репозитории и стиль работы конкретного пользователя.

---

## Текущий статус

### v0.1 — Prompt workflow

- [x] Оценка сложности задачи
- [x] Оценка риска для 5h
- [x] Оценка влияния на weekly
- [x] Рекомендация модели
- [x] Рекомендация reasoning
- [x] Декомпозиция задачи
- [x] Оптимизация prompt
- [x] Разбор raw `/status` внутри промтов
- [x] Post-run usage recorder
- [x] Фактическая история расхода
- [x] EN / RU шаблоны задачи
- [ ] Автоматическое получение `/status`
- [ ] Автоматический repository scope scanner
- [ ] Прогноз на основе накопленной истории
- [ ] CLI
- [ ] Интеграция VS Code / Codex
- [ ] Usage dashboard

---

## Важное ограничение

Codex Usage Planner **не является официальным калькулятором квоты OpenAI** и не может заранее знать точную стоимость будущего agent run только по тексту prompt.

Пока не накоплено достаточно реальной истории, проект специально использует качественные уровни риска (`LOW / MEDIUM / HIGH`) или явно обозначенные приблизительные диапазоны вместо ложной точности.

---

## Идея проекта

Цель не в том, чтобы избегать мощных моделей, а в том, чтобы тратить дорогой reasoning там, где он действительно даёт дополнительную ценность.

> Сначала спланируй. После — измерь. Улучшай прогнозы данными.
