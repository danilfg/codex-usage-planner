# Changelog

All notable changes to this project will be documented in this file.

The format is based on Keep a Changelog, and this project follows Semantic Versioning where practical.

## [0.1.0] - 2026-09-17

### Added

- EN / RU project documentation.
- `prompts/planning/usage-planner.md` for preflight task and quota assessment.
- `prompts/history/usage-recorder.md` for recording actual before/after usage.
- Minimal EN / RU task input templates.
- Factual usage history in `history/usage-log.md`.
- Model and reasoning recommendations.
- 5-hour and weekly usage risk assessment.
- Task decomposition and optimized prompt generation.
- Recorder workflow with reset detection and one-run-per-row history tracking.
- Repository agent instructions in `AGENTS.md`.
- MIT license.

### Notes

This is the first prompt-based release of Codex Usage Planner.

The planner provides heuristic estimates before execution and uses actual recorded measurements after execution to improve future recommendations.
