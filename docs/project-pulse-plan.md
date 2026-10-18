# Project Pulse dashboard implementation plan

## Summary

Build Mona's lightweight, static Project Pulse dashboard for contributors. The page will let the team scan active projects, owners, status, recent activity, priority/risk, and a short contributor-friendly summary in a polished, responsive card layout. Use plain HTML, CSS, and JSON; the repository has no existing app, framework, package manifest, or app test suite, so no build or third-party dependencies are needed.

The implementation must follow `.github/project-pulse-brief.md`: create the three `app/` files and a VS Code launch configuration named **Run Project Pulse Dashboard** that serves from `app/` and opens `index.html`, not a directory listing. Use the custom agents in `.github/agents/` through the Orchestrator, rather than treating implementation as one undifferentiated task.

## Agent responsibilities

- **Orchestrator** (`.github/agents/orchestrator.agent.md`): coordinate the phases below, delegate using the file boundaries, keep the shared data/markup contract consistent, integrate the results, and report validation and any remaining limitations. It does not implement the dashboard itself.
- **Planner** (`.github/agents/planner.agent.md`): produce this plan from the brief and repository context. Planning is complete once the team accepts this document as the implementation contract.
- **Designer** (`.github/agents/designer.agent.md`): define information hierarchy, card layout, responsive behavior, typography, spacing, status/priority treatments, and accessible interaction/contrast choices. Own `app/styles.css`; coordinate selectors and responsive states with Coder before styling is finalized. Keep the required `.dashboard` and `.project-card` hooks.
- **Coder** (`.github/agents/coder.agent.md`): implement the data-driven page and runnable preview. Own `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. Match the Designer's agreed hooks and the data contract below; keep launch JSON strict (no comments), with `cwd` set to `${workspaceFolder}/app`.

## File assignments and implementation contract

| File | Owner | Scope and acceptance notes |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic page with the exact title **Project Pulse**, a clear page heading, a dashboard container, and project cards rendered from `project-data.json`. Link `styles.css`, load the JSON using a relative URL, and show project name, owner, status, recent activity, priority/risk, and contributor-friendly summary. Include understandable loading, fetch-error, and empty-project states. Use the agreed `.dashboard` and `.project-card` hooks. |
| `app/styles.css` | Designer | Polished, responsive card-based visual design with readable spacing, visible status badges, clear priority treatment, rounded corners, and shadows. Include `.dashboard` and `.project-card`. Maintain contrast, visible keyboard focus, and status/priority distinctions that do not rely on color alone. |
| `app/project-data.json` | Coder | Valid JSON with a top-level `projects` array. Each sample project includes `name`, `owner`, `status`, `recentActivity`, and `priority`, as required by the brief; also include `summary` so the requested contributor-friendly summary is represented in the data rather than hard-coded in the page. Keep sample content concise and internally consistent. |
| `.vscode/launch.json` | Coder | Strict JSON configuration named **Run Project Pulse Dashboard**. Run `python3 -m http.server 5500` with `cwd` `${workspaceFolder}/app`; use `serverReadyAction` to open `http://localhost:%s/index.html`. This serves the app directory and targets the page explicitly, avoiding a directory listing. |

### Data and UI decisions

- Treat each project record as an object with the five brief-required fields plus `summary`. Use a small, representative set of sample projects; the brief does not provide actual team project records.
- Display values from the JSON rather than duplicating project content in HTML. For plain static hosting, use a relative `fetch("project-data.json")`; do not rely on opening the page via `file://`, since browsers commonly block fetches from local files.
- Render fetched strings as text, not injected HTML. Keep status and priority labels visible in the card even when their visual treatment uses color.
- Use the five required field names exactly, including camelCase `recentActivity`, so data and markup remain straightforward to validate.

## Ordered phases and dependencies

1. **Confirm the shared contract — Orchestrator, Designer, and Coder.** Review this plan and agree on card content, the `.dashboard`/`.project-card` hooks, the additional `summary` field, and responsive/accessibility requirements before editing. No implementation files are owned in this coordination step.
2. **Produce independent foundation files — Designer and Coder, in parallel after Phase 1.**
   - Designer creates `app/styles.css` using the agreed hooks.
   - Coder creates `app/project-data.json` and `.vscode/launch.json`. These two files do not depend on each other or on final page markup once the shared contract is fixed.
   - This parallelism is safe because file ownership is separate and the data schema/hooks are fixed first.
3. **Implement and integrate the page — Coder, sequential after Phase 2.** Create `app/index.html` against the delivered JSON schema and CSS hooks. This phase must wait for both foundation outputs so the page loads the actual data and uses selectors the Designer supplied. If design feedback changes the contract, Orchestrator resolves it before edits rather than allowing overlapping ownership.
4. **Validate and hand off — Orchestrator coordinates, Coder/Designer fix within their assignments.** Run structural checks, launch the app, inspect the rendered dashboard at desktop and narrow widths, and resolve defects. Do not stage, commit, or push as part of implementation; Git operations belong to the learner's Copilot CLI workflow.

The Designer and Coder may work in parallel only on the non-overlapping foundation files in Phase 2. The HTML integration and end-to-end validation are sequential because they depend on the final stylesheet, data, and launch configuration. No two agents should edit the same file.

## Validation expectations

### Static checks

- Confirm all four assigned implementation files exist and `app/index.html` links `styles.css` and loads `project-data.json`.
- Parse `app/project-data.json` and `.vscode/launch.json` as JSON; ensure `projects` is an array and every project has `name`, `owner`, `status`, `recentActivity`, `priority`, and `summary`.
- Check that cards visibly render the name, owner, status, recent activity, priority, and summary; confirm `.dashboard` and `.project-card` appear in the corresponding markup/styles.
- Confirm launch configuration name, command, working directory, and URL target: **Run Project Pulse Dashboard**, `python3 -m http.server 5500`, `${workspaceFolder}/app`, and `http://localhost:%s/index.html`. Keep the configuration strict JSON with no comments.

### Runtime and experience checks

- Start **Run Project Pulse Dashboard** in VS Code. Verify the server starts from `app/`, the browser opens `/index.html`, and the visible result is the dashboard rather than a directory listing.
- Verify project cards load from JSON over HTTP. Check that loading, fetch failure, and an empty `projects` array produce understandable messages rather than a blank page or uncaught failure.
- Inspect the page at desktop and narrow/mobile widths. Check readable hierarchy and spacing, no horizontal overflow, keyboard-visible focus, semantic landmarks/headings, adequate contrast, and text labels that preserve status/priority meaning without color.
- Stop the preview server after runtime validation. There is no repository-specific app test runner or build command; these checks are the appropriate lightweight validation for a static app.

## Risks, assumptions, and open questions

- The repository currently has no `app/` directory or dashboard implementation; the assigned files are new outputs, not edits to an existing frontend.
- Sample project names, owners, statuses, activity, and priorities are not specified in the brief. Coder should use clearly illustrative contributor-friendly sample data unless Mona supplies real records.
- `summary` is not among the five explicitly enumerated JSON fields, but the brief also requires a short contributor-friendly summary. Including it as an additional field reconciles both requirements.
- The launch configuration assumes the Codespace has `python3` and VS Code's `node-terminal` launch support available. Python is the requested server command; if VS Code cannot resolve the launch type in the target environment, verify its debugger support and adjust only the launch adapter while preserving the command, `app/` working directory, and explicit `/index.html` URL.
- Port `5500` may already be occupied. If the launch fails for that reason, stop the conflicting server or select one consistent alternate port and update the launch URL together.
