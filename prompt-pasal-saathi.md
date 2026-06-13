You are the lead Flutter engineer and multi-agent orchestrator for this workspace.

Build a complete, polished MVP called **Pasal Sathi** from the existing bare-bones Flutter project. Work autonomously from planning through implementation, testing, browser verification, documentation, and local Git commits.

Do not only create a plan. Execute the plan feature by feature until the MVP meets all acceptance criteria.

## Product

Pasal Sathi is a simple local-first shop-management app for small Nepali businesses.

The MVP helps a shopkeeper:

* Record sales
* Track cash, QR, and udhaaro payments
* Add and manage udhaaro entries
* Record daily expenses
* View a useful dashboard
* Keep data after refreshing or restarting the app

The primary demonstration target is Flutter Web in Chrome, but the code must remain compatible with Android.

## Multi-Agent Workflow

Act as the lead agent. Spawn and coordinate specialist agents where supported.

Use these roles:

1. **Product and UX agent**

   * Refine the requirements.
   * Define user flows and acceptance criteria.
   * Review usability and local-market relevance.

2. **Architecture agent**

   * Inspect the Flutter project.
   * Recommend a simple structure.
   * Review dependencies and architecture.
   * Prevent unnecessary complexity.

3. **Feature implementation agents**

   * Implement clearly separated features.
   * Work only on assigned, non-overlapping areas.
   * Report changed files and verification results.

4. **Testing and QA agent**

   * Add tests.
   * Run analyzer and test commands.
   * Test the app in Chrome.
   * Report bugs with reproduction steps.

5. **Review agent**

   * Review completed code for correctness, maintainability, accessibility, responsiveness, and missing edge cases.

Parallelize read-only work and independent features when safe. Do not allow multiple agents to modify the same files at the same time. Serialize changes to shared files such as `pubspec.yaml`, navigation, app setup, storage, and shared models.

The lead agent owns integration, final decisions, testing, and Git commits. If spawning agents is unavailable, perform the same roles sequentially.

## Working Rules

* Read any existing `AGENTS.md` and follow it.
* Inspect the current project before editing.
* Preserve useful existing code.
* Do not rewrite unrelated files.
* Do not upgrade Flutter or Dart.
* Use the installed stable Flutter SDK.
* Prefer simple, readable, idiomatic Flutter.
* Keep business logic outside widget `build()` methods.
* Keep widgets reasonably small.
* Do not add unnecessary abstractions or packages.
* Do not use a backend, authentication, cloud services, or network APIs.
* Do not add secrets or signing credentials.
* Do not push to a remote repository.
* Do not use destructive Git commands.
* Do not amend or rewrite existing user commits.
* Make reasonable decisions without stopping for confirmation unless genuinely blocked.
* Record assumptions and limitations in the documentation.

## Technology Choices

Use the following unless the existing project already has an appropriate alternative:

* Material 3
* Flutter SDK state management with `ChangeNotifier`, `ValueNotifier`, or another simple built-in approach
* `shared_preferences` with JSON serialization for local persistence
* `intl` only if useful for dates or currency
* Standard Flutter navigation
* Repository or service abstraction between storage and UI
* Feature-oriented project organization
* Null-safe Dart

Avoid adding Riverpod, Bloc, Provider, GetX, GoRouter, code generation, a database, or a dependency-injection framework for this MVP.

## Suggested Structure

Adapt this when appropriate rather than following it mechanically:

```text
lib/
  app/
    app.dart
    theme.dart
  core/
    storage/
    utils/
    widgets/
  features/
    dashboard/
    sales/
    credits/
    expenses/
  main.dart

test/
  unit/
  widgets/

docs/
  mvp_spec.md
  progress.md
  qa_report.md
  play_store_checklist.md
```

## Core Data

### Sale

A sale should contain:

* ID
* Item or description
* Quantity
* Unit price
* Total amount
* Payment method: cash, QR, or udhaaro
* Optional customer name
* Creation date and time

When payment method is udhaaro:

* Customer name is required.
* A corresponding unpaid credit entry must be created.
* The operation must not leave sale and credit data inconsistent.

### Credit Entry

An udhaaro entry should contain:

* ID
* Customer name
* Amount
* Optional note
* Created date
* Paid status
* Optional paid date
* Optional related sale ID

Support direct udhaaro entries as well as entries created from credit sales.

Partial payments are outside this MVP.

### Expense

An expense should contain:

* ID
* Title
* Category
* Amount
* Date
* Optional note

Use a small predefined category list such as stock purchase, transport, rent, utilities, and other.

## Screens and Features

### 1. Responsive App Shell

Create a responsive Material 3 shell with these destinations:

* Dashboard
* Sales
* Udhaaro
* Expenses

Use:

* `NavigationBar` on narrow screens
* `NavigationRail` or an appropriate wider layout on large screens

The app must work at common mobile and desktop browser widths.

### 2. Dashboard

Display:

* Total sales today
* Amount collected today through cash and QR
* Expenses today
* Outstanding udhaaro
* Net cash flow today: collected amount minus expenses
* Recent sales
* Recent udhaaro entries

Dashboard values must update immediately after data changes.

Avoid adding a chart dependency. Use clean summary cards and lists.

### 3. Sales

Implement:

* Sales list
* Add-sale form
* Item or description
* Quantity
* Unit price
* Calculated total
* Payment method selector
* Customer field when udhaaro is selected
* Form validation
* Empty state
* Useful success and error feedback

Support cash, QR, and udhaaro sales.

### 4. Udhaaro

Implement:

* Udhaaro list
* Add direct udhaaro form
* All, unpaid, and paid filters
* Customer search
* Outstanding total
* Mark-as-paid action with confirmation
* Paid date
* Clear paid/unpaid visual status
* Empty states

Do not implement partial payments.

### 5. Expenses

Implement:

* Expense list
* Add-expense form
* Category selection
* Amount and date
* Daily total
* Form validation
* Empty state

### 6. Persistence

Persist sales, credits, and expenses locally.

Requirements:

* Data survives browser refresh.
* JSON parsing failures are handled safely.
* Storage failures produce understandable errors.
* Repositories are testable without requiring widgets.
* Do not access local storage directly from screen widgets.

### 7. UI and Local Context

Use:

* App name: Pasal Sathi
* Currency label: `Rs.`
* The familiar term `Udhaaro`
* Clean Material 3 styling
* Accessible contrast
* Useful tooltips and semantic labels
* Proper loading, empty, success, and error states
* Confirmation before important destructive actions

Keep user-facing strings organized so localization can be added later, but do not implement full localization in this MVP.

## Explicit Non-Goals

Do not implement:

* Authentication
* Cloud synchronization
* Multiple shops
* Multiple users
* Inventory management
* Barcode scanning
* Payment gateway integration
* Actual QR payment processing
* Notifications
* Partial credit payments
* PDF invoices
* Advanced reports
* Analytics
* Full Nepali localization
* Play Store publishing or credential setup

Document these as possible future improvements.

## Execution Phases

Maintain a checklist in `docs/progress.md`. Update it after every phase.

### Phase 0: Inspect and Establish Baseline

1. Inspect the repository and existing files.
2. Check Git status without discarding existing work.
3. Run:

```bash
flutter --version
flutter doctor -v
flutter pub get
flutter analyze
flutter test
flutter devices
```

4. Confirm that Flutter Web exists.
5. If web support is missing, add only the required web platform files using an appropriate Flutter command without overwriting app code.
6. Create:

   * `docs/mvp_spec.md`
   * `docs/progress.md`
7. Record baseline problems separately from newly introduced problems.

Commit the completed planning and baseline work when the repository is in a valid state:

```text
docs: define Pasal Sathi MVP and implementation plan
```

### Phase 1: App Foundation

Implement:

* App entry point
* Material 3 theme
* Responsive app shell
* Navigation
* Shared formatting utilities
* Shared empty/error/loading widgets where genuinely useful
* Initial feature placeholders

Run formatting, analysis, tests, and a Chrome smoke test.

Commit:

```text
feat: establish responsive Pasal Sathi app foundation
```

### Phase 2: Models and Persistence

Implement:

* Sale model
* Credit-entry model
* Expense model
* JSON serialization
* Repository interfaces
* SharedPreferences-backed implementations
* Application state/controllers
* Unit tests for models and repositories

Commit only after tests pass:

```text
feat: add local data models and persistence
```

### Phase 3: Sales Feature

Implement the complete sales flow.

Test at minimum:

* Total calculation
* Form validation
* Cash sale
* QR sale
* Udhaaro sale
* Customer requirement for udhaaro
* Credit entry creation from udhaaro sale
* Persistence after reload

Run the app in Chrome and use the browser to complete the sales flows.

Commit:

```text
feat: add sales recording workflow
```

### Phase 4: Udhaaro Feature

Implement the complete udhaaro flow.

Test at minimum:

* Direct credit creation
* Unpaid filtering
* Paid filtering
* Customer search
* Marking an entry as paid
* Outstanding-total calculation
* Persistence after reload

Commit:

```text
feat: add udhaaro tracking and settlement
```

### Phase 5: Expenses Feature

Implement the complete expenses flow.

Test at minimum:

* Expense validation
* Category handling
* Daily-total calculation
* Persistence after reload

Commit:

```text
feat: add daily expense tracking
```

### Phase 6: Dashboard

Implement dashboard calculations and recent activity.

Test at minimum:

* Today sales total
* Cash and QR collected amount
* Today expenses
* Outstanding udhaaro
* Net cash flow
* Immediate updates after mutations
* Correct empty-state behavior

Commit:

```text
feat: add shop dashboard and daily summaries
```

### Phase 7: QA and Polish

Have the QA and review agents inspect the complete application.

Fix:

* Layout overflow
* Narrow-screen problems
* Wide-screen problems
* Missing validation
* Confusing labels
* Accessibility issues
* Unhandled errors
* Duplicate logic
* Analyzer warnings
* Flaky tests
* Poor empty states

Commit:

```text
fix: polish responsive UI and edge cases
```

### Phase 8: Documentation and Final Verification

Create or update:

* `README.md`
* `docs/qa_report.md`
* `docs/play_store_checklist.md`

README must include:

* Product summary
* Implemented features
* Architecture overview
* Package choices
* How to run on Flutter Web
* How to run tests
* How local persistence works
* Known limitations
* Future improvements

The Play Store checklist should explain preparation steps only. Do not create or modify signing credentials.

Run final verification:

```bash
dart format .
flutter analyze
flutter test
flutter test --platform chrome
flutter build web --release
```

If Chrome-platform tests are unavailable in the environment, document the reason and compensate with normal Flutter tests and browser smoke testing.

Optionally run an Android debug build when the Android toolchain is available:

```bash
flutter build apk --debug
```

Commit final documentation and verification:

```text
docs: complete MVP documentation and release checklist
```

## Browser Verification

Run the app in Chrome using the most reliable available approach, such as:

```bash
flutter run -d chrome
```

or a Flutter web server that the browser agent can open.

Use the browser agent to verify these scenarios:

1. Open the empty app.
2. Add a cash sale.
3. Add a QR sale.
4. Add an udhaaro sale with a customer.
5. Confirm the related udhaaro entry exists.
6. Add a direct udhaaro entry.
7. Filter unpaid and paid entries.
8. Mark an udhaaro entry as paid.
9. Add an expense.
10. Confirm dashboard totals.
11. Refresh the browser.
12. Confirm all data remains.
13. Check a narrow mobile-sized viewport.
14. Check a wide desktop-sized viewport.
15. Confirm there are no visible exceptions, overflow warnings, or broken controls.

Record results and any limitations in `docs/qa_report.md`.

## Testing Expectations

Add meaningful tests rather than testing implementation details.

Include:

* Model serialization tests
* Repository tests with controllable storage or suitable fakes
* Dashboard calculation tests
* Sales validation tests
* Udhaaro status tests
* Expense calculation tests
* Widget tests for important forms and interactions

Before every commit, run the relevant subset of tests.

Before the final commit, run the full verification suite.

Do not claim a test passed unless the command was actually executed successfully.

## Git Discipline

Use Git throughout the implementation.

Before changing files:

```bash
git status --short
```

Before committing:

```bash
dart format .
flutter analyze
flutter test
git diff --check
git status --short
```

Rules:

* Create small, coherent local commits after each completed phase.
* Only commit code that is formatted and passes relevant tests.
* Use conventional commit messages.
* Never commit secrets, generated build outputs, `.dart_tool`, IDE caches, or temporary files.
* Do not push.
* Do not force, reset, rebase, or delete existing user work.
* Do not bundle several unrelated features into one commit.
* The lead agent should perform commits after integrating specialist-agent work.
* End with a clean working tree unless an unavoidable limitation is documented.

## Definition of Done

The MVP is complete only when:

* The app launches successfully in Chrome.
* Dashboard, sales, udhaaro, and expenses are usable.
* Cash, QR, and udhaaro sales work.
* Udhaaro entries can be added and marked paid.
* Expenses can be added.
* Dashboard calculations are correct.
* Data survives browser refresh.
* Mobile and desktop layouts are usable.
* `flutter analyze` passes without errors.
* All relevant tests pass.
* `flutter build web --release` succeeds.
* Browser verification is completed.
* README and QA documentation are complete.
* Work is divided into clean local commits.
* Git working tree is clean.
* No secrets or credentials are introduced.

## Final Response

When all work is complete, provide:

1. A concise implementation summary
2. The architecture used
3. Features completed
4. Tests and commands executed with results
5. Browser scenarios verified
6. Git commits created
7. Known limitations
8. Recommended next production step

Begin by inspecting the project and organizing the specialist agents. Then execute every phase without waiting for additional approval unless the environment makes progress impossible.
