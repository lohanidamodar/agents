# Flutter Multi-Agent Playbook

Use this companion file for medium to large Flutter projects where one coding
agent can orchestrate multiple focused agents or independent work streams.

This file is project-neutral. Copy it into a Flutter project, link to it from
`CLAUDE.md`, `AGENTS.md`, `GEMINI.md`, or another agent instruction file, and
delete any roles that do not fit the tooling available in that project.

---

## 1. Purpose

Large Flutter projects fail when one long-running agent session tries to hold
product requirements, architecture, UI details, platform behavior, tests, and
review context at the same time. Use this playbook to split work into small,
verifiable jobs with clear ownership.

The main agent acts as the orchestrator. Subagents are used for focused work:
read-only exploration, planning, implementation, testing, review, documentation,
and release preparation.

For small tasks, do not force this workflow. A single agent can inspect, edit,
verify, and summarize directly.

Research basis:

- Anthropic's Claude Code guidance recommends separating exploration, planning,
  implementation, and commit/review work, and using subagents to preserve main
  conversation context.
- OpenAI Codex guidance recommends subagents for read-heavy work such as
  exploration, triage, tests, and summarization, while treating parallel
  write-heavy workflows carefully because of merge conflicts and coordination
  overhead.
- Empirical work on agent instruction files shows that structured repository
  instructions can improve agent efficiency, but only when they are specific,
  current, and operational rather than vague.
- Flutter's own guidance emphasizes adaptive UI from one codebase and layered
  testing. In Flutter projects, that means every UI task needs layout proof and
  every business/data slice needs focused tests.

---

## 2. Operating Rules

The orchestrator is responsible for:

- Understanding the user request.
- Reading the main project instruction file first.
- Keeping scope, acceptance criteria, and constraints visible.
- Deciding which agents are needed.
- Giving each subagent a self-contained prompt.
- Preventing two agents from editing the same files at the same time.
- Verifying results before reporting completion.
- Preserving user changes already present in the working tree.

Every subagent prompt must include:

- Goal
- Relevant files or directories
- Scope boundaries
- Whether file edits are allowed
- Flutter conventions to follow
- Verification commands to run
- Expected response format

Subagents should not rely on hidden context from the orchestrator. If a detail
matters, paste it into the prompt.

---

## 3. Agent Roles

Use only the roles needed for the current task.

| Role | Edit Files | Best For | Returns |
| --- | --- | --- | --- |
| Orchestrator | Yes | Main coordination, scope control, final integration | Decisions, prompts, final summary |
| Product Analyst | No | Clarifying user workflows, acceptance criteria, edge cases | Requirements summary |
| Codebase Explorer | No | Finding existing patterns, dependencies, ownership boundaries | File references and findings |
| Flutter Architect | No | Feature boundaries, app structure, state/data flow | Implementation plan |
| UI Implementer | Yes | Screens, widgets, theming, responsive layout | UI diff and verification |
| State Implementer | Yes | Riverpod/BLoC/controllers, state models, async state | State diff and tests |
| Data Implementer | Yes | Repositories, APIs, persistence, migrations | Data diff and tests |
| Platform Implementer | Yes | Android/iOS/web/desktop config, permissions, native setup | Platform diff and build notes |
| Test Engineer | Yes | Unit, widget, integration, golden, regression tests | Test diff and results |
| Accessibility Reviewer | No | Semantics, keyboard use, tap targets, text scaling | Accessibility findings |
| Performance Reviewer | No | Rebuilds, list performance, async bottlenecks, jank risks | Performance findings |
| Security Reviewer | No | Secrets, auth, storage, API handling, release risk | Security findings |
| Code Reviewer | No | Bugs, regressions, missing tests, convention violations | PASS/FAIL with fix list |
| Documentation Keeper | Yes | README, setup docs, architecture notes, changelog | Documentation diff |
| Release Checker | Usually No | Store readiness, signing checklist, build verification | Release checklist and risks |

Do not invent a new role unless the task has a real responsibility that these
roles do not cover.

---

## 4. Standard Lifecycle

For medium or large feature work:

```txt
Product Analyst
  -> Codebase Explorer
  -> Flutter Architect
  -> Implementers
  -> Test Engineer
  -> Reviewers
  -> Documentation Keeper
  -> Orchestrator final verification
```

Recommended default for Flutter:

```txt
1. Orchestrator defines acceptance criteria.
2. Codebase Explorer maps existing patterns, files, tests, and constraints.
3. Flutter Architect slices work by ownership boundary.
4. Implementers execute one slice at a time unless file sets are disjoint.
5. Test Engineer adds or updates focused tests.
6. Accessibility / Performance / Security reviewers run in parallel after code
   is stable when those concerns matter.
7. Code Reviewer checks the final diff.
8. Orchestrator runs fresh verification and summarizes evidence.
```

For bug fixes:

```txt
Codebase Explorer
  -> Flutter Architect, if the fix is not obvious
  -> Implementer
  -> Test Engineer
  -> Code Reviewer
  -> Orchestrator final verification
```

For UI-heavy changes:

```txt
Codebase Explorer
  -> Flutter Architect
  -> UI Implementer
  -> Accessibility Reviewer
  -> Test Engineer
  -> Code Reviewer
```

For release readiness:

```txt
Release Checker
  -> Platform Implementer, only if fixes are needed
  -> Test Engineer
  -> Documentation Keeper
```

Use the full lifecycle when the task touches more than one layer, such as
`presentation + state + repository`, or when it affects platform configuration,
persistence, authentication, payments, notifications, or release behavior.

---

## 5. Parallelism Rules

Parallelize only when work is independent.

Safe to parallelize:

- Multiple read-only explorers investigating different areas.
- Accessibility, performance, and security review after implementation is done.
- Documentation updates that do not depend on still-changing code.
- Tests for independent modules when file ownership is clear.

Usually worth parallelizing in Flutter:

- Explorer A: current UI/routing pattern.
- Explorer B: state/data flow.
- Explorer C: existing test helpers and mocks.
- Reviewer A: accessibility and text scaling.
- Reviewer B: rebuild/performance risk.
- Reviewer C: test gaps and edge cases.

Do not parallelize:

- Two implementers touching the same file.
- Implementation before exploration when existing patterns are unknown.
- Review before implementation is finished.
- Test updates while the API under test is still changing.
- Platform config edits that may conflict in `pubspec.yaml`, Gradle, Xcode,
  entitlements, manifests, or CI files.

When in doubt, serialize the work.

Practical rule: parallel reads are cheap and useful; parallel writes are only
worth it when the orchestrator can name non-overlapping files before dispatch.

---

## 6. Work Slicing For Flutter Projects

Slice medium and large features by ownership boundary.

Good slices:

- Domain models and pure business rules
- Repository or service layer
- State management layer
- Screen shell and routing
- Individual widgets
- Responsive layout behavior
- Platform permissions/configuration
- Tests
- Documentation

Recommended order:

```txt
domain/models
  -> services/repositories
  -> state/controllers/providers/blocs
  -> routing/screen shell
  -> widgets
  -> platform config, if needed
  -> tests and docs
```

This order keeps lower layers stable before UI agents build against them.

Risky slices:

- "Build the whole feature"
- "Update all UI"
- "Refactor state"
- "Fix tests"
- "Clean architecture"

Each implementation slice should name the exact files or directories it may
touch. If the slice needs to expand, the implementer should stop and report why.

---

## 7. Flutter-Specific Constraints

Include relevant constraints in every implementation prompt:

- Follow the existing state management approach.
- Keep business logic out of `build()` methods.
- Use the project theme and design tokens.
- Keep responsive behavior centralized.
- Check important screens at compact and expanded sizes.
- Avoid adding dependencies unless explicitly approved.
- Keep data access out of widgets.
- Preserve localization conventions.
- Do not edit signing credentials, API keys, secrets, or production configs
  unless explicitly requested.
- Run `flutter analyze` and relevant tests after edits.

For UI work, also include:

- Identify the compact and expanded layouts before coding.
- Use `LayoutBuilder`, `MediaQuery`, or the project's centralized form-factor
  helper consistently.
- Check text scaling, small screens, wide screens, keyboard navigation, and
  empty/loading/error states.
- Prefer widget tests for adaptive layout decisions and user interactions.

If the project uses code generation, include the exact generation command. If it
does not, explicitly say not to introduce generated models, providers, or
adapters.

For data or platform work, also include:

- Do not touch signing files, API keys, secrets, or production identifiers
  unless the user explicitly requested it.
- Add migrations for persistent schema changes.
- Add repository/service tests for parsing, error handling, and edge cases.

---

## 8. Handoff Format

Every subagent should report in this format:

```txt
Result: PASS | FAIL | BLOCKED

Scope:
- <What was inspected or changed>

Files:
- <path>: <reason>

Verification:
- <command>: <result or not run with reason>

Findings:
- <Important findings, bugs, or trade-offs>

Next:
- <Concrete next step, if any>
```

For reviewers, use this stricter format:

```txt
Result: PASS | FAIL

Findings:
1. <severity> <path>:<line> <issue and impact>

Required fixes:
1. <concrete fix>

Residual risk:
- <anything not covered by review/tests>
```

---

## 9. Prompt Templates

### Product Analyst

```txt
You are the Product Analyst for a Flutter project.

Goal: <request>

Read the project instruction file and any provided requirements. Produce concise
acceptance criteria, important edge cases, and open questions. Do not edit files.

Return:
- User workflows
- Acceptance criteria
- Edge cases
- Open questions, only if needed
```

### Codebase Explorer

```txt
You are the Codebase Explorer for a Flutter project.

Goal: <what to investigate>
Start here: <files/directories>

Read only. Do not edit files. Find the existing architecture, patterns,
dependencies, and tests relevant to the goal. Cite file paths and line numbers
where possible.

Return under 250 words:
- Existing pattern
- Files likely involved
- Tests likely involved
- Risks or constraints
```

### Flutter Architect

```txt
You are the Flutter Architect for a medium/large Flutter project.

Goal: <feature or fix>
Requirements: <acceptance criteria>
Explorer findings: <paste findings>
Project constraints: <state management, routing, localization, codegen, testing>

Do not edit files. Produce an implementation plan with small slices. For each
slice, list files to create or modify, public APIs, state/data flow, responsive
layout behavior, and tests.

Return:
- Recommended approach
- Ordered implementation slices
- Files per slice
- Test plan
- Risks and trade-offs
```

### UI Implementer

```txt
You are the UI Implementer for a Flutter project.

Execute this slice only:
<slice>

Constraints:
- Follow existing widgets, theme, spacing, and naming.
- Keep business logic out of build methods.
- Support compact and expanded layouts where this UI is user-facing.
- Use existing localization and accessibility patterns.
- Do not add packages unless the plan explicitly says to.

After editing, run:
- flutter analyze
- relevant flutter tests

Stop and report if the planned files are insufficient.
Return the handoff format.
```

### State Implementer

```txt
You are the State Implementer for a Flutter project.

Execute this state-management slice only:
<slice>

Constraints:
- Use the existing state management approach.
- Keep APIs typed and intent-based.
- Represent loading, error, and empty states explicitly.
- Avoid exposing mutable collections.
- Add unit tests for important state transitions.

After editing, run:
- flutter analyze
- relevant flutter tests

Return the handoff format.
```

### Data Implementer

```txt
You are the Data Implementer for a Flutter project.

Execute this data/persistence/API slice only:
<slice>

Constraints:
- Keep data access out of widgets.
- Follow existing repository/service patterns.
- Handle errors explicitly.
- Preserve migration and serialization conventions.
- Do not touch secrets or production credentials.

After editing, run:
- flutter analyze
- relevant flutter tests

Return the handoff format.
```

### Test Engineer

```txt
You are the Test Engineer for a Flutter project.

Goal: add or update focused tests for:
<files/features>

Use the existing test style. Prefer unit tests for business logic and widget
tests for important UI behavior. For adaptive screens, test compact and expanded
sizes.

Run:
- flutter test <relevant tests>
- flutter analyze, if test changes affect imports or project structure

Return the handoff format.
```

### Code Reviewer

```txt
You are the Code Reviewer for a Flutter project.

Review the current diff for:
- Bugs and regressions
- Missing tests
- Architecture violations
- Responsive layout issues
- Accessibility issues
- Error/loading/empty-state gaps
- Unwanted dependency or config changes

Do not edit files. Report PASS only if there are no blocking findings.
Use the reviewer handoff format.
```

### Documentation Keeper

```txt
You are the Documentation Keeper for a Flutter project.

Update only documentation affected by this change:
<change summary>

Possible files:
- README.md
- project agent instructions
- setup docs
- architecture notes
- release notes

Do not document speculative behavior. Keep docs concise and current.
Return the handoff format.
```

### Release Checker

```txt
You are the Release Checker for a Flutter project.

Check release readiness for:
<Android / iOS / web / desktop>

Read only unless explicitly asked to fix issues. Check app identity, versioning,
permissions, icons, signing expectations, privacy requirements, build commands,
and store/testing readiness.

Do not print or modify secrets.
Return:
- Ready / Not ready
- Blocking issues
- Non-blocking warnings
- Verification commands run
```

---

## 10. Orchestrator Checklist

Before dispatching agents:

- [ ] Read the main project instructions.
- [ ] Check the working tree for existing changes.
- [ ] Identify the task type: feature, bug, refactor, UI, data, platform,
      release, documentation.
- [ ] Define acceptance criteria.
- [ ] Decide which roles are needed.
- [ ] Decide what can run in parallel.
- [ ] Prefer read-only parallel exploration before any implementation.

Before implementation:

- [ ] Explorer findings are available or deliberately skipped for a known area.
- [ ] The plan names files and tests.
- [ ] The plan states responsive layout expectations for UI work.
- [ ] The plan states code generation expectations.
- [ ] No two agents will edit the same file concurrently.
- [ ] Write-heavy work is serialized unless file ownership is explicitly
      disjoint.

Before reporting completion:

- [ ] Review subagent handoffs.
- [ ] Inspect the actual diff.
- [ ] Run or confirm fresh verification commands.
- [ ] Explain any command that could not be run.
- [ ] Summarize changed files and remaining risks.

Verification evidence to request from agents:

- Exact command run.
- Exit code or clear pass/fail output.
- Failing test names and error snippets when failures occur.
- Screenshots or visual inspection notes for substantial UI changes when the
  environment supports it.
- A note when a command was skipped and why.

---

## 11. Failure Handling

If an implementer changes files outside scope:

- Stop the lifecycle.
- Review whether the extra changes are necessary.
- Keep necessary changes only if they are clearly justified.
- Otherwise ask the user before reverting user-visible work.

If tests fail:

- Capture the exact command and failure.
- Send the failure to a Codebase Explorer or the original implementer.
- Do not report completion until the failure is fixed or explicitly accepted.

If the plan is wrong:

- Do not let implementers improvise.
- Return to the Flutter Architect with the new facts.

If reviewers disagree:

- Prefer concrete code evidence over opinion.
- Ask for a narrow follow-up review on the disputed files.
- Let the orchestrator make the final call and state the trade-off.

If the task grows:

- Split the new work into follow-up tickets.
- Finish or pause the current scoped change before starting the next one.

---

## 12. When Not To Use Multi-Agent Workflow

Use a single agent when:

- The change is a typo, small doc edit, or one-file fix.
- The affected area is already well understood.
- The task has no meaningful parallel work.
- The overhead would exceed the implementation effort.

Even in single-agent mode, keep the same standards: inspect first, edit narrowly,
run relevant verification, and summarize clearly.

---

## 13. Source Links

Use these sources to refresh this playbook when tooling changes:

- Claude Code subagents:
  <https://docs.anthropic.com/en/docs/claude-code/sub-agents>
- Claude Code best practices:
  <https://docs.anthropic.com/en/docs/claude-code/best-practices>
- OpenAI Codex subagents:
  <https://developers.openai.com/codex/concepts/subagents>
- Flutter adaptive and responsive design:
  <https://docs.flutter.dev/ui/adaptive-responsive>
- Flutter testing overview:
  <https://docs.flutter.dev/testing/overview>
- Agentic coding manifests empirical study:
  <https://arxiv.org/abs/2509.14744>
- AGENTS.md efficiency study:
  <https://arxiv.org/abs/2601.20404>
