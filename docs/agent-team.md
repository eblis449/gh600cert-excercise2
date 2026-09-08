# Agent team

To build Mona's Project Pulse dashboard, I am using a custom agent team defined
under `.github/agents/`, orchestrated with GitHub Copilot CLI running in a
Codespace.

## Orchestrator

- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Coordinates the Planner, Coder, and Designer agents. Breaks
  requests into phases, assigns non-overlapping file scopes, decides what can run
  in parallel vs. sequentially, and reports final outcomes. Does not implement
  work itself.
- **Definition:** `.github/agents/orchestrator.agent.md`

## Planner

- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Researches the codebase, docs, dependencies, and edge cases
  to produce an implementation plan (ordered steps, file assignments,
  dependencies, parallelizable work, and validation expectations). Does not
  write code.
- **Definition:** `.github/agents/planner.agent.md`

## Coder

- **Model:** GPT-5.5 (copilot)
- **Responsibility:** Implements code within the file scope assigned by the
  Orchestrator, including support files like `.vscode/launch.json` for the
  runnable Project Pulse app. Keeps behavior deterministic, testable, and
  validated before reporting completion.
- **Definition:** `.github/agents/coder.agent.md`

## Designer

- **Model:** Gemini 3.1 Pro (copilot)
- **Responsibility:** Owns UI/UX, accessibility, information architecture, and
  visual design for the dashboard, ensuring it uses polished project cards,
  status badges, and responsive layout with deterministic CSS hooks
  (`.dashboard`, `.project-card`).
- **Definition:** `.github/agents/designer.agent.md`

## Orchestration note

This session uses GitHub Copilot CLI in a Codespace as the orchestration
surface, invoking these custom agents to plan, build, and style Mona's Project
Pulse dashboard.
