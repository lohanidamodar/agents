# Flutter Agent Templates

Reusable instruction templates for coding agents working on Flutter projects.

This repository is meant to be copied from, not installed as a package. The
files here are neutral starting points for Claude, Codex, Antigravity, Gemini,
or any other coding agent that reads project-level Markdown instructions.

## Files

| File | Purpose |
| --- | --- |
| `flutter_template.md` | General Flutter project instructions covering workflow, architecture, UI, responsive layout, state, data, assets, tests, release checks, and review. |
| `flutter_multi_agent.md` | Expanded multi-agent workflow for medium to large Flutter projects, including roles, lifecycle, parallelism rules, handoff format, and prompt templates. |

## How To Use

For a new Flutter project:

1. Copy `flutter_template.md` into the project.
2. Rename it for the agent or tool you use, for example `CLAUDE.md`,
   `AGENTS.md`, or `GEMINI.md`.
3. Fill in the project-specific placeholders.
4. Delete sections that do not apply.
5. If the project is medium or large, also copy `flutter_multi_agent.md` and
   link to it from the main instruction file.

For an existing Flutter project:

1. Compare the template with the project conventions already in place.
2. Keep the project conventions where they are more specific.
3. Add only the guidance that helps agents work consistently.
4. Avoid turning the template into stale documentation. Update it when the
   project changes.

## Recommended Link Snippet

Add this to the main project instruction file when using the multi-agent
companion:

```md
For medium or large tasks, use `flutter_multi_agent.md` as the orchestration
playbook. Keep small tasks single-agent and focused.
```

## Notes

- These files intentionally avoid project-specific package names, features,
  credentials, local paths, and release policies.
- The templates assume agents should inspect first, edit narrowly, verify with
  Flutter commands where possible, and clearly report anything unverified.
- Release, signing, deployment, publishing, and credential changes should stay
  human-controlled unless explicitly requested.
