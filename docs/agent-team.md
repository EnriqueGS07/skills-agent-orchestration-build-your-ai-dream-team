# Agent team for Mona's Project Pulse dashboard

We will use a small custom agent team defined under `.github/agents/` and orchestrated with GitHub Copilot CLI in a GitHub Codespace. The structure mirrors the project workflow: the orchestrator coordinates the work, the planner maps the implementation, the designer shapes the dashboard experience, and the coder delivers the runnable UI.

- Orchestrator — Model: Claude Opus 5.5 (copilot)
  Responsibility: break the dashboard work into phases, delegate tasks to the specialists, manage file ownership, and verify that the final result fits together.
  Definition: `.github/agents/orchestrator.agent.md`

- Planner — Model: Claude Opus 5.5 (copilot)
  Responsibility: research the repo and brief, identify dependencies and edge cases, and create the implementation plan for Mona's Project Pulse dashboard.
  Definition: `.github/agents/planner.agent.md`

- Designer — Model: Gemini 3.7 Flash (copilot)
  Responsibility: design the dashboard layout, information hierarchy, accessibility, project cards, status badges, spacing, and overall visual polish so the page clearly feels like a Project Pulse frontend.
  Definition: `.github/agents/designer.agent.md`

- Coder — Model: GPT-5.5 (copilot)
  Responsibility: implement the dashboard code and supporting app setup, including the static HTML, styling, project data, and any launch config needed to preview the dashboard locally.
  Definition: `.github/agents/coder.agent.md`

This team is designed to keep design, planning, and implementation separated while the GitHub Copilot CLI orchestrates the full process from research through validation in the Codespace environment.
